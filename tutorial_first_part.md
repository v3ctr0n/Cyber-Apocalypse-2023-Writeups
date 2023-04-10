As in many other RE challenges, we are given an executable to analyze, which in this case is tied to a server application.
Key to solving this challenge was to analyze the given file _licence_.

The first step is always gonna be a file check.
```
file license
```
and thus we find out that we are dealing with a 64-bit linux executable
```
license: ELF 64-bit LSB executable, x86-64, version 1 (SYSV), dynamically linked, 
interpreter /lib64/ld-linux-x86-64.so.2, BuildID[sha1]=5be88c3ed329c1570ab807b55c1875d429a581a7, 
for GNU/Linux 3.2.0, not stripped
```
After running the file by ./license we are asked for three passwords, which we can find by simple RE analysis...now let me put this bluntly...ltrace is your friend.
All three passwords can be found by using ltrace, but it is possible to also use the command 'strings' for the first two.

After running strings or ltrace we find out the first password: _PasswordNumeroUno_
The second password is also accesible in plaintext by running strings in this form: _0wTdr0wss4P_, which as the file itself is telling us is actually backwards.
Now you can reverse it by simple python script or just by looking at it, it's fairly easy, or you can run ltrace, which will already give you the right password after 
putting in the first password succesfully.

```
strcmp("PasswordNumeroUno", "PasswordNumeroUno")                           = 0
free(0xc8b8d0)                                                             = <void>
readline("Getting harder - what's the seco"...Getting harder - what's the second password? s
)                            = "s"
strcmp("s", "P4ssw0rdTw0")                                                 = 35
puts("You've got it all backwards..."You've got it all backwards...
)                                     = 31
exit(-1 <no return ...>
```
Strings won't help you much with the third password (explained in the second part), but ltrace has your back once again. Once you put in the previous two passwords after running the file, ltrace
graciously provides you with the third one.
```
strcmp("P4ssw0rdTw0", "P4ssw0rdTw0")                                       = 0
free(0x1c7a760)                                                            = <void>
readline("Your final test - give me the th"...Your final test - give me the third, and most protected, password: y
)                            = "y"
strcmp("y", "ThirdAndFinal!!!")                                            = 37
puts("Failed at the final hurdle!"Failed at the final hurdle!
)                                        = 28
exit(-1 <no return ...>
```
