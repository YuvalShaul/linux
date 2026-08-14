# Introduction to the Linux firewall (UFW)

**Prerequisite:** [0-linux-networking-basics.md](0-linux-networking-basics.md) and [1-nm-config.md](1-nm-config.md) — you should know how to find your VM's IP address and verify that two VMs can reach each other.

In this lab you will:
1. Understand what the Linux firewall actually is (kernel vs. frontend tools).
2. Run a tiny web server on one VM and reach it from another.
3. Turn the firewall **on** and watch the connection get blocked.
4. Add a rule to allow it again.

## What is "the Linux firewall"?

A firewall decides, packet by packet, what is allowed **in** and **out** of a machine. To understand the Linux tooling around this, you have to separate two things people constantly mix up: the firewall *itself*, and the tools that *configure* it.

**The actual firewall is in the kernel.** It is the kernel's packet filtering framework, called **netfilter** — the code that hooks into the network stack, inspects every packet as it flows through, and accepts or drops it according to the loaded rules. It has been part of the kernel since version 2.4 (released in 2001). No userspace program ever touches your packets; by the time a packet could reach a program, the filtering decision has already been made.

**iptables is a userspace tool for configuring netfilter.** It doesn't filter anything itself — it loads rules into the kernel and walks away. But for about two decades it was so dominant that people used "iptables" and "Linux firewall" interchangeably — that's why the confusion exists (and why nearly every firewall guide you'll find online is written in iptables terms). It is the control interface, not the firewall.

**nftables is iptables' successor.** Around 2014 the kernel gained a redesigned rule engine (still part of netfilter), configured with the `nft` tool. Modern distributions — Ubuntu included — use it as the backend; even the `iptables` command on Ubuntu today is a compatibility wrapper that translates to nftables underneath.

**Frontends make it humane.** Raw `nft`/`iptables` rules are verbose and easy to get dangerously wrong, so distributions ship higher-level frontends that generate the rules for you:

| Tool | What it is | Where you meet it |
|---|---|---|
| `nft` | The low-level nftables tool | Scripts, advanced setups |
| `iptables` | The legacy tool (still everywhere in old guides) | Older systems, Docker |
| `firewalld` | High-level daemon | Fedora/RHEL family |
| **`ufw`** | **"Uncomplicated Firewall"** — high-level frontend | **Ubuntu (this lab)** |

All of these end up programming the *same* kernel machinery — they are different steering wheels on the same engine, and the layering is:

```
you → ufw / firewalld → nftables rules → netfilter (kernel) → your packets
```

On Ubuntu, `ufw` is installed by default but **disabled** — a fresh Ubuntu accepts any incoming connection.

### Stateless or stateful?

Firewalls come in two kinds:

- A **stateless** firewall judges every packet **in isolation**: it sees addresses, ports and flags, applies the rules, and forgets the packet immediately. It has no idea whether a packet is the start of a conversation or the middle of one.
- A **stateful** firewall additionally **remembers connections**. When you open a connection outward, it records it in a *connection tracking* table; every packet that arrives is first checked against that table, and packets that belong to an existing (or related) connection are recognized as such.

**The Linux firewall is stateful.** Netfilter's connection tracker (*conntrack*) maintains that table in the kernel — you can even look at it with `sudo conntrack -L` (package `conntrack`, optional). All the frontends, UFW included, build their rules on top of it: the very first thing UFW's generated ruleset does is accept anything conntrack marks as `ESTABLISHED` or `RELATED`.

This matters more than it sounds. Think about what "deny incoming" would mean for a *stateless* firewall: when your browser fetches a web page, the page comes back as **incoming** packets — a stateless "deny incoming" would block the replies to your own requests and break everything. A stateful firewall has no such problem: your outgoing request created a conntrack entry, so the reply is recognized as part of an established connection and sails through. Only packets that try to **start a new** incoming connection get judged by your rules.

### The default policy

With that in mind, the single most important fact about UFW: when enabled, its **default policy** is
- **deny incoming** — no **new** connection may come *in* unless a rule allows it,
- **allow outgoing** — you may connect *out* to anyone (and thanks to statefulness, the replies get back in automatically).

So enabling the firewall doesn't break your web browsing or `apt` — it breaks other machines connecting to *your* services. That is exactly what we'll demonstrate.

## The lab setup

Two Ubuntu VMs on the same VMware NAT network, like in the previous labs:
- **server** — will run a tiny web server (ours: `192.168.111.128`)
- **client** — will connect to it (ours: `192.168.111.129`)

Find your own addresses with `ip -4 addr show` and verify with `ping` that the client can reach the server. Substitute your addresses everywhere below.

### Step 1: Start a tiny web server

Python's built-in web server is perfect for this — no installation, one command. On the **server**:
```
server$ mkdir ~/www && cd ~/www
server$ echo "it works" > index.html
server$ python3 -m http.server 8000
Serving HTTP on 0.0.0.0 port 8000 (http://0.0.0.0:8000/) ...
```
Leave it running in this terminal. `0.0.0.0` means "listening on all interfaces" — including the one the client can reach.

### Step 2: Connect from the client (firewall still off)

On the **client**:
```
client$ curl http://192.168.111.128:8000/
it works
```
It works because the server's firewall is inactive. Confirm that on the **server** (open a second terminal, leave the web server running):
```
server$ sudo ufw status
Status: inactive
```

### Step 3: Enable the firewall — but protect SSH first!

⚠️ **The classic self-lockout:** if you are connected to the server over SSH, enabling the firewall with its "deny incoming" default will cut off your *next* SSH connection. Always allow SSH **before** enabling:
```
server$ sudo ufw allow ssh
Rules updated
```
(`ssh` is a service name UFW knows — it means TCP port 22. If you work directly in the VMware console, this step can't hurt either.)

Now enable:
```
server$ sudo ufw enable
Command may disrupt existing ssh connections. Proceed with operation (y|n)? y
Firewall is active and enabled on system startup
```
Note "enabled on system startup" — this survives reboots.

### Step 4: Watch the connection get blocked

On the **client**, try again:
```
client$ curl --max-time 10 http://192.168.111.128:8000/
curl: (28) Connection timed out after 10005 milliseconds
```
Two things to notice:
- The web server is **still running** — check its terminal on the server; nothing changed there. The packets never reached it: the kernel dropped them first.
- The failure is a **silent timeout**, not an instant "connection refused". This is the firewall's signature: by default UFW *drops* blocked packets (says nothing) rather than *rejecting* them (sending back an error). Remember this symptom — a hang that eventually times out usually means *firewall*, an instant refusal usually means *service not running*.

### Step 5: Allow port 8000

On the **server**:
```
server$ sudo ufw allow 8000/tcp
Rule added
Rule added (v6)
```
Rules take effect immediately — no reload, no restart. Check the rule list:
```
server$ sudo ufw status
Status: active

To                         Action      From
--                         ------      ----
22/tcp                     ALLOW       Anywhere
8000/tcp                   ALLOW       Anywhere
```
And from the **client**:
```
client$ curl http://192.168.111.128:8000/
it works
```
Blocked, allowed, working again — that's the whole firewall workflow.

### Step 6: A more precise rule (optional but good practice)

`allow 8000/tcp` opens the port to *anyone* who can reach the machine. On a real server you often want to allow only a specific network. Delete the broad rule and replace it:
```
server$ sudo ufw delete allow 8000/tcp
server$ sudo ufw allow from 192.168.111.0/24 to any port 8000 proto tcp
```
Re-test from the client — still works, but now only hosts in your subnet are allowed. This *from/to* form is the one you'll use in the NFS lab ([3-nfs-lab.md](3-nfs-lab.md)).

### Cleanup

Stop the web server with `Ctrl+C`. Then either keep the firewall on (it's good hygiene — SSH is allowed) or turn it off for the next labs:
```
server$ sudo ufw disable
Firewall stopped and disabled on system startup
```

## Cheat sheet

| Command | What it does |
|---|---|
| `sudo ufw status verbose` | Show state, default policies, and rules |
| `sudo ufw allow ssh` | Allow a service by name (port 22/tcp) |
| `sudo ufw allow 8000/tcp` | Allow a port from anywhere |
| `sudo ufw allow from <subnet> to any port <port> proto tcp` | Allow a port from one network only |
| `sudo ufw delete allow 8000/tcp` | Delete a rule (prefix any rule with `delete`) |
| `sudo ufw enable` / `sudo ufw disable` | Turn the firewall on / off (persists across reboots) |
