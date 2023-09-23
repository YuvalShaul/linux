# Playing with Linux mount command

## Linux Devices

- The Linux filesystem does not show only files
- **cd** into the /dev directiry and look at what you see there:
```
cd /dev
ls -l
```
- Here's an example from what I see in my Debian-11 Linux.  
I just cut a few of the lines
```
...
brw-rw----  1 root    disk      8,   0 Sep 20 09:49 sda
brw-rw----  1 root    disk      8,   1 Sep 20 09:49 sda1
brw-rw----  1 root    disk      8,   2 Sep 20 09:49 sda2
brw-rw----  1 root    disk      8,   3 Sep 20 09:49 sda3
brw-rw----  1 root    disk      8,   4 Sep 20 09:49 sda4
...
```
- These entries are not files, nor are they directories.  
They are [**devices**](https://en.wikipedia.org/wiki/Device_file), in this case a hard disk drive (**sda**) and the partitions inside it (**sda1**, **sda2**, ...)
- Note the letter **b** at the beginning of each line, specifying this as a [block device](https://en.wikipedia.org/wiki/Device_file#Block_devices).  
Disks, SSDs and the such are block devices.
- Other files has a **c**, and that means that they are [charcter devices](https://en.wikipedia.org/wiki/Device_file#Character_devices)
- We use the [**mount**](https://man7.org/linux/man-pages/man8/mount.8.html) command to attach a device to specific location inside the  directory tree.

## Showing mounts

- Use the **mount** command to show mounts.  
(there may be several of them)
- Use this command to look for a mount for the root directory:  
**mount | grep " / "**
- You should be able to discover the device that is mounter there.  
In my case this is **/dev/sda1**

## Adding a mount

- Create a new directory under the current directory:
```
cd
mkdir myroot
```
- Mount your device there:  
```
sudo mount /dev/sda1 ./myroot/
```
- Go to this directory and look around:
```
cd myroot
ls
```
(use pwd to see that you are not dreaming):  
```
pwd
```
- Amazing.  
now remove your new mount:
```
cd ..
sudo umount /dev/sda1 ./myroot
```
