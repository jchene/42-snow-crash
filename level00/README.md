# level00

- We found a suspicious line inside `/etc/passwd`:
    >`flag01:42hDRfypTqqnw:3001:3001::/home/flag/flag01:/bin/bash`

- This `42hDRfypTqqnw` was clearly a hashed password for the user flag01, we need to break it

- I know i can use `John the Ripper` to bruteforce a hashed password

- Maybe `john` is already installed on the machine: I try the command `john`

- The terminal responds: `-bash: /usr/sbin/john: Permission denied`

- It means there something called `john` inside one of the $PATH directories

- We find a suspicious file (not an executable) `/usr/sbin/john`: 
    >`----r--r--  1 flag00  flag00      15 Mar  5  2016 john`

- Inside this file there is `cdiiddwpgswtgt`

- Viewing this pair of double letters, it was looking like Cesar's cipher

- When trying with an offset of 15 to the right in Cesar's cipher it becomes `nottoohardhere`

- Password works for logging to flag00

- The flag to connect to level01 is `x24ti5gi3x0ol2eh4esiuxias`