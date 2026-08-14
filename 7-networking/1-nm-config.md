# Configuring the network with NetworkManager (nmcli)

**Prerequisite:** [0-linux-networking-basics.md](0-linux-networking-basics.md) — you should already know what NetworkManager, systemd-networkd and netplan are, and how to tell which one manages your system.

In this lab you will:
1. Verify that your system is actually managed by NetworkManager.
2. Use `nmcli` to configure a **permanent static IP address** (one that survives reboots).
3. Switch back to an **automatic (DHCP) address**.

But first — you need to know *which* interface you are configuring, and *what* addresses make sense on your network.


### Step 0a: Finding your interface name
Modern Linux does not call interfaces `eth0` anymore. Names are "predictable", based on the hardware location, e.g.:
- `ens33` — a PCI Ethernet device (this is what you will typically see in VMware Workstation)
- `enp0s3` — another PCI naming variant (common in VirtualBox)
- `lo` — the loopback interface (always there, ignore it)

List your interfaces with either of:
```
$ ip link
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 ...
2: ens33: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 ...
```
```
$ nmcli device status
DEVICE  TYPE      STATE      CONNECTION
ens33   ethernet  connected  Wired connection 1
lo      loopback  unmanaged  --
```

Note two things from the `nmcli` output:
- The **device** name (`ens33`) — the actual interface.
- The **connection** name (`Wired connection 1`) — NetworkManager's configuration *profile* attached to that device. `nmcli` commands modify the *connection*, not the device directly.


### Step 0b: Finding sensible addresses (hint: look at the default route)
Before assigning a static IP you must know:
- Which **subnet** you are on.
- What the **default gateway** (router) address is.

Ask the kernel what it currently uses:
```
$ ip route
default via 192.168.111.2 dev ens33 proto dhcp metric 100
192.168.111.0/24 dev ens33 proto kernel scope link src 192.168.111.128 metric 100
```
This tells us:
- The default gateway is `192.168.111.2`.
- The subnet is `192.168.111.0/24`.
- Our current (DHCP-given) address is `192.168.111.128`.

**VMware Workstation Pro hint:** with the default NAT network (vmnet8), VMware picks a random `192.168.x.0/24` subnet, where:
- `.1` is the address of the host machine (your Windows/Linux host on the virtual switch)
- `.2` is the NAT gateway (this is your default gateway and also a DNS forwarder)
- `.128`–`.254` is the DHCP pool

You can see (and change) these in *Edit → Virtual Network Editor* on the host.
So a safe choice for a static IP is something **inside the subnet but outside the DHCP pool**, e.g. `192.168.111.50`.


### Step 1: Verify that NetworkManager is used
As you learned in [0-linux-networking-basics.md](0-linux-networking-basics.md), not every system uses NetworkManager — and on Ubuntu Server, netplan usually renders to systemd-networkd instead. Before touching `nmcli`, confirm NetworkManager is both **running** and **managing your interface**:
```
$ systemctl status NetworkManager
● NetworkManager.service - Network Manager
     Loaded: loaded (/usr/lib/systemd/system/NetworkManager.service; enabled; preset: enabled)
     Active: active (running) since Thu 2026-08-14 09:12:03 IDT; 1h 2min ago
```
Look for `Active: active (running)` and `enabled` (so it starts again on boot). Then the decisive check:
```
$ nmcli device status
DEVICE  TYPE      STATE      CONNECTION
ens33   ethernet  connected  Wired connection 1
```
`connected` with a connection name means NetworkManager owns the interface — you are good to go.

If instead it shows `unmanaged`, go back to the checklist in [0-linux-networking-basics.md](0-linux-networking-basics.md) to find out who really manages it (on Ubuntu, most likely you need to set `renderer: NetworkManager` in `/etc/netplan/*.yaml` and run `sudo netplan apply`).


### Step 2: Configure a permanent static IP with nmcli
The key idea: `nmcli connection modify` writes to the connection's **configuration file on disk**, so the change is *permanent by definition*. It does **not** change the running state — that happens when the connection is (re)activated.

Using the values we discovered in Step 0 (adjust to *your* subnet!):
```
$ sudo nmcli connection modify "Wired connection 1" \
    ipv4.method manual \
    ipv4.addresses 192.168.111.50/24 \
    ipv4.gateway 192.168.111.2 \
    ipv4.dns "192.168.111.2 8.8.8.8"
```
What each setting means:
- `ipv4.method manual` — static configuration (instead of DHCP)
- `ipv4.addresses` — the address **including the prefix length** (`/24`)
- `ipv4.gateway` — the default route (in VMware NAT: the `.2` address)
- `ipv4.dns` — DNS servers (the VMware NAT gateway forwards DNS, plus Google as backup)

Now apply it by re-activating the connection:
```
$ sudo nmcli connection up "Wired connection 1"
Connection successfully activated (D-Bus active path: /org/freedesktop/NetworkManager/ActiveConnection/2)
```
(If you are connected over SSH you may lose the session — the IP just changed. Reconnect to the new address.)

Verify:
```
$ ip addr show ens33
2: ens33: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 ...
    inet 192.168.111.50/24 brd 192.168.111.255 scope global noprefixroute ens33

$ ip route
default via 192.168.111.2 dev ens33 proto static metric 100
```
Note the route now says `proto static` instead of `proto dhcp`.

Check connectivity:
```
$ ping -c 2 192.168.111.2      # can we reach the gateway?
$ ping -c 2 8.8.8.8            # can we reach the internet?
$ ping -c 2 google.com         # does DNS resolution work?
```

**Where did the configuration go?** Look at the file NetworkManager wrote:
```
$ sudo cat /etc/NetworkManager/system-connections/Wired\ connection\ 1.nmconnection
...
[ipv4]
address1=192.168.111.50/24,192.168.111.2
dns=192.168.111.2;8.8.8.8;
method=manual
```

**Prove it survives a restart:**
```
$ sudo reboot
```
After the machine comes back, log in and run `ip addr show ens33` again — you should still have `192.168.111.50`.


### Step 3: Configure automatic IP (back to DHCP)
Switch the method back to `auto` and clear the static settings (otherwise they linger in the profile):
```
$ sudo nmcli connection modify "Wired connection 1" \
    ipv4.method auto \
    ipv4.addresses "" \
    ipv4.gateway "" \
    ipv4.dns ""
```
Re-activate to make it take effect now:
```
$ sudo nmcli connection up "Wired connection 1"
```
Verify you got an address from the DHCP server again:
```
$ ip addr show ens33
    inet 192.168.111.128/24 ... scope global dynamic noprefixroute ens33

$ ip route
default via 192.168.111.2 dev ens33 proto dhcp metric 100
```
Note the address is marked `dynamic` and the route is back to `proto dhcp`.

Again — since `nmcli connection modify` changed the file on disk, this setting is also permanent and will survive a reboot.


### Useful commands summary
| Command | What it does |
|---|---|
| `nmcli device status` | List devices and which connection manages each |
| `nmcli connection show` | List all connection profiles |
| `nmcli connection show "Wired connection 1"` | Show all settings of a profile (very verbose) |
| `nmcli connection modify <name> <setting> <value>` | Change a setting **permanently** (on disk) |
| `nmcli connection up <name>` | (Re)activate a connection — apply changes now |
| `ip link` / `ip addr` / `ip route` | Inspect the *current running* state of the kernel |
