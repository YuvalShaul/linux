# Linux Programs

A **"real"** linux program is an executable file of the type ELF.

## Looking at programs
You can learn about programs by finding one in /usr/bin (for example).  
Then inspect it.
Example:
- **ls -l /usr/bin/date**  
(which will show you the program file)
- **file /usr/bin/date**  
(which will inspect the file type)
- **which date**  
(which will make sure that this is what runs when you type **date**)
- **date**  
(to actually run the program)

## Python programs

- The **myprog.py** in this directory is just a text file.
- If you have python installed, you could run it like this:  
**python myprog.py**  
(which should print a short message)
- This is what a really happens when a python program runs.
- You can find where that **python** program is really at, by typing **which python**  
(**/usr/bin/python** in my case)
- ..but you can run it in a different way, like this:  
**./myprog.py**  
This the **bash** that we want to try to **execute** the file as if it was a real program
- If you try that, you will probably get a permission error.  
Change the file permission like this:  
**chmod u+x myprog.py**  
and try to run it again.
- It now looks like the file itself is running, but this is not true.  
It is still the python interpreter running the script file.
