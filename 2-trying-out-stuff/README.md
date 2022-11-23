# Linux Commands

The following are experiments you can run yourself using a Bash terminal on Linux. 

## Command Types

Find the type (buitin/external command/alias/function) of the following commands.  
Copy your bash session, and deliver as a file.

- cd
- ls
- ps
- set
- echo
- printf
- man
- mkdir
- rm
- ping
- kill
- vi
- grep

## Running things

- Use **ps** to see what's running:
  - **ps -e**  (to see all processes)
  - **ps**     (to see only processes starting with your current shell)
- Try **which bash** to see where is bash  
Do **ls -l** on this, to make sure can see the binary
- Try **ldd** on several executables, to see if they are dynamically linkable, and what is it that they need in order to run:  
  - **ldd \<path to\>/ps**
  - **ldd \<path to\>/ls**
  - **ldd \<path to\>/bash**
  - **ldd \<path to\>/python**
- What does **cd** needs in order to run?

## PATH

Find you way with the following instructions.  
Use the Internet to find commands you need.
- copy the **ls** program into this current directory  
(where is it?)
- rename it to **myls**, and use it.  
(how can you run it?)
- add the current directory to the path, so you can run it more simply. 