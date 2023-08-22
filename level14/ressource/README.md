# level14

- We log as 'level14'

- I did not find any file related to `level14` or `flag14` in the system so let's reverse the getflag command itself.

- The command is located inside `/bin/getflag`

- The assembler dump is huge let's use `ghidra` instead to understand how it works

- The C traduction of the main function done by `ghidra` is in the `level14.c` file

- We can see that the binary contains a weak reverse "protection" using `ptrace` to see if some other program is tracing it
```c
lVar2 = ptrace(PTRACE_TRACEME,0,1,0);
  if (lVar2 < 0) {
    pcVar7 = "You should not reverse this";
    puts("You should not reverse this");
    uVar3 = 1;
  }
```

- We can see that the binary checks the user id with `getuid` and then give the correct token accordingly
>`_Var5 = getuid();`

- Let's use gdb to bypass this protection and modify the uid:
>`0x08048989 <+67>:    call   0x8048540 <ptrace@plt>`
>`0x0804898e <+72>:    test   %eax,%eax`

>`(gdb) b*0x0804898e`
>`(gdb) set $eax=0`

>`0x08048afd <+439>:   call   0x80484b0 <getuid@plt>`
>`0x08048b02 <+444>:   mov    %eax,0x18(%esp)`

>`(gdb) b*0x08048b02`
>`(gdb) set $eax=3014`
>`step`

>`Single stepping until exit from function main,`
>`which has no line number information.`
>`Check flag.Here is your token : 7QiHafiNa3HVozsaXkawuYrTstxbpABHD8CPnHJ`

- We get the flag: `7QiHafiNa3HVozsaXkawuYrTstxbpABHD8CPnHJ`