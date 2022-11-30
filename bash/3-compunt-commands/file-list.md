# File List - bash exercise

In this exercise you'll create a script that list files in the current directory.

- LIst all files in a directory.
- The script accepts a single parameters, with the path of the directory to list.
- If there is no parameter, use the current directory.
- Create an array with the file names.  
(this is not really necessary, just to make sure you can)
- Iterate over all files
  - If the file type is a directory, write "directory:"
  - If this is a normal file, write "file:"
- You should get a listing somehow like this:  
file: file1.txt  
file: file2.txt  
directory: dir1  
file: file3.txt