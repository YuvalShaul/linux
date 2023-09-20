# Playing with Linux mount command

## Showing mounts

- Use the **mount** command to show mounts  
(there may be several)
- Use this command to look for a mount for the root directory:  
**mount | grep " / "**
- You should be able to discover the device that is mounter there.  
In my case this is **/dev/sda1**

## Adding a mount

- Create a new directory under the current directory:  
**mkdir myroot**
- Mount your device there:  
**sudo mount /dev/sda1 ./myroot/**
- Go to this directory and look around:  
  - **cd myroot**
  - **ls**  
  (use pwd to see that you are not dreaming):  
  - **pwd**
- This is amazing!!!  
now remove your new mount:
  - **cd ..**
  - **sudo umount /dev/sda1 ./myroot**