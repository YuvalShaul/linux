#### Infrastructure

- Run your copy of linux.
- If this is a GUI version, search and run a terminal

#### Commands:
- **clear** to clear the screen
- **whoami** to find the current logged in user
- **uptime** Tells you how long the computer has been running without a reboot.
- **pwd** (print working directory) to find out current dir
- **cd <dir name>** (change directory) to change to a new directory
- **type** to find out the type of a command
- **ls** to list files
- **ls -l** for long listing
- **ls <dir>** to list files in a directory
- **ls -d <dir>** to list the directory entry itself
- **echo <string or variable>** to show something on the terminal
- For globbing (also called wildcards):
  - '*' stands for 0 or more (ant) characters
  - '?' stands for a exactly one character
  - [] stands for exatly one character in the brackets.
    - [a-e] stands for a single letter in the range a-e
    - [a-e79,] stands for a single letter in range a-e and also '7', '9', and comma ','
- **cat <file name>** displays the content of a file
- **touch** update creation time of a file, or create it if it is not there
- **cp** to copy a file
- **mv** to move or rename a file

#### Try out commands

1 list all files in the root directory
    Which one is the largest file?

2 Inspect the "echo" command.
    What type command is it?

3 Find a file which is not a directory in /lib directory, and find the file type.

4 List all the files with name ending with "log" in the /var/log directory

5 Display the content of /var/log/dpkg.log

6 Create a "try" directory under your home directory.
    Inside, create a file called "file1.f.g"
    Rename this file to "file1.g.f"
    Now, rename it to myfile, and move it into your home directory (one command)

7  Copy the "ls" command to your home directory.
     Rename it to "good-ls"
     Run your new command

