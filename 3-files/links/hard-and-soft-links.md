# Hard Links

## About hard links

- A hard link is a directory entry that associates a name with a file.
- each file must have at least one hard link.
- Creating additional hard links for a file makes another entry pointing to the same file.
- A soft link is **ANOTHER DIFFERENT FILE**. 
The contet of this file also poins to the original file.

## Create hard link

- Create a new file in the current directory called **filea**:  
**touch filea**
- Create a hard link to this file, with the name **fileaa**:
**ln filea fileaa**
- There's a good way to see where your directory entry points to:  
**ls -li**  
(make sure you notice that **filea** and **fileaa** refer to the same i-node, same file)

## Create soft link

- Create a soft link that will point to **filea**:  
**ln -s filea filesa**
- Use **ls -il** again, to see what entry points where:
- Note that the new soft-link **filesa** does not point to **filea**
- Instead, it points to another file, that **IS** the soft link.  
The content of this file is the actual point to **filea**
- It is always safe to delete the soft-link, it does not delete the file that it points to.
- Delete the soft-link:  
**rm filesa**

## Delete hard-link

- Use **ls -il** to look at your files again.
- Note that there is a **2** in the 2nd field of the command.  
This is the number of the hard links pointing to this file.
- Delete **filea**:  
**rm filea**
- Use **ls -il** to see the results:  
Note that **fileaa** was not deleted, but the number of links was decremented by 1
- Now remove **fileaa**:  
**rm fileaa**  
(the file is removed)