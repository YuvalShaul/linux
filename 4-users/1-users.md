## Linux Users

Linux, everything—from people to background services—needs an identity to interact with the system.

#### Users Properties

Every user account is defined by a specific set of attributes. When you create a user, the system assigns:
- **Username**: The human-readable name (e.g., jane).
- **UID (User ID)**: A unique number the system actually uses to track you.
  - **0**: Reserved for the root user.
  - **1-999**: Usually reserved for system accounts (services like databases).
  - **1000+**: Standard human users.
- **GID (Group ID)**: The ID of the user's primary group.
- **Home Directory**: The user's personal "sandbox" (usually /home/username).
- **Default Shell**: The command-line environment that opens when they log in (usually /bin/bash or /bin/zsh).

Linux doesn't hide this info in a secret registry; it’s stored in simple text files that you can actually read.

|  File |  Purpose |
|---------------- | -----------------|
|/etc/passwd | Contains the main user details (Username, UID, GID, Home Dir, Shell).|
|/etc/shadow | Stores encrypted passwords and account aging info (only readable by root for security).| 
| /etc/group |  Defines groups and lists which users belong to them. |
| /etc/sudoers | Defines who has "admin" privileges.|


#### Creating Users

- While you could technically edit files directly, there are much safer ways to handle this.
- Use the **useradd** command:
  - The **-m** option makes a home directory /home/username and populates it with default configuration files (like .bashrc) from the /etc/skel directory.
  - The capital **-M** explicitly tells Linux not to create the home directory.  
  This is common for "service accounts" that run background software and don't need to log in or store personal files.
  - Example - creating a new user:
  ```
  yuval@comp> sudo useradd -m moshe
[sudo: authenticate] Password: 
yuval@comp> 
yuval@comp> ls -l /home
total 8
drwxr-x---  2 moshe moshe 4096 Mar 27 08:08 moshe
drwxr-x--- 38 yuval yuval 4096 Mar 27 07:59 yuval
yuval@comp> 
```
- The new account is created, but password is not set.
- Change (set) the password:
```
yuval@comp> sudo passwd moshe
New password: 
BAD PASSWORD: The password is shorter than 8 characters
Retype new password: 
passwd: password updated successfully
yuval@comp> 
yuval@comp> 
yuval@comp> su moshe
Password: 
yuval@comp> whoami
moshe
yuval@comp> 
```
- The new user is not in the sudoers list:
```
yuval@comp> 
yuval@comp> sudo su moshe
$ whoami
moshe
$ sudo su root
sudo-rs: I'm sorry moshe. I'm afraid I can't do that
$ exit
yuval@comp> whoami
yuval
yuval@comp> sudo su root
root@yuval-VMware-Virtual-Platform:/home/yuval# whoami
root
root@yuval-VMware-Virtual-Platform:/home/yuval# exit
exit
yuval@comp> 
```

  