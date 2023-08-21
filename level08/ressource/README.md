# level08

- We log as 'level08'

- Two files are located inside the home directory of level08:
>`-rwsr-s---+ 1 flag08 level08 8617 Mar  5  2016 level08`
>`-rw-------  1 flag08 flag08    26 Mar  5  2016 token`

- The executable file has setuid & setgid bits permissions

- We can see that the executable will be executed as `flag08` if we execute it being `level08`

- Upon execution without arguments the executable prints:
>`./level08 [file to read]`

- Upon execution with `token` as argument the executable prints:
>`You may not access 'token'`

- We dissassemble it using gdb but the asm dump is bigger than the last one, let's use `ghidra` on kali to reverse this binary easily

- After analysing the binary this is the main function in C provided by `ghidra`:
```c
int main(int argc,char **argv,char **envp)
{
  char *pcVar1;
  int __fd;
  size_t __n;
  ssize_t sVar2;
  int in_GS_OFFSET;
  undefined4 *in_stack_00000008;
  int fd;
  int rc;
  char buf [1024];
  undefined local_414 [1024];
  int local_14;
  
  local_14 = *(int *)(in_GS_OFFSET + 0x14);
  if (argc == 1) {
    printf("%s [file to read]\n",*in_stack_00000008);
                    /* WARNING: Subroutine does not return */
    exit(1);
  }
  pcVar1 = strstr((char *)in_stack_00000008[1],"token");
  if (pcVar1 != (char *)0x0) {
    printf("You may not access \'%s\'\n",in_stack_00000008[1]);
                    /* WARNING: Subroutine does not return */
    exit(1);
  }
  __fd = open((char *)in_stack_00000008[1],0);
  if (__fd == -1) {
    err(1,"Unable to open %s",in_stack_00000008[1]);
  }
  __n = read(__fd,local_414,0x400);
  if (__n == 0xffffffff) {
    err(1,"Unable to read fd %d",__fd);
  }
  sVar2 = write(1,local_414,__n);
  if (local_14 != *(int *)(in_GS_OFFSET + 0x14)) {
                    /* WARNING: Subroutine does not return */
    __stack_chk_fail();
  }
  return sVar2;
}
```

- We can see that the binary check if the file passed as arguments contains an occurence of `token` if not, it open the file, read it and print it on stdout

- We can't rename `token` so let's create a symbolic link to `token` in `tmp`:
>`ln -s /home/user/level08/token /tmp/pwn08`
>`level08@SnowCrash:~$ ./level08 /tmp/pwn08`
>`quif5eloekouj29ke0vouxean`

- We can log to `flag08` with this password:
>`level08@SnowCrash:~$ su flag08`
>`Password: `
>`Don't forget to launch getflag !`
>`flag08@SnowCrash:~$ getflag`
>`Check flag.Here is your token : 25749xKZ8L7DkSCwJkT9dyv6f`

- We get the flag: `25749xKZ8L7DkSCwJkT9dyv6f`