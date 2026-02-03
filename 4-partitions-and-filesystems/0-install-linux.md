
## Install Linux for partitions exercises

### Settings
- New VM
- Download Ubuntu ISO
- Choose Custom
- Choose your ISO (I use Ubuntu-desktop 24.04)
- define a user with password, system name
- Choose NAT for networking
- Choose LSI Logic
- Storge type:
We'll choose NVMe, so the name in /dev will be something like: 
    /dev/nvme0n1
- Create a new virtual disk  
- Specify Disk Capacity:
  - Max disk size - 20G (this is like the physical size of the disk)
  - Do NOT "Allocate all disk space now" (saves host space).
  - Select "Store virtual disk as a single file" (better performance/cleaner).
- Finish installation
