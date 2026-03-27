## Linux users

- In Linux, every single thing that happens—every file opened or command run—is tied to a specific User ID (UID). 
- Think of it like a "digital passport." When a program runs, it carries the passport of the person who started it, 
which determines exactly what it can and cannot touch.


- Best way to see these IDs:
  - Use **id** to get the id of the current user:
```
yuval@comp> whoami
yuval
yuval@comp> id
uid=1000(yuval) gid=1000(yuval) groups=1000(yuval),4(adm),24(cdrom),27(sudo),30(dip),46(plugdev),100(users),116(lpadmin),981(docker)
yuval@comp> sudo su moshe
$ id
uid=1001(moshe) gid=1001(moshe) groups=1001(moshe)
$ exit
yuval@comp> 
```
  - Use **id \<username\>** to get the id of another user.
  - add **-u** to get just the id


#### Using sudo
- The **sudo** command changes the user id (both RUID and EUID which we'll cover in the next part) to 0 (root)
- It is temporary, accepts a single command and "remembers" the old user id:
```
yuval@comp> id
uid=1000(yuval) gid=1000(yuval) groups=1000(yuval),4(adm),24(cdrom),27(sudo),30(dip),46(plugdev),100(users),116(lpadmin),981(docker)
yuval@comp> sudo id
uid=0(root) gid=0(root) groups=0(root)
yuval@comp> 
yuval@comp> whoami
yuval
yuval@comp> sudo whoami
root
yuval@comp> 
```

#### Using su

- The **su** command stands for **substitute user**
- Some info:
```
> type su
su is /usr/bin/su
$> ls -l /usr/bin/su
-rwsr-xr-x 1 root root 55832 Sep  6  2025 /usr/bin/su
$> file /usr/bin/su
/usr/bin/su: setuid ELF 64-bit LSB pie executable, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, BuildID[sha1]=702151a20eae25f5371681229436765a796de714, for GNU/Linux 3.2.0, stripped
$> 
```
- The su command runs, changes RUID (so it becomes somebody else).
- It then runs bash for that someone.