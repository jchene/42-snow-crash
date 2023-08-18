# level01

- In level00, we found a password inside `/etc/passwd`:
    >`flag01:42hDRfypTqqnw:3001:3001::/home/flag/flag01:/bin/bash`

- This `42hDRfypTqqnw` was clearly a hashed password for the user flag01, we need to break it

- I install a new Kali VM because `john` is preinstalled on Kali

- I connect to snow-crash through kali using ssh and download the `/etc/passwd` file.

- I run it through `john` using:
    >`john ./passwd`

- `john` finds the password:
    >`abcdefg          (flag01)`

- Password works, we log to flag01

- The flag to connect to level02 is `f2av5il02puano7naaf6adaaf`