# Week 6 Lab 04 — Reading the Blueprints

**Student Name:** Tiwonge Kafera

**Date Completed:** 11/3/26

**Module:** 2 — Networking & Cloud Foundations | **Week:** 6  
**Submission Path:** `week-06/labs/lab-04-reading-the-blueprints.md`

---

> ### 🔒 Cloud Heights Security Rule
> Your Bastion link and Cloud Heights password are **private access credentials**. Never paste either into a worksheet, screenshot, GitHub repository, Circle post, or chat message. When taking screenshots, crop out the browser address bar and all login information.

---

## Overview

**This is a SHORT lab — 15 to 20 minutes.** It is deliberately small. You already have the commands; this lab is about matching a drawing to reality.

The **Cloud Heights Network Blueprint** is displayed at the top of this lab page in the portal. Everything you write about the network's architecture comes from that blueprint or from your own machine — never from a guess.

---

## Lab Environment / Pre-Lab Check

| Component | Details |
|---|---|
| Environment | Cloud Heights — live Ubuntu 22.04 VM, reached through Azure Bastion |
| Source of truth | The Cloud Heights Network Blueprint shown at the top of this lab page |
| Commands used | `ip addr`, `ip route` |
| Known value | Student subnet: **`10.60.6.0/26`** |

---

## Part A — Read the Drawing

### Step 1 — Record the Architecture Values

From the blueprint at the top of this page, record each value **exactly as drawn**. If a value is not shown on the blueprint, write "not shown on blueprint" — do not guess.

| Item | Value from the blueprint |
| --- | --- |
| VNet name | Vnet-cf-labs |
| VNet address space | 10.60.6.0/24 |
| Student subnet range | 10.60.6.0/26 |

---

## Part B — Verify Against Your Own Machine

### Step 1 — Confirm Your Address Lives in the Subnet

Run `ip addr` and find your private IPv4 address.

Command and output:

```
ip addr

1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group def
ault qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host 
       valid_lft forever preferred_lft forever
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP group d
efault qlen 1000
    link/ether 7c:ed:8d:57:18:0b brd ff:ff:ff:ff:ff:ff
    inet 10.60.6.42/26 metric 100 brd 10.60.6.63 scope global eth0
       valid_lft forever preferred_lft forever
    inet6 fe80::7eed:8dff:fe57:180b/64 scope link 
       valid_lft forever preferred_lft forever
3: enP20808s1: <BROADCAST,MULTICAST,SLAVE,UP,LOWER_UP> mtu 1500 qdisc mq mast
er eth0 state UP group default qlen 1000
    link/ether 7c:ed:8d:57:18:0b brd ff:ff:ff:ff:ff:ff
    altname enP20808p0s2
```

Your private IP:

```
127.0.0.1
```

Explain how you know your address falls inside `10.60.6.0/26` — what range does that prefix actually cover:

```
My IP address is 10.60.6.42, which falls directly within the 10.60.6.0/26 subnet. A /26 prefix indicates a subnet mask of 255.255.255.192 (leaving 6 host bits), which covers a total block of 64 addresses ranging from 10.60.6.0 to 10.60.6.63, placing 10.60.6.42 safely inside the usable host range.

```

### Step 2 — Confirm Route Behaviour

Run `ip route`.

Command and output:

```
ip route

 ip route
default via 10.60.6.1 dev eth0 proto dhcp src 10.60.6.42 metric 100 
10.60.6.0/26 dev eth0 proto kernel scope link src 10.60.6.42 metric 100 
10.60.6.1 dev eth0 proto dhcp scope link src 10.60.6.42 metric 100 
168.63.129.16 via 10.60.6.1 dev eth0 proto dhcp src 10.60.6.42 metric 100 
169.254.169.254 via 10.60.6.1 dev eth0 proto dhcp src 10.60.6.42 metric 100 

```

What the default route tells you about traffic that is not destined for your own subnet:

```
The defualt route tells the system that any traffic destined for an IP address outside of the local subnet 10.60.6.0/26 should be forwarded to the default gateway at 10.60.6.1 so it can be routed toward external networks or the internet
```

### Step 3 — Capture Your Evidence

**Required filename:** `blueprint-verified.png`

This must be **your own `ip addr` and `ip route` output** — not a re-screenshot of the blueprint. Crop out the address bar and any login information.

---

## Part C — How Traffic Actually Moves

### Step 1 — No Public IP

Your VM has a private address and **no public IP**. Explain what that means for who can reach it directly from the internet:

```
Since the virtual machine only has a private IP address and no public IP address, it cannot be reached directly from the public internet. External devices or users outside the network cannot initiate a connection to this VM because private IP addresses are not routable on the public internet.

```

### Step 2 — Outbound vs. Inbound

Outbound internet traffic from your VM leaves through address **translation (NAT)**. Inbound access for you arrives through **Azure Bastion**, not through a public address on the VM.

Explain both directions in your own words:

```
Outbound traffic leaves the VM using Network Address Translation (NAT) to communicate with external resources or the internet. Inbound access to the VM arrives through Azure Bastion rather than a direct public IP address on the machine itself. This means administrative connections are safely tunneled and managed securely via the Bastion service.

```

### Step 3 — The Guard Post You Do Not Touch Yet

Each student machine sits behind its own **network security group** — a per-student guard post that decides what traffic is allowed in.

**In Week 6 you do not configure it.** Week 7 is when you take control of those rules.

Write one sentence naming what the guard post does and one sentence stating what you are *not* doing with it this week:

```
The network security group act as a per-student guard post that decides what traffic is allowed in or out. I am not configuring or modifying those security rules this week
```

---

## Analysis Questions

**Analysis Question 1.** Why would an organization put every student machine in one small subnet instead of giving each machine a public address? *(Minimum 3 sentences.)*

```
Placing every student machine into a small private subnet instead of assigning a public address conserves scarce IPv4 address space by allowing multiple internal devices to share a single public gateway through Network Address Translation. This architecture also enhances security by hiding internal machines from direct exposure on the public internet, preventing external entities from initiating unsolicited connections. Furthermore, grouping machines into isolated subnets makes it easier for administrators to implement centralized network policies, monitoring, and perimeter filtering.

```

**Analysis Question 2.** Segmentation means separating a network into parts that cannot freely reach each other. Give one concrete benefit of segmentation during a security incident. *(Minimum 3 sentences.)*

```
Network segmentation creates logical or physical boundaries that restrict lateral movement across a network. During a security incident where an attacker compromises a single host, segmentation prevents the threat actor from easily pivoting to access other sensitive systems or databases on the network. This containment limits the blast radius of the breach, giving security teams valuable time to isolate the compromised segment and mitigate the threat before it impacts the entire organization.

```

**Analysis Question 3.** A diagram and a live machine disagree about an address range. Which do you trust, what do you do next, and why? *(Minimum 2 sentences.)*

```
In a disagreement between documentation and a live system, I would generally trust the live machine because it reflects the current reality and actual configuration of the environment. Next, I would investigate the discrepancy by checking recent configuration changes or logs to determine why the documentation or blueprint is out of sync.

```

---

## Submission Checklist

- [x] VNet name, address space, and subnet range recorded from the blueprint (Part A)

- [x] `ip addr` run and own private IP confirmed inside `10.60.6.0/26` (Part B, Step 1)

- [x] `ip route` run and default route behaviour explained (Part B, Step 2)

- [x] `blueprint-verified.png` captured from your own terminal, cropped, uploaded to `assets/screenshots/week-06/` (Part B, Step 3)

- [x] Private address / NAT / Bastion explained (Part C, Steps 1–2)

- [x] Per-student guard post identified — and explicitly not configured this week (Part C, Step 3)

- [x] All three Analysis Questions answered (minimum sentence counts met)

- [x] This file is committed to your portfolio repo at `week-06/labs/lab-04-reading-the-blueprints.md`

---

## GitHub Commit Subsection

1. Open **Week 6 → Lab 04: Reading the Blueprints** in the Lab Portal.
2. Fill in the worksheet fields and upload `blueprint-verified.png` to `assets/screenshots/week-06/`.
3. Click **Submit to GitHub** — the Portal commits to `week-06/labs/lab-04-reading-the-blueprints.md`.

---

*CyberVisionaries Institute · Cyber Foundations · Tier I*
