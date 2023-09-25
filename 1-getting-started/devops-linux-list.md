# The DevOps Linux list

The following list represents my (changing) views on the Linux knowledge requirements for a DevOps engineer.  
This will be an ever growing list, so you may want to come back here from time to time.

## Basics

- What is linux, how is it different from UNIX, some history
- Running your own linux using your favorite virtualizaion
- Basic installation of linux (virtualizaion systen or (hardware))
- The main idea of how an operating system works:
  - the kernel
  - processes
  - system calls

## Directories and Files

- files
  - listing file in another directory (not the current directory)
  - listing all files (including hidden files)\
  - long listing (ls -l)
  - creating new files (using **touch** or an editor)
- Directories
    - showing the current directory, moving from a  directory to a directory (setting the current directory)
    - Creating new directories
    - understanding . and ..
    - understanding the well-known linux/UNIX directories:  
    /:/usr/:/usr/bin:/bin:/lib/:/etc:/tmp:/home   and others


## Processes and programs

- Understanding the idea of an executable file (compiling basic C program to an executable)
- Understanding dynamic libraries (so files) how programs depend on them
- Understanding processes as running programs:
  - list processes (current processes and all processes)
  - understand process IDs
  - how processes are born (fork and exec system calls)
- Understanding the theoretical idea behind the processes:
  - independant memory address space for each process
  - time sharing among processes (including explanation of using multiple cpu/core/thread)
  - REDO: how a process interracts with OS to do things (system-calls revisited)

## Linux Editors

- text-based editors;
  - vi (and is successor vim) is the basic
    - why vi is so important
    - move freely from command mode to edit mode (pen-down, pen-up)
    - save, quit-and-save
    - discard changes so you can quit
    - delete characters and lines
    - copy and paste
    - go to places (line numbers, end-of-file etc.)
  - nano
    - ??
- graphical editors:
  - Visual Studio Code

## bash 


## linux networking

- Basics of showing info
  - ifconfig (..which is obsolete, but you should know it)
  - ip commands (ip addr show, ip link ...)
- linux networking is a mess:
  - get to know network-manager (nmcli...)
  - get to know networkd
  - get to know 