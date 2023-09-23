# Linux Commands

When you interract with the linux shell, you are typing commands for the shell to execute.
This document will focus on understanding what they are.

## Command Types

- Linux has several command types(e.g ‘alias’, ‘function’, ‘builtin’, ‘file’, ‘keyword’)
- We'll focus on shell running executable files and shell builtins.

## Running commands

- Most people try this well-known linux command:
```
ls
```
- This is not something that is part of the shell!
- Try the following:
```
type -a ls
which ls
```
- The bash [type](https://www.gnu.org/software/bash/manual/bash.html#index-type) builtin shell command can show you what is the type of the parameter you give it. The -a parameter asks for ALL available types.
- Here's what I saw in my linux system:
```
$ type -a ls
ls is aliased to `ls --color=auto'
ls is /usr/bin/ls
ls is /bin/ls
``` 
- So first, ls is an alias (so another name) for a more complicated version of the command (with some coloring parameters)
- Then, ls is just an executable file, located twice in these directories.  
Here's how to see the details of this file:
```
$ ls -l /usr/bin/ls
-rwxr-xr-x 1 root root 147176 Sep 24  2020 /usr/bin/ls
```
- So we see a file that has 147176 bytes in size, an executable file, a computer program.

EACH TIME YOU TYPE LS - YOU ARE RUNNING A PROGRAM !!!

## PATH

But how does linux know where to find the **ls** executable file?
- **The shell has a list of directories that it looks for program in.**
- This list is called [path](https://www.gnu.org/software/bash/manual/bash.html#index-PATH), and it is stored in a shell variable called PATH.  
(we will cover shell variable later)
- In order to see this list, use the following command:
```
echo $PATH
```
- Directories are seperated from each other by using a colob (*:*)
- We'll learn how to change the PATH variable when we learn about shell variables.

## Shell builtins

- What about the **cd** (*c*hange *d*irectory) command?  
Is it also a program, external to the shell?
- Try the **type** command with **cd**
- As you can see, the **cd** operation is part of the shell itself, not an external command.
- There are [many other shell builtins](https://www.gnu.org/software/bash/manual/bash.html#Bash-Builtins) (for example **type** is one of them)
