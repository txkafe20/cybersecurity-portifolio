# Week 5 Lab 01 — Finding Your Place on the Grid (CLI Simulator)

**Student Name:** Tiwonge Kafera

**Date Completed:** 10/26/26

**Module:** 2 — Networking & Cloud Foundations | **Week:** 5  
**Submission Path:** `week-05/labs/lab-01-finding-your-place.md`

---

## Overview

Module 1 was about the machine in front of you. Module 2 is about everything your machine can *reach* — and The Grid is where you learn to find your way around it. This lab walks you through the four questions every network troubleshooter asks first: *What is my own address? Can I get out of my own neighbourhood? What number is hiding behind that name? And what do these same commands look like on Windows?*

This is a **guided** lab. Every step tells you exactly what to run and exactly what to record — Lab 02 is where you work independently. Take your time, read each output before you paste it, and notice that the numbers you see connect to each other.

**Nothing here can break anything real.** The CLI Simulator is a consequence-free practice space, and every command in this lab only *looks* — none of them change a single setting.

---

## Lab Environment / Pre-Lab Check

| Component | Details |
|---|---|
| Environment | CyberFoundations CLI Simulator (browser-based, inside the Lab Portal) — no install, no VM, no real terminal required |
| Shell | Parts A, B, and C use **bash**; Part D uses **PowerShell** |
| Prerequisite | Week 5, Lessons 1 and 2 completed |
| Commands used | `ip addr`, `ip route`, `ping`, `dig` (bash) · `ipconfig`, `Resolve-DnsName`, `Test-Connection` (PowerShell) |

**Before you start:** log into the Lab Portal, open **Week 5 → CLI Simulator**, and load the **"The Grid — Finding Your Place"** scenario. You'll see two entries in the list: **The Grid — Finding Your Place (Bash)** (used for Parts A, B, and C) and **The Grid — Finding Your Place (PowerShell)** (used for Part D). Start with the Bash one.

Your simulated workstation on The Grid is called `ivy-workstation`. It sits on the same network segment as several other machines, and it has a way out to the rest of the world. Your first job is to find out what that looks like from the inside.

---

## Part A — Who Am I

Before you can ask whether you can reach anything else, you have to know where you are. On a network, "where you are" is an address — and the machine already knows it. You just have to ask.

### Step 1 — Read Your Own Address Plate

Run `ip addr` in the bash simulator. This prints every network interface your machine has, along with the address assigned to each one. The interface you care about is `eth0` — that's your wired connection to The Grid.

Command you ran:

```
ip addr
```

Output (the full `ip addr` result):

```
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 02:1a:7c:44:0b:5e brd ff:ff:ff:ff:ff:ff
    inet 10.20.5.42/24 brd 10.20.5.255 scope global eth0
       valid_lft forever preferred_lft forever
```

### Step 2 — Pull Out Your IPv4 Address

Look at the `eth0` section of your output and find the line beginning with `inet`. The number right after it, before the slash, is your **IPv4 address** — the dotted-quad address that identifies this machine on The Grid.

Your IPv4 address:

```
10.20.5.42
```

### Step 3 — Translate the Slash Into a Subnet Mask

The number *after* the slash on that same `inet` line is your **prefix length** — shorthand for the subnet mask. Lesson 1 called the subnet mask the answer to "which addresses are my neighbours?"

A `/24` prefix means the first three of the four numbers identify the network, and only the last number identifies the individual machine. Written the long way, `/24` is the subnet mask **255.255.255.0**.

Record both forms — the prefix you saw, and the mask it translates to.

Your prefix length and the subnet mask it means:

```
/24
subnet mask 255.255.255.0
```

### Step 4 — Find the Door Out

Your subnet mask tells you who your neighbours are. The **default gateway** is the address your machine sends traffic to when the destination is *not* a neighbour — the door out of your neighbourhood.

Here's something worth knowing now rather than being surprised by later: `ip addr` does **not** show you the gateway. It only reports the addresses on your interfaces. The gateway lives in a separate place — the machine's routing table — and it takes a second command to read it:

```
ip route
```

Run it. You'll get a couple of lines, and the one that matters starts with the word `default`. Read it as a sentence: *"anything with no better instructions goes via this address, out of this interface."*

(On Windows this split doesn't exist — `ipconfig` hands you address, mask, and gateway all at once, which you'll see in Part D. Linux separates "what are my addresses" from "where do I send things," and now you know both commands.)

The full `ip route` output:

```
default via 10.20.5.1 dev eth0
10.20.5.0/24 dev eth0 proto kernel scope link src 10.20.5.42
```

Your default gateway — the address on the `default` line:

```
10.20.5.1
```

### Step 5 — Say It in a Sentence

Put the three numbers together in plain English, the way you'd say it out loud to a colleague: *"My address is ___, my neighbours are everything that starts with ___, and anything outside that goes through ___."*

Your plain-English summary:

```
My address is 10.20.5.1 my neighbors are everything that starts with 10.20.5 and anything outside that goes through 10.20.5.1
```

---

## Part B — Can I Get Out

> **Two-tab workflow.** Keep this worksheet in one browser tab and open the CLI Simulator in a second tab (right-click the link → *Open in new tab*). Run a step in the CLI Simulator tab, switch back here, record it, then go back. Save Progress here as you go — the CLI Simulator does not save your worksheet answers.

You know your own address. Now find out whether anything can hear you. `ping` is the simplest question a network tool can ask: **are you alive, and how long did it take you to answer?** It sends a small packet, waits for a reply, and reports the round trip.

The order here matters, and it's the same order you'll use for the rest of your career: **check the near thing before the far thing.** Your gateway is the nearest thing outside your own machine, so it goes first.

### Step 1 — Ping Your Gateway

Ping the default gateway address you recorded in Part A, Step 4 — `10.20.5.1`.

Command you ran:

```
ping 10.20.5.1
```

Output (the full ping result, including the summary line):

```
PING 10.20.5.1 (10.20.5.1) 56(84) bytes of data.
64 bytes from grid-gateway (10.20.5.1): icmp_seq=1 ttl=64 time=1.000 ms
64 bytes from grid-gateway (10.20.5.1): icmp_seq=2 ttl=64 time=1.200 ms
64 bytes from grid-gateway (10.20.5.1): icmp_seq=3 ttl=64 time=1.100 ms
64 bytes from grid-gateway (10.20.5.1): icmp_seq=4 ttl=64 time=1.300 ms

--- 10.20.5.1 ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3005ms
rtt min/avg/max/mdev = 1.000/1.150/1.300/0.100 ms
```

### Step 2 — Read the Result

Two numbers in that output tell the story. **Packet loss** tells you whether the replies came back at all. **Time** (latency, measured in milliseconds) tells you how long each round trip took.

How many packets you sent, how many came back, and the typical round-trip time:

```
4 packets sent and 4 packets received time- 3005ms
```

### Step 3 — Ping a Machine by Name

> **Run →** `ping foundry-archive.grid.local` · **Look for →** the number on the first line, then the loss and time in the summary · **Record →** the command and the full output below

Now reach a different machine on your own subnet — and this time, use a **name** instead of a number. Ping `foundry-archive.grid.local`. Its address, `10.20.5.20`, starts with the same `10.20.5.` as yours, so it is one of your `/24` neighbours: your workstation reaches it directly, without going through the gateway. (You'll see a host that *is* past the gateway — `cloud-heights.grid.local` — in Lab 02.) (Yes, that's the Foundry District's archive server from Module 1. It's still running, and now you can see it from the network side.)

Command you ran:

```
ping foundry-archive.grid.local
```

Output (the full ping result):

```
PING foundry-archive.grid.local (10.20.5.20) 56(84) bytes of data.
64 bytes from foundry-archive.grid.local (10.20.5.20): icmp_seq=1 ttl=64 time=2.000 ms
64 bytes from foundry-archive.grid.local (10.20.5.20): icmp_seq=2 ttl=64 time=2.200 ms
64 bytes from foundry-archive.grid.local (10.20.5.20): icmp_seq=3 ttl=64 time=2.100 ms
64 bytes from foundry-archive.grid.local (10.20.5.20): icmp_seq=4 ttl=64 time=2.300 ms

--- foundry-archive.grid.local ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3005ms
rtt min/avg/max/mdev = 2.000/2.150/2.300/0.100 ms
```

### Step 4 — Note What Ping Quietly Did For You

Look at the very first line of your Step 3 output. You typed a *name*, but ping printed a *number*. Something translated one into the other before a single packet went anywhere — and it did it so fast you'd have missed it if nobody pointed it out.

The number ping showed for `foundry-archive.grid.local`, and its packet loss and latency:

```
10.20.5.20 0% packets loss, time 3005ms
```

---

## Part C — Name to Number

Part B, Step 4 left a question hanging: *who translated that name?* This part answers it, and then proves the answer is true. This is the most important thing in this lab — don't rush it.

### Step 1 — Ask the Directory Board Directly

`dig` is the tool that does out loud what ping did silently: it asks the DNS server to turn a name into a number, and it shows you the whole conversation. Run `dig foundry-archive.grid.local`.

Command you ran:

```
dig foundry-archive.grid.local
```

Output (the full `dig` result):

```
; <<>> DiG 9.18.24 <<>> foundry-archive.grid.local
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 41207
;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 0

;; QUESTION SECTION:
;foundry-archive.grid.local.			IN	A

;; ANSWER SECTION:
foundry-archive.grid.local.	3600	IN	A	10.20.5.20

;; Query time: 1 msec
;; SERVER: 10.20.5.10#53(10.20.5.10)
```

### Step 2 — Record the A Record

Find the **ANSWER SECTION** in your output. The line there ending in `A` followed by an address is the **A record** — the mapping from the name you asked about to the IPv4 address that name points to.

The name you looked up and the IP address its A record returned:

```
;foundry-archive.grid.local.
10.20.5.20
```

### Step 3 — Record Who Answered

Now scroll to the bottom of your `dig` output and find the **SERVER** line. This tells you *which machine gave you the answer* and *which port the question went to*. That machine is The Grid's DNS server — the Directory Board from Lesson 2. The port number after the `#` is the standard port for DNS.

The answering server's address and the port number:

```
SERVER: 10.20.5.10#53(10.20.5.10)
```

### Step 4 — Prove It Yourself

Here's the test. In Part B you pinged the archive **by name** and got replies. Now ping it **by the number** you just found in the A record — `10.20.5.20` — and compare.

Command you ran:

```
ping 10.20.5.20
```

Output (the full ping result):

```
PING 10.20.5.20 (10.20.5.20) 56(84) bytes of data.
64 bytes from foundry-archive.grid.local (10.20.5.20): icmp_seq=1 ttl=64 time=2.000 ms
64 bytes from foundry-archive.grid.local (10.20.5.20): icmp_seq=2 ttl=64 time=2.200 ms
64 bytes from foundry-archive.grid.local (10.20.5.20): icmp_seq=3 ttl=64 time=2.100 ms
64 bytes from foundry-archive.grid.local (10.20.5.20): icmp_seq=4 ttl=64 time=2.300 ms

--- 10.20.5.20 ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3005ms
rtt min/avg/max/mdev = 2.000/2.150/2.300/0.100 ms
```

### Step 5 — The Aha: The Name and the Number Are the Same Place

Stop and look at what you're holding.

You pinged `foundry-archive.grid.local` and got replies from **10.20.5.20**. You then pinged **10.20.5.20** directly and got the same replies, from the same machine, at the same speed. Two different-looking commands, one single destination.

**The name is not a different thing from the number. The name is a label on the number.** DNS is nothing more than the lookup that swaps one for the other, and you just watched the whole exchange happen — the question (`dig`), the answer (the A record), the machine that answered it (10.20.5.10), and the proof that the answer was correct (identical ping results).

This is why network troubleshooting has a *by name* test and a *by IP* test, and why they are not the same test. Hold onto that. Lab 02 is built entirely on the difference between them.

Write the aha in your own words — what did the two pings prove, and what did `dig` show you in between them?

Your explanation:

```
The two pings proved that a name and an IP address point to the exact same machine and yield identical results. meanwhile, dig showed how DNS translates the domain name into its corresponding IP address.
```

### Step 6 — Capture Your Session (OPTIONAL screenshot)

Take a screenshot of your simulator session showing your Part C sequence — the `dig` output and both pings. Name it `cli-grid-address.png`.

**This screenshot is optional this week.** There's no flagship deliverable due in Week 5, so nothing is riding on it. Do it anyway if you can: Week 7's Deliverable 2 will ask for evidence exactly like this, and building the habit now — capture the moment the tool proved something — is genuinely easier than building it under deadline. Treat it as portfolio practice. If you do capture it, the GitHub Commit section below shows you how to upload it and record its filename.

---

## Part D — The Windows Side

Every question you asked in Parts A through C has a Windows answer. The words change; the questions don't. Switch to **The Grid — Finding Your Place (PowerShell)** in the scenario list for this part.

### Step 1 — Your Address, Windows Edition

Run `ipconfig`. Notice that Windows hands you all three numbers in one place — IPv4 address, subnet mask, and default gateway — spelled out in words instead of slash notation.

Command you ran:

```
ipconfig
```

Output (the full `ipconfig` result):

```
Windows IP Configuration

Ethernet adapter Ethernet0:

   Connection-specific DNS Suffix  . : grid.local
   IPv4 Address. . . . . . . . . . . : 10.20.5.42
   Subnet Mask . . . . . . . . . . . : 255.255.255.0
   Default Gateway . . . . . . . . . : 10.20.5.1
```

### Step 2 — Compare the Two Views

Compare your `ipconfig` output to your `ip addr` output from Part A. The values should be identical — same machine, same address plate. What's different is how each tool *presents* it.

One thing `ipconfig` showed more plainly than `ip addr`, and one thing that was the same in both:

```
IP config showed the network values clearly in words rather than using slash notation while the IP address remained the exact same on both.
```

### Step 3 — Look Up the Name, Windows Edition

Run `Resolve-DnsName foundry-archive.grid.local`. This is PowerShell's `dig`.

Command you ran:

```
Resolve-DnsName foundry-archive.grid.local
```

Output (the full result):

```
Name                                     Type   TTL   Section    IPAddress
----                                     ----   ---   -------    ---------
foundry-archive.grid.local               A      3600  Answer     10.20.5.20
```

The IP address it returned:

```
10.20.5.20
```

### Step 4 — Test Reachability, Windows Edition

Run `Test-Connection foundry-archive.grid.local`. This is PowerShell's `ping`, and it returns its results as a tidy table rather than a running list of replies.

Command you ran:

```
Test-Connection foundry-archive.grid.local
```

Output (the full result):

```
Source          Destination                    IPV4Address      Bytes    Time(ms)
------          -----------                    -----------      -----    --------
ivy-workstation foundry-archive.grid.local     10.20.5.20       32       2
ivy-workstation foundry-archive.grid.local     10.20.5.20       32       2
ivy-workstation foundry-archive.grid.local     10.20.5.20       32       2
ivy-workstation foundry-archive.grid.local     10.20.5.20       32       2
```

### Step 5 — Build Your Own Translation Table

You now have both halves of the same toolkit. Fill in the pairs from memory of what you just ran — this table is worth keeping.

Your bash-to-PowerShell pairs (show my address / test reachability / look up a name):

```
show my address: ip a (Bash)/ ipconfig (powershell)
Test reachability: Ping (Bash)/ ping (powershell)
Look up a name: dig (Bash)/ Resolve-DnsName (powershell)
```

---

## Analysis Questions

**Analysis Question 1.** In Part C you proved that pinging `foundry-archive.grid.local` and pinging `10.20.5.20` reach the same machine. If the number works perfectly well on its own, why does The Grid bother having names at all? Give at least two distinct reasons, and think beyond "names are easier to remember." *(Minimum 3 sentences.)*

```
First, domain names provide abstraction so that if a server's underlying IP address changes due to infrastructure updates, users and services can still connect using the same static name. Second, security features like TLS/SSL certificates rely on domain names rather than raw IP addresses to validate identity and establish encrypted connections. Third, names enable load balancing and failover, allowing a single domain name to seamlessly route traffic across multiple backend servers.

```

**Analysis Question 2.** Part A gave you three numbers: your address, your subnet mask, and your default gateway. Explain what the subnet mask lets your machine decide, and what your machine does with a packet when the answer to that decision is "not a neighbour." *(Minimum 3 sentences.)*

```
The subnet mask allows your machine to determine whether a target IP address belongs to the local network (a neighbor) or a remote network. It calculates this by performing a logical comparison between the IP addresses and the subnet mask to separate the network portion from the host portion. When the answer to that decision is "not a neighbor," your machine packages the frame and sends it to the default gateway so it can be routed outside the local subnet.

```

**Analysis Question 3.** You asked the same three questions twice — once in bash, once in PowerShell — and got the same three answers. Why is it worth learning both sets of words rather than picking a favourite and ignoring the other? Answer in terms of the job, not the exam. *(Minimum 2 sentences.)*

```
In professional IT and cybersecurity roles, you will constantly encounter diverse environments that utilize both Linux and Windows operating systems. Knowing both toolsets ensures you can effectively troubleshoot, audit, and administer any system regardless of the platform you encounter on the job.

```

---

## Submission Checklist

- [x] Full `ip addr` output recorded (Part A, Step 1)

- [x] IPv4 address recorded (Part A, Step 2)

- [x] Prefix length translated to a subnet mask (Part A, Step 3)

- [x] `ip route` run, output recorded, and default gateway identified (Part A, Step 4)

- [x] Address, mask, and gateway summarised in plain English (Part A, Step 5)

- [x] Gateway pinged, output recorded, loss and latency read (Part B, Steps 1–2)

- [x] `foundry-archive.grid.local` pinged by name, output recorded (Part B, Steps 3–4)

- [x] `dig` run; A record and answering server + port recorded (Part C, Steps 1–3)

- [x] `10.20.5.20` pinged directly and compared to the by-name ping (Part C, Step 4)

- [x] The name-equals-number aha explained in your own words (Part C, Step 5)

- [ ] *Optional:* `cli-grid-address.png` captured, uploaded to `assets/screenshots/week-05/`, and its filename recorded (Part C, Step 6)

- [x] `ipconfig`, `Resolve-DnsName`, and `Test-Connection` all run and recorded (Part D)

- [x] bash-to-PowerShell translation table completed (Part D, Step 5)

- [x] All three Analysis Questions answered (minimum sentence counts met)

- [x] This file is committed to your portfolio repo at `week-05/labs/lab-01-finding-your-place.md`

---

## GitHub Commit Subsection

This lab's written answers are submitted through the **CyberFoundations Lab Portal**, the same way as Week 4.

1. Go to the CyberFoundations Lab Portal and sign in.
2. Open **Week 5 → Lab 01: Finding Your Place on the Grid**.
3. Fill in the worksheet fields — they match the commands, outputs, and questions in this file, in the same order.
4. Connect your GitHub account if you haven't already (one-time setup), and select your portfolio repo.
5. Click **Submit to GitHub**. The Portal commits the completed file to `week-05/labs/lab-01-finding-your-place.md` for you.

**📸 OPTIONAL — portfolio practice.** No deliverable depends on this screenshot in Week 5. If you're building the habit ahead of Week 7, here's the full method:

1. Go to your portfolio repository on GitHub.com and navigate to `assets/screenshots/week-05/` (create the folder if this is your first Week 5 screenshot).
2. Click **Add file → Upload files**, drag in your screenshot, named `cli-grid-address.png` (lowercase, hyphens, no spaces).
3. Scroll down and click **Commit changes**.
4. Click the uploaded image's filename to open it and confirm it displays correctly.
5. Record the filename below so your grader knows to look for it.

The screenshot filename you uploaded:

```
(type the filename here, e.g. cli-grid-address.png)
```

Your screenshot lives in `assets/screenshots/week-05/` in your repository, alongside the rest of your Week 5 evidence. It does not need to be linked inside this worksheet.

---

*CyberVisionaries Institute · Cyber Foundations · Tier I*
