# Who configures the network? (NetworkManager, systemd-networkd, netplan)

A common source of confusion in Linux networking: you edit a configuration, and nothing happens. Usually the reason is simple — **you talked to the wrong manager**. This lesson explains who the players are and how to find out which one runs *your* system.

### The kernel does the work, a manager does the configuration
The actual networking — moving packets, holding IP addresses, routing — is done by the **kernel**. Commands like `ip addr` and `ip route` show the kernel's *current* state, and can even change it:
```
$ sudo ip addr add 192.168.111.50/24 dev ens33
```
But changes made this way are **not persistent** — they vanish on reboot, because nobody wrote them down anywhere.

Persistence is the job of a **network manager**: a service that reads configuration files at boot and applies them to the kernel. On modern systems there are two common managers — and on Ubuntu, an extra layer on top of them.


### Player 1: NetworkManager
- **Where you'll find it:** desktop distributions — Ubuntu Desktop, Fedora, RHEL.
- **Designed for:** dynamic environments — laptops that hop between Wi-Fi networks, VPNs, plugging cables in and out.
- **Configured with:** the `nmcli` command (or the desktop GUI / `nmtui`).
- **Configuration lives in:** `/etc/NetworkManager/system-connections/` — one file per *connection profile*.
```
$ nmcli device status
DEVICE  TYPE      STATE      CONNECTION
ens33   ethernet  connected  Wired connection 1
lo      loopback  unmanaged  --
```
A key concept: NetworkManager separates the **device** (`ens33`, the actual interface) from the **connection** (`Wired connection 1`, a configuration profile). Commands modify the *connection*; the device just runs whatever profile is active on it.

### Player 2: systemd-networkd
- **Where you'll find it:** Ubuntu **Server**, minimal and cloud images.
- **Designed for:** static environments — servers whose network is configured once and never changes.
- **Configured with:** plain text files (there is a small CLI, `networkctl`, mostly for *inspecting*).
- **Configuration lives in:** `/etc/systemd/network/` — `*.network` files.
```
$ networkctl list
IDX LINK  TYPE     OPERATIONAL SETUP
  1 lo    loopback carrier     unmanaged
  2 ens33 ether    routable    configured
```
`configured` in the SETUP column means networkd owns that interface. (`unmanaged` means it doesn't.)

### Player 3 (Ubuntu only): netplan
netplan is **not a third manager** — it is a translation layer that Ubuntu puts on top of the other two. You write YAML files in `/etc/netplan/`, and on boot netplan *renders* them into configuration for one of the real managers:
```
$ cat /etc/netplan/*.yaml
network:
  version: 2
  renderer: NetworkManager
```
- `renderer: NetworkManager` → NetworkManager manages everything (Ubuntu **Desktop** default).
- `renderer: networkd` — or no renderer line at all → systemd-networkd manages everything (Ubuntu **Server** default).

After editing a netplan file, apply it with `sudo netplan apply`.

**Why this matters:** on Ubuntu Server, `nmcli` may be installed and NetworkManager may even be running — but if netplan renders to networkd, your interface is `unmanaged` from NetworkManager's point of view, and `nmcli` changes will go nowhere.


### So who runs MY system? A checklist
Run these and compare with the table below.

**Check 1 — which manager services are running?**
```
$ systemctl is-active NetworkManager
active
$ systemctl is-active systemd-networkd
inactive
```
(`could not be found` counts as "not running".) Careful: **both can be active at once** — this alone does not settle it.

**Check 2 — who claims your interface?** This is the decisive check. Ask each manager:
```
$ nmcli device status
DEVICE  TYPE      STATE      CONNECTION
ens33   ethernet  connected  Wired connection 1
```
```
$ networkctl list
IDX LINK  TYPE  OPERATIONAL SETUP
  2 ens33 ether routable    unmanaged
```
Whichever manager shows the interface as managed (`connected` for nmcli, `configured` for networkctl) is the one in charge. The other should say `unmanaged`.

**Check 3 (Ubuntu) — what does netplan say?**
```
$ cat /etc/netplan/*.yaml
```
Look for the `renderer:` line as explained above.

**Summary:**

| You see | Conclusion |
|---|---|
| `nmcli`: device `connected` with a connection name | NetworkManager is in charge — use `nmcli` |
| `networkctl`: device `configured` | systemd-networkd is in charge — use `/etc/systemd/network/` files (or netplan on Ubuntu) |
| Ubuntu with `/etc/netplan/*.yaml` present | Configure via netplan YAML, *or* set `renderer: NetworkManager` and use `nmcli` |
| Device `unmanaged` in **both** | Something else (e.g. old `ifupdown`, a cloud agent) — investigate before touching anything |

Once you have confirmed that **NetworkManager** manages your interface, continue to [1-nm-config.md](1-nm-config.md) to actually configure addresses with `nmcli`.
