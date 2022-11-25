# File permissions

## Using letters

- Create a file in the current directory
- change the permissions of this file, so that everybody can write/read/execute the file.  
Use only letters, for example:  
**chmod u+x myfile**
- remove everything for everybody else, and leave read/write/execute just for the user.

## Using octal numbers

- Set the following:  
user:r-x  group r-- others: r-x

