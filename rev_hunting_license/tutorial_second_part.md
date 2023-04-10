The flag itself isn't hidden in the license file, instead it was accesible only after solving questions about the given executable. The server hosting this questionnaire was available only during the CTF.

The first three questions were easy to answer by simple file checks.

```
What is the file format of the executable?
> ELF
[+] Correct!

What is the CPU architecture of the executable?
> x86-64
[+] Correct!

What library is used to read lines for user answers? (`ldd` may help)
> libreadline.so.8
[+] Correct!
```
Solving the other two questions required a decompiler like radare2/rizin or gdb.
```
What is the address of the `main` function?
> 0x401172
[+] Correct!

How many calls to `puts` are there in `main`? (using a decompiler may help)
> 5
[+] Correct!
```
For instance, in radare2/rizin command 'afl' displays all functions and their addresses including the main function. You can then easily disassemble the function
via 'pdf' and see all the 'puts' calls...alternatively use gdb with peda (or something else) to dissasemble main (disas main).

The rest of the questions is related to the passwords found in the license executable (see tutorial_first_part). 
```
What is the first password?
> PasswordNumeroUno
[+] Correct!

What is the reversed form of the second password?
> 0wTdr0wss4P
[+] Correct!

What is the real second password?
> P4ssw0rdTw0
[+] Correct!

What is the XOR key used to encode the third password?
> 0x13

What is the third password?
> ThirdAndFinal!!!
[+] Correct!

```
All three passwords can be found with ltrace easily. It seems the third password has undergone a XOR encryption, and we need to find the XOR key, which
ltrace didn't provide us with. For that question to be answered we need to head back to gdb (or your preffered decompiler/debugger).

```
gdb ./license
```
To extract the xor key we need to set a breakpoint at the instruction where the key is used to perform the XOR operation. After that, we need to examine
the values of the registers or memory locations that hold the key.

```
break *0x00401272
```
After setting a breakpoint at the location where the xor key is used (don't confuse it with the xor function itself), we run the executable with 'run'.

```
[-------------------------------------code-------------------------------------]
   0x401268 <xor+49>:   movsxd rcx,edx
   0x40126b <xor+52>:   mov    rdx,QWORD PTR [rbp-0x18]
   0x40126f <xor+56>:   add    rdx,rcx
=> 0x401272 <xor+59>:   xor    al,BYTE PTR [rbp-0x2c]
   0x401275 <xor+62>:   mov    BYTE PTR [rdx],al
   0x401277 <xor+64>:   add    DWORD PTR [rbp-0x4],0x1
   0x40127b <xor+68>:   mov    eax,DWORD PTR [rbp-0x4]
   0x40127e <xor+71>:   cdqe
[------------------------------------stack-------------------------------------]

```
We can see the al register is being XORed with the byte value stored in the memory location [rbp-0x2c] using the xor al, 
BYTE PTR [rbp-0x2c] instruction. To examine the memory location we use command x/1xb. This will display the key in a hexadecimal format. The 1 in the command specifies the number of bytes to display,
the b specifies the format of the output as a single byte.

```
gdb-peda$ x/1xb $rbp-0x2c
0x7fffffffdb34: 0x13

```
And with that we have our xor key. The third password we already got by using ltrace, and considering that was the last question, we get the flag: 

```
[+] Here is the flag: `HTB{l1c3ns3_4cquir3d-hunt1ng_t1m3!}

```
