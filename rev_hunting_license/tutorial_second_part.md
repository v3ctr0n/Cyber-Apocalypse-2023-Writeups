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
ltrace didn't provide us with.
