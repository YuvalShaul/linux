# NFS lab: sharing a filesystem between two Ubuntu VMs

**Prerequisite:** [0-linux-networking-basics.md](0-linux-networking-basics.md) and [1-nm-config.md](1-nm-config.md) — you should be comfortable finding your interface, IP address and subnet, and know how your VMs get their addresses.

## What is NFS?

**NFS (Network File System)** lets one machine (the *server*) export a directory over the network, and other machines (the *clients*) mount it as if it were a local filesystem. Once mounted, ordinary tools (`ls`, `cp`, `vim`, …) work on the remote files transparently — the kernel translates file operations into network requests behind the scenes.

A few things worth knowing before we start:

- NFS is the classic Unix/Linux file-sharing protocol (Windows' equivalent is SMB/CIFS). It was created by Sun Microsystems in 1984 and is still the standard way to share filesystems between Linux machines.
- The version that matters today is **NFSv4**. Older versions (v3) relied on a whole family of helper services (`rpcbind`, `mountd`, `statd`, …), each listening on its own — sometimes random — port. **NFSv4 uses a single well-known TCP port: 2049.** This makes it dramatically easier to firewall, and it is what Ubuntu uses by default.
- The server decides *what* to export and *to whom* in one file: `/etc/exports`.
- The client mounts an export with a normal `mount` command — the "device" is just `server:/path` instead of `/dev/something`.
- **Trust model:** plain NFS identifies users by **numeric UID/GID**. If UID 1000 on the client writes a file, it is owned by UID 1000 on the server — regardless of the user *names* on either side. Keep this in mind when the permissions look "wrong".

## The lab setup

You need **two Ubuntu 25.x VMs** running side by side in VMware Workstation Pro (or Fusion on macOS):

| Role   | VM              | Packages we'll install |
|--------|-----------------|------------------------|
| Server | first Ubuntu VM | `nfs-kernel-server`    |
| Client | second Ubuntu VM| `nfs-common`           |

If you only have one VM: shut it down, and use VMware's **Manage → Clone → Create a full clone** to get a second one. After cloning, give it a different hostname (`sudo hostnamectl set-hostname nfs-client`) so you always know which terminal you're typing in.

### Step 0: Make sure the two VMs can reach each other

Both VMs must be on the **same virtual network**. The simplest choice is to leave both on the default **NAT** network (vmnet8 in Workstation, "Share with my Mac" in Fusion) — two VMs on the same NAT network sit on the same virtual switch and can talk to each other directly.

On **each** VM, find its address:
```
$ ip -4 addr show
2: ens33: <BROADCAST,MULTICAST,UP,LOWER_UP> ...
    inet 192.168.111.128/24 ...
```
Write both addresses down. For the rest of this lab we'll use:
- **Server:** `192.168.111.128`
- **Client:** `192.168.111.129`

(Yours will differ — substitute your own everywhere below.)

Now verify connectivity **from the client to the server**:
```
client$ ping -c 2 192.168.111.128
64 bytes from 192.168.111.128: icmp_seq=1 ttl=64 time=0.41 ms
```
If ping fails, stop and fix that first: check that both VMs are on the same network type in *VM Settings → Network Adapter*, and that both got an address in the same subnet.

**Tip:** DHCP addresses can change across reboots. For a real setup you'd give the server a static IP — you already know how from [1-nm-config.md](1-nm-config.md). For this lab, a DHCP address is fine as long as you re-check it after a reboot.


## Part 1 — The server

### Step 1: Install the NFS server
```
server$ sudo apt update
server$ sudo apt install nfs-kernel-server
```
This installs the in-kernel NFS server plus its userspace tools, and starts it immediately. Verify:
```
server$ systemctl status nfs-server
● nfs-server.service - NFS server and services
     Active: active (exited)
```
(`active (exited)` is normal here — the actual serving happens inside the kernel; the service unit just sets it up.)

### Step 2: Create a directory to export
```
server$ sudo mkdir -p /srv/nfs/share
server$ sudo chown nobody:nogroup /srv/nfs/share
server$ echo "hello from the server" | sudo tee /srv/nfs/share/greeting.txt
```
`/srv` is the conventional place for data served to other machines. We hand the directory to `nobody:nogroup` so that anonymous/squashed users (explained below) can write to it.

### Step 3: Declare the export in /etc/exports
Edit the exports file:
```
server$ sudo nano /etc/exports
```
Add this line (using **your** subnet from Step 0):
```
/srv/nfs/share  192.168.111.0/24(rw,sync,no_subtree_check)
```
Reading it left to right:
- `/srv/nfs/share` — the directory being exported.
- `192.168.111.0/24` — **who** may mount it: any host in this subnet. You could also put a single IP (`192.168.111.129`) to allow only your client. Never use `*` outside a lab.
- `rw` — clients may read **and** write (use `ro` for read-only).
- `sync` — the server confirms writes only after they hit the disk (safer; this is the default and the recommended setting).
- `no_subtree_check` — disables a legacy per-request path check; recommended for reliability and the effective default on modern systems.

One option we did *not* write, because it's on by default: `root_squash`. It maps root on the *client* to the unprivileged `nobody` user on the server — so a client machine's root cannot behave as root inside your export. This is a deliberate security feature; that's why we `chown`ed the directory to `nobody:nogroup` in Step 2.

### Step 4: Apply and verify the export
Tell the server to re-read `/etc/exports`:
```
server$ sudo exportfs -ra
```
(`-r` re-exports everything from the file, `-a` applies to all exports.) Then check what is actually being exported:
```
server$ sudo exportfs -v
/srv/nfs/share  192.168.111.0/24(sync,wdelay,hide,no_subtree_check,sec=sys,rw,secure,root_squash,no_all_squash)
```
Note how the defaults you didn't type (`root_squash`, `sec=sys`, …) show up explicitly here — `exportfs -v` shows the *effective* options, which makes it a great debugging tool.


## Part 2 — The firewall (read this even if everything works)

Ubuntu ships with the **UFW** firewall (*Uncomplicated Firewall*) — but **disabled by default**. Check on the server:
```
server$ sudo ufw status
Status: inactive
```
- `Status: inactive` — no firewall is filtering anything; NFS will work with no extra steps, and you can skip to Part 3.
- `Status: active` — the firewall is on, and its default policy blocks incoming connections. Your client's mount attempt will **hang for a long time and then fail** — this is the classic symptom of a firewall problem (a "connection refused" is instant; a firewall *drop* just goes silent).

If the firewall is (or should be) active, allow NFS. Because NFSv4 uses the single port 2049, one rule is enough — and we scope it to the client's subnet rather than the whole world:
```
server$ sudo ufw allow from 192.168.111.0/24 to any port 2049 proto tcp
server$ sudo ufw status
Status: active
To                         Action      From
--                         ------      ----
2049/tcp                   ALLOW       192.168.111.0/24
```
No reload or restart is needed — UFW rules take effect immediately.

Two more firewall notes:

1. **If you use SSH between the VMs**, make sure you don't lock yourself out when enabling UFW: run `sudo ufw allow ssh` *before* `sudo ufw enable`.
2. **If you ever need NFSv3** (e.g. an old appliance as client), the single-port trick no longer holds — v3 also needs `rpcbind` (port 111) and `mountd`, whose port must be pinned in `/etc/nfs.conf` (`[mountd] port=...`) before you can open it in the firewall. This is exactly the pain NFSv4 was designed to remove — prefer v4 whenever you can.

The *client* side needs no firewall rules: UFW allows outgoing connections by default, and the client only ever initiates connections to the server.


## Part 3 — The client

### Step 5: Install the NFS client tools
```
client$ sudo apt update
client$ sudo apt install nfs-common
```
This provides `mount.nfs`, the mount helper that the `mount` command calls for NFS filesystems.

### Step 6: Mount the export
Create a mount point and mount:
```
client$ sudo mkdir -p /mnt/nfs
client$ sudo mount -t nfs4 192.168.111.128:/srv/nfs/share /mnt/nfs
```
The "device" argument is `server:/exported/path`. If the command returns silently — it worked. Verify:
```
client$ findmnt /mnt/nfs
TARGET   SOURCE                            FSTYPE OPTIONS
/mnt/nfs 192.168.111.128:/srv/nfs/share    nfs4   rw,relatime,vers=4.2,...

client$ cat /mnt/nfs/greeting.txt
hello from the server
```
You are reading a file that lives on the other VM's disk.

**If the mount hangs for ~2 minutes and then times out:** that is the firewall symptom from Part 2 — go back and check `ufw status` **on the server**. Press `Ctrl+C` to abort the hung mount.

### Step 7: Write from the client, watch on the server
```
client$ echo "hello from the client" | sudo tee /mnt/nfs/reply.txt
client$ ls -l /mnt/nfs
-rw-r--r-- 1 nobody nogroup 22 Aug 14 10:30 reply.txt
```
Note the owner: `nobody`. You wrote as root (`sudo`), but `root_squash` on the server mapped you to `nobody` — the security default from Step 3 in action.

Now check on the **server**:
```
server$ cat /srv/nfs/share/reply.txt
hello from the client
```

### Step 8: Make the mount permanent (fstab)
The mount from Step 6 disappears on reboot. To make it permanent, add it to the client's `/etc/fstab`:
```
client$ sudo nano /etc/fstab
```
Add this line:
```
192.168.111.128:/srv/nfs/share  /mnt/nfs  nfs4  defaults,_netdev  0  0
```
The one non-obvious option is `_netdev`: it tells the boot process "this filesystem needs the *network*, don't try to mount it before the network is up".

Test it **without rebooting** — unmount, then ask fstab to mount everything:
```
client$ sudo umount /mnt/nfs
client$ sudo systemctl daemon-reload
client$ sudo mount -a
client$ findmnt /mnt/nfs
```
If `findmnt` shows the mount again, your fstab line is correct and will work on reboot too.

**Caveat for this lab:** if your server's address is from DHCP and it changes, this fstab line breaks and the client may *wait for the mount at boot*. If that worries you, either give the server a static IP (you know how), or add `,noauto` to the options so the mount only happens when you run `mount /mnt/nfs` manually.


## Cleanup / troubleshooting cheat sheet

| Symptom | Likely cause | Check |
|---|---|---|
| Mount hangs, then times out | Firewall dropping port 2049 | `sudo ufw status` on the **server** |
| `mount.nfs: access denied by server` | Client IP not matched by `/etc/exports` | `sudo exportfs -v` on the server; is the client's IP inside the allowed subnet? |
| `mount.nfs: Connection refused` (instant) | NFS server not running | `systemctl status nfs-server` on the server |
| Files owned by `nobody`, writes denied | `root_squash` + directory not writable by `nobody` | `ls -ld /srv/nfs/share` on the server |
| Changed `/etc/exports`, nothing happened | Forgot to re-export | `sudo exportfs -ra` |

To tear the lab down: on the client, `sudo umount /mnt/nfs` and remove the fstab line; on the server, `sudo apt remove nfs-kernel-server` (or just leave it — it only serves your NAT subnet).
