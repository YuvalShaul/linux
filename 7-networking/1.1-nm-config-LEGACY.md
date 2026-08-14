# Legacy network configuration files (ifcfg, interfaces — and who still reads them)

**Prerequisite:** [0-linux-networking-basics.md](0-linux-networking-basics.md) — you should already know the modern players: NetworkManager, systemd-networkd and netplan.

The lessons so far describe the *modern* frameworks. But log into an older server — or an Amazon Linux 2 instance — and you'll find none of the familiar files. Instead there are configuration schemes from an earlier era, and they are still very much in production. This lesson maps them, and answers two practical questions: **do the new frameworks read the old files?** and **who is really in charge on a cloud instance?**

### The four coexisting regimes
Linux never had one network configuration format. Today roughly **four regimes** coexist, and which one you touch depends on distro *and version*:

| Regime | Config location | Lineage |
|---|---|---|
| **ifcfg files** | `/etc/sysconfig/network-scripts/ifcfg-*` | classic Red Hat initscripts |
| **interfaces file** | `/etc/network/interfaces` | classic Debian |
| **netplan** | `/etc/netplan/*.yaml` | Ubuntu (17.10+), a *generator* on top of the two below |
| **systemd-native** | `/etc/systemd/network/*.network` (networkd) or `/etc/NetworkManager/system-connections/*` (NM keyfiles) | the modern target everything is drifting toward |

### Regime 1: Red Hat–style `ifcfg` files
The classic format: one file per interface under `/etc/sysconfig/network-scripts/`:
```
$ cat /etc/sysconfig/network-scripts/ifcfg-eth0
DEVICE=eth0
BOOTPROTO=dhcp
ONBOOT=yes
```
Its status varies sharply across the Red Hat family:

| Distro | Status of ifcfg |
|---|---|
| **Amazon Linux 2** | **Still the active scheme** — AL2 is RHEL7-ish in ancestry and inherited it wholesale |
| **RHEL/CentOS 7–8** | NetworkManager took over as the *service*, but kept an **ifcfg compatibility plugin**, so the old files remained the active input |
| **RHEL 9+** | ifcfg is **deprecated** — NM now uses keyfiles in `/etc/NetworkManager/system-connections/`; old ifcfg files may still be read, but new installs don't create them |
| **Amazon Linux 2023** | **Dropped entirely** — AL2023 switched to systemd-networkd (`/etc/systemd/network/*.network`) with systemd-resolved for DNS |

Also in this family: `/etc/sysconfig/network` — a global settings file that is mostly **vestigial** now.

### Regime 2: Debian's `/etc/network/interfaces`
```
$ cat /etc/network/interfaces
auto eth0
iface eth0 inet dhcp
```
- **Debian:** still fully functional.
- **Ubuntu:** abandoned it for netplan starting with **17.10**. Remember from lesson 0: netplan is just a generator — it renders YAML into config for systemd-networkd (servers) or NetworkManager (desktops).

### Ancient files that are NOT legacy
Don't confuse the dying schemes above with these — they are ancient *and very much alive* on every distro:
- `/etc/hosts` — static name → address mappings
- `/etc/resolv.conf` — DNS resolver config (though today it is usually a **symlink to a systemd-resolved stub**, not a hand-edited file)
- `/etc/nsswitch.conf` — lookup order (files vs. DNS vs. others)
- `/etc/hostname` — the system's name

### Do the new frameworks read the old files?
Partially — and **only within the Red Hat lineage**:

- **NetworkManager can read ifcfg files**, via its `ifcfg-rh` plugin. This is exactly how RHEL 7/8 worked: NM was the running service, but the config format stayed the familiar ifcfg files. On those systems the old files weren't legacy leftovers — they were the **active input to a modern framework**. In RHEL 9 the plugin still exists but is deprecated; keyfiles are the default.
- **systemd-networkd reads NOTHING legacy** — not ifcfg, not `/etc/network/interfaces`. It only understands its own `.network`/`.netdev` files. No compatibility layer at all.
- **netplan doesn't read the old Debian `interfaces` file either** — it's a one-way street. There is an experimental `netplan migrate` command, but no runtime compatibility.

Bottom line: "the new framework covers the old files" is really an **NM-specific, Red-Hat-family** story. Everywhere else, the transition was a hard cut.

### The cloud twist: cloud-init sits above all of them
On any cloud instance there is an extra layer to understand, and it is the important one: **cloud-init**. On first boot it queries the cloud's metadata service (EC2 metadata on AWS) and *generates* the network configuration — for whatever renderer the image uses:

| AMI | cloud-init renders to |
|---|---|
| Amazon Linux 2 | ifcfg files (initscripts) |
| Amazon Linux 2023 | systemd-networkd `.network` files |
| RHEL/Rocky/Alma 9 | NetworkManager keyfiles |

**Why this matters:** hand-editing the config files often gets **overwritten on the next boot**, because cloud-init regenerates them. The real "source of truth" is cloud-init, not the files. To go manual, you must disable cloud-init's network module (e.g. `network: {config: disabled}` in `/etc/cloud/cloud.cfg.d/`).

#### So can an AWS instance be configured by NetworkManager?
Yes — with distro-dependent caveats:

- **Amazon Linux 2:** the running service is the classic `network` initscripts service, but you *can* install and enable NetworkManager — thanks to the ifcfg plugin it picks up the same files. People do this mainly for NM features (VPN plugins, etc.). Not the default; works fine.
- **Amazon Linux 2023:** systemd-networkd is the default. You can install NM and disable networkd, but you're swimming against Amazon's testing matrix — their docs, cloud-init integration and IMDSv2 helpers assume networkd.
- **RHEL/Rocky/Alma AMIs:** NetworkManager **is already the default** there — "AWS configured by NM" is the normal state, not a hack.

And in every case: swap the framework and you must also tell cloud-init to render for it (or disable its network module), otherwise your changes get regenerated over on the next boot.

### Summary
- Four regimes coexist: **ifcfg**, **interfaces**, **netplan**, and **systemd-native** (networkd / NM keyfiles). Which one you touch depends on distro and version.
- Only **NetworkManager** absorbs old files (ifcfg, Red Hat family). networkd and netplan made hard cuts.
- The **AL2 → AL2023 transition** is a concrete example of the industry-wide drift from initscripts-era files to systemd-native networking.
- On cloud instances, **cloud-init outranks all of them** — find out what it renders before editing anything by hand.
