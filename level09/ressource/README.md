# level09

- We log as 'level09'

- Two files are located inside the home directory of level09:
>`-rwsr-sr-x 1 flag09  level09 7640 Mar  5  2016 level09`
>`----r--r-- 1 flag09  level09   26 Mar  5  2016 token`

- The executable file has setuid & setgid bits permissions

- We can see that the executable will be executed as `flag09` if we execute it being `level09`

- Upon execution without arguments the executable prints:
>`You need to provied only one arg.`

- Upon execution with `aaaaa` as argument the executable prints:
>`abcde`

- We can see this is a simple Cesar's cipher with position as offset for each character

- We `cat` `token`:
>`f4kmm6p|=�p�n��DB�Du{��`

- There is some non displayable characters let's see them with `cat -e`
>`f4kmm6p|=M-^B^?pM-^BnM-^CM-^BDBM-^CDu{^?M-^LM-^I$`

- Let's do the substitution for each character of the token:
```
ASCII	NAME	DEC		POS		RES		RES?	ASCII
f				102		-0		102		102		f
4				52		-1		51		51		3
k				107		-2		105		105		i
m				109		-3		106		106		j
m				109		-4		105		105		i
6				54		-5		49		49		1
p				112		-6		106		106		j
|				124		-7		117		117		u
=				61		-8		53		53		5
M-^B	(STX)	2		-9		-7		121		y
^?		(DEL)	127		-10		117		117		u
p				112		-11		101		101		e
M-^B	(STX)	2		-12		-10		118		v
n				110		-13		97		97		a
M-^C	(ETX)	3		-14		-11		117		u
M-^B	(STX)	2		-15		-13		115		s
D				68		-16		52		52		4
B				66		-17		49		49		1
M-^C	(ETX)	3		-18		-15		113		q
D				68		-19		49		49		1
u				117		-20		97		97		a
{				123		-21		102		102		f
^?		(DEL)	127		-22		105		105		i
M-^L	(FF)	12		-23		-11		117		u
M-^I	(TAB)	9		-24		-15		113		q
result:		f3iji1ju5yuevaus41q1afiuq
```

- The result is the password for `flag09`
>`level09@SnowCrash:~$ su flag09`
>`Password: `
>`Don't forget to launch getflag !`
>`flag09@SnowCrash:~$ getflag`
>`Check flag.Here is your token : s5cAJpM8ev6XHw998pRWG728z`

- We get the flag: `s5cAJpM8ev6XHw998pRWG728z`