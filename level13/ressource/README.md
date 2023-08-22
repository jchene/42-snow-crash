# level13

- We log as 'level13'

- A single file is located inside the home directory of level13:
>`-rwsr-sr-x 1 flag13  level13 7303 Aug 30  2015 level13`

- The executable file has setuid & setgid bits permissions

- Let's dissassemble it, here is the asm dump of the main function:
```asm
Dump of assembler code for function main:
   0x0804858c <+0>:     push   %ebp
   0x0804858d <+1>:     mov    %esp,%ebp
   0x0804858f <+3>:     and    $0xfffffff0,%esp
   0x08048592 <+6>:     sub    $0x10,%esp
   0x08048595 <+9>:     call   0x8048380 <getuid@plt>
   0x0804859a <+14>:    cmp    $0x1092,%eax
   0x0804859f <+19>:    je     0x80485cb <main+63>
   0x080485a1 <+21>:    call   0x8048380 <getuid@plt>
   0x080485a6 <+26>:    mov    $0x80486c8,%edx
   0x080485ab <+31>:    movl   $0x1092,0x8(%esp)
   0x080485b3 <+39>:    mov    %eax,0x4(%esp)
   0x080485b7 <+43>:    mov    %edx,(%esp)
   0x080485ba <+46>:    call   0x8048360 <printf@plt>
   0x080485bf <+51>:    movl   $0x1,(%esp)
   0x080485c6 <+58>:    call   0x80483a0 <exit@plt>
   0x080485cb <+63>:    movl   $0x80486ef,(%esp)
   0x080485d2 <+70>:    call   0x8048474 <ft_des>
   0x080485d7 <+75>:    mov    $0x8048709,%edx
   0x080485dc <+80>:    mov    %eax,0x4(%esp)
   0x080485e0 <+84>:    mov    %edx,(%esp)
   0x080485e3 <+87>:    call   0x8048360 <printf@plt>
   0x080485e8 <+92>:    leave  
   0x080485e9 <+93>:    ret    
End of assembler dump.
```

- We can see that the binary gets the user id with `getuid` and compares it to `0x1092` (4242), if it is not equal, it will print an error and exit, otherwise it will call `ft_des` and print the result

- Let's set a breakpoint after the call to `getuid`
>`0x0804859a <+14>:    cmp    $0x1092,%eax`
>`(gdb)  b*0x0804859a`

- Then we modify the return value of `getuid` stored in `$eax`
>`(gdb)  set $eax=4242`

- And we can step:
>`(gdb) step`
>`Single stepping until exit from function main,`
>`which has no line number information.`
>`your token is 2A31L79asukciNyi8uppkEuSx`

- We get the flag: `2A31L79asukciNyi8uppkEuSx`