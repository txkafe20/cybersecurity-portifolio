# Week 6 Lab 05 — Layer Detective

**Student Name:** Tiwonge Kafera

**Date Completed:** 11/3/26

**Module:** 2 — Networking & Cloud Foundations | **Week:** 6  
**Submission Path:** `week-06/labs/lab-05-layer-detective.md`

---

## Overview

**This is a SHORT lab — 20 to 30 minutes — and it needs no VM.** No Cloud Heights session, no simulator, no screenshot. This is a thinking lab: you take the evidence you have already collected in Weeks 5 and 6 and sort it into layers.

This is an **independent** lab.

---

## Lab Environment / Pre-Lab Check

| Component | Details |
|---|---|
| Environment | This worksheet only — nothing to start, nothing to connect to |
| Prerequisite | Week 5 labs and Week 6 Labs 01–04 |
| Screenshot | None required |

---

## Part A — The Seven-Row Table

Fill in every row. For the last column, name one **real thing you personally saw** in Weeks 5–6 that belongs at that layer.

| # | Layer name | One-line job | Real thing from Weeks 5–6 |
| --- | --- | --- | --- |
| 7 | Application | provides network services directly to user application and browsers | accessing the lab portal web page via HTTPS in the browser |
| 6 | Presentation | translates encrypts and compress data between application and network formats | TLS /HTTPS encryption |
| 5 | Session | Manages and maintains communication sessions between application | Maintaining active web portal session |
| 4 | Transport | Ensures reliable end to end data delivery, flow, control and error recovery | TCP segments managing reliable connection states |
| 3 | Network | Handles logical addressing like packet forwarding and routing | Private IP address10.60.6.42, subnet 10.60.6.0/26 and IP route default gateway (10.60.6.1) |
| 2 | Data Link | Facilitates nod to node data transfer accross the local physical network using MAC addresses | Local network interface |
| 1 | Physical | Transmit raw binary bits over physical media | Laptop |

---

## Part B — Case Files

For each case, name the layer where the problem lives, and name the evidence proving the layers **below** it were already working.

### Case File 1 — The Name That Went Nowhere

A hostname lookup fails, but pinging the machine's IP address directly succeeds.

Layer:

```
Application Layer
```

Evidence that the layers below were working:

```
 Because pinging the machine's IP address directly succeeded, all underlying network layers, including Layer 1 (Physical), Layer 2 (Data Link), Layer 3 (Network IP routing), and Layer 4 (Transport ICMP delivery), must be fully operational. The failure is isolated to name resolution (DNS), meaning the lower layers are healthy and passing packets successfully.

```

### Case File 2 — Permission Denied

`ssh` to a host returns `Permission denied` after a password prompt.

Layer:

```
Session
```

Evidence that the layers below were working:

```
 Because the ssh command successfully reached the remote host and prompted you for a password, all lower layers, including Layer 1 through Layer 4 (Physical, Data Link, Network routing, and Transport TCP connection establishment). successfully transported your packets across the network. The connection failed at the authentication stage because of invalid credentials or permissions, proving that the underlying network pathway and transport services were completely functional.

```

### Case File 3 — The Cable Story

A machine reports no link on its interface and has no address at all.

Layer:

```
Physical layer
```

Evidence and reasoning:

```
 The machine reports no link on its interface and has no IP address assigned. This indicates a physical layer failure, such as an unplugged, damaged, or unpowered cable or transceiver—preventing the device from establishing any baseline electrical or optical signal.

```

### Case File 4 — Ping Works, The Page Does Not

`ping` to a server succeeds, but `curl http://<that server>` returns nothing useful.

Layer:

```
Application
```

Evidence that the layers below were working:

```
 Because ping (using ICMP at Layer 3/4) successfully reaches the server, all underlying layers including Physical, Data Link, Network routing, and Transport, are fully operational and passing packets. The failure occurs at the application layer because the web server daemon is either not running, misconfigured, or blocking HTTP/HTTPS traffic on the specific port required for the web page to load.

```

### Case File 5 — Wrong Neighbourhood

A machine has an address, but its default route points somewhere that cannot forward its traffic.

Layer:

```
Network
```

Evidence and reasoning:

```
 The issue involves routing, IP addresses, and gateways, which operate entirely at Layer 3 of the OSI model. Since the default route points to an incorrect or unreachable gateway that cannot properly forward traffic off the local network, packets fail to route correctly even though the machine itself has an IP address assigned.

```

---

## Part C — The Silent Gateway Case

In Lab 03 the Azure default gateway did not answer your ping. However, your VM had a valid default route configured, and your local communication with the Grid Beacon — the ping replies, the HTTP banner, and `TRACE ID: CF-NET-0604` — succeeded.

A failed gateway ping is one piece of evidence — not automatically proof of a gateway or network failure. But the evidence you weigh against it has to be the right kind of evidence.

The Grid Beacon at `10.60.6.4` sits on the same local subnet as your VM (`10.60.6.0/26`). Reaching it proves **local-subnet connectivity** — that traffic never crosses the default gateway, so beacon success alone cannot prove the gateway forwarded anything. Your `ip route` output proves a **default route is configured** — your VM knows where it intends to send non-local traffic — but it does not prove the gateway forwarded that traffic. The evidence that demonstrates the **default path is functioning** is successful communication with a destination outside `10.60.6.0/26`, such as the outbound internet access through NAT that you examined in Lab 04.

### Step 1 — Rule on the Case

Is the failed gateway ping enough evidence to declare a network-layer failure? Explain your answer using the other evidence you collected. In your response, distinguish between:

- evidence that proves **local-subnet connectivity**
- evidence that proves a **default route is configured**
- evidence that supports **successful off-subnet connectivity**

```
A failed gateway ping is not enough evidence to declare a network-layer failure. Local-subnet connectivity is proven by successfully reaching the Grid Beacon at 10.60.6.4 since it resides on the same local subnet (10.60.6.0/26). Furthermore, a default route is confirmed as configured via ip route, and successful off-subnet connectivity is proven by outbound internet access reaching external destinations outside the local subnet.

```

### Step 2 — Name the Correct Conclusion

For each of these four results, state what it actually proves: the Grid Beacon at `10.60.6.4` answering, the default route shown by `ip route`, a successful connection to a destination outside your local subnet, and the gateway's failed ping. Then state the rule you would give a junior colleague about the difference between an observation ("the gateway did not answer my ICMP probe") and a diagnosis ("the gateway is broken"):

```
Answering the Grid Beacon at 10.60.6.4 proves local-subnet communication works, while the default route shown by ip route confirms the routing table is set up to handle traffic. A successful connection to a destination outside your local subnet proves that off-subnet traffic is successfully reaching the internet despite the gateway's failed ping. The rule you should give a junior colleague is that an observation is a raw factual measurement (e.g., "the gateway did not answer my ICMP probe"), whereas a diagnosis is a judgment or conclusion drawn from it (e.g., "the gateway is broken"), and you must never confuse the two without corroborating evidence.

```

---

## Part D — Two Models, One Job

The OSI model has seven layers. The practical TCP/IP model most engineers speak day to day has four or five.

### Step 1 — Map Them

Briefly show how the seven OSI layers collapse into the practical model:

```
Application Layer (TCP/IP) combines OSI Layers 7, 6, and 5 (Application, Presentation, and Session).
Transport Layer (TCP/IP) corresponds directly to OSI Layer 4 (Transport).
Internet Layer (TCP/IP) corresponds directly to OSI Layer 3 (Network).
Network Access / Link Layer (TCP/IP) combines OSI Layers 2 and 1 (Data Link and Physical).

```

### Step 2 — When Each Is Useful

Explain when the seven-layer vocabulary helps and when the practical model is the better tool:

```
The seven-layer OSI vocabulary is most useful during detailed troubleshooting, security analysis, or when isolating precise protocol anomalies because its granular breakdown helps pinpoint exactly where a failure occurs. On the other hand, the practical four-layer TCP/IP model is a better tool for day-to-day network architecture, engineering, and configuration since it closely aligns with real-world protocol suites like IP and TCP. Using both models allows engineers to switch between high-level architectural design and deep, step-by-step diagnostic triage.

```

---

## Analysis Questions

**Analysis Question 1.** Explain the Ladder Rule using layer language. What does "test the near thing first" mean when the rungs are layers? *(Minimum 3 sentences.)*

```
The Ladder Rule states that troubleshooting network issues should follow a sequential, layered approach through the OSI model from the bottom up or top down. "Test the near thing first" means you should verify the lowest, closest layer components such as physical cables, local interfaces, or the local subnet gateway before moving on to complex remote services or higher layers. By validating each rung of the ladder systematically, you ensure that the foundational layers are fully operational before blaming upper-layer services.

```

**Analysis Question 2.** Why is "which layer is this?" a faster question than "what is broken?" when you are under pressure? *(Minimum 3 sentences.)*

```
Asking "which layer is this?" is much faster than asking "what is broken?" because the OSI model narrows down the scope of potential failures into distinct, manageable categories rather than an infinite list of possibilities. Instead of guessing randomly across hardware, code, and configurations, identifying the layer immediately points you to the specific diagnostic tools and protocols associated with that level. This structured triage under pressure prevents wasted time and guides you straight to the root cause.

```

**Analysis Question 3.** Pick one case file from Part B and describe the very next command you would run to confirm your ruling, and what result would change your mind. *(Minimum 2 sentences.)*

```
For Case File 1 (The Name That Went Nowhere): The next command to run would be nslookup or dig targeting the hostname to confirm whether DNS resolution is failing. If the DNS lookup successfully returned the correct IP address matching the machine you successfully pinged, that result would change our ruling from a Layer 7/DNS failure to a healthy status where everything is working.

```

---

## Submission Checklist

- [x] All seven rows of the OSI table completed with a real Week 5–6 anchor each (Part A)

- [x] All five case files given a layer and supporting evidence (Part B)

- [x] Silent gateway case ruled on correctly (Part C)

- [x] OSI vs. practical TCP/IP model compared (Part D)

- [x] All three Analysis Questions answered (minimum sentence counts met)

- [x] No screenshot required for this lab

- [x] This file is committed to your portfolio repo at `week-06/labs/lab-05-layer-detective.md`

---

## GitHub Commit Subsection

1. Open **Week 6 → Lab 05: Layer Detective** in the Lab Portal.
2. Fill in the worksheet fields.
3. Click **Submit to GitHub** — the Portal commits to `week-06/labs/lab-05-layer-detective.md`.

---

*CyberVisionaries Institute · Cyber Foundations · Tier I*
