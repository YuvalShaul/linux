# Remove Passwords lines

Write a bash script that does the following:  

- It iterates over all files in a directory specified as parameter #1
- It scans each file, and looks for a line containing the word **password** of **PASSWORD**
- the script removes that line from the file
- At the end, you should create a "security report" specifying files that were changed, and the number of lines removed from each file.