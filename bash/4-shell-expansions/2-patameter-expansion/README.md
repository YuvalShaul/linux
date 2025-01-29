# Shell Parameter Expansion

## Quoting within parameter expansion

Try this:  
- **touch a.txt b.txt**  
(crete those two files)
- **files="a.txt b.txt"**  
(setting the variable)
- **ls -l $files**  
(this should work, I want word splitting)
- **ls -l "$files"**  
(should not work without word splitting)
- Try both options with **shpar**

## Basic parameter expansion

Make sure that the **shpar** program is available from all directories.  

Try this:  
- Basic parameter expansion:  
**myvar=abc123**  
**shpar "$myvar"**
- With braces (not needed here):  
**shpar "${myvar}"**

## Parameter expansion with braces

- Try this:  
```
var1=abc
var2=def
shpar "$var1_$var2"
```
- NOW TRY THIS:  
```
shpar "${var1}_$var2"
```

## Setting a default

- Set a default if not set or empty:  
```
abc=
echo "${abc:-mydef}"
```
- Set a default if set:  
```
abc=123
echo "${abc:-mydef}"
```
- Set a default ONLY if not set:  
```
abc=
echo "${abc:-mydef}"
```  
..now unset and try again:  
```
unset abc
echo "${abc:-mydef}"
```

## Setting an alternate

First, unset:  
```
unset myvar
echo "${myvar:+alternate}"
```  
..now set to empty and see:  
```
myvar=
echo "${myvar:+alternate}"
```  
...now set to some non-empty, try again:  
```
myvar=hello
echo "${myvar:+alternate}"
```

## Strings

${parameter:offset}
${parameter:offset:length}
Examples:
```
$ str1=abcdefghijklmnopqrstuvwxyz
$ echo ${str1:10}
klmnopqrstuvwxyz
$ echo ${str1:10:4}
klmn
$ 
```

