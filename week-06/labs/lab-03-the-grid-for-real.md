# Week 6 Lab 03 — The Grid, For Real

**Student Name:** Tiwonge Kafera

**Date Completed:** 11/1/26

**Module:** 2 — Networking & Cloud Foundations | **Week:** 6  
**Submission Path:** `week-06/labs/lab-03-the-grid-for-real.md`

---

> ### 🔒 Cloud Heights Security Rule
> Your Bastion link and Cloud Heights password are **private access credentials**. Never paste either into a worksheet, screenshot, GitHub repository, Circle post, or chat message. When taking screenshots, crop out the browser address bar and all login information.

---

## Overview

In Week 5 you ran `ip addr`, `ip route`, `ping`, and `traceroute` in a simulator that always behaved. Today you run the same toolkit against real cloud infrastructure that does **not** always behave the way the textbook implies — and you learn to tell "broken" apart from "normal."

This is an **independent** lab. It tells you what to accomplish; you choose the commands. Expect about 40 minutes.

---

## Lab Environment / Pre-Lab Check

| Component | Details |
|---|---|
| Environment | Cloud Heights — live Ubuntu 22.04 VM, reached through Azure Bastion |
| Commands used | `ip addr`, `ip route`, `ping`, `traceroute`, `curl` |
| Known-good target | **Grid Beacon — `10.60.6.4`** |
| Prerequisite | Week 6 Labs 01–02 |

---

## Part A — Where You Actually Are

### Step 1 — Read Your Own Address

Run the command that lists your interfaces and addresses.

Command and output:

```
analyst@cf-student-35:~$ ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host 
       valid_lft forever preferred_lft forever
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP group default qlen 1000
    link/ether 7c:ed:8d:57:18:0b brd ff:ff:ff:ff:ff:ff
    inet 10.60.6.42/26 metric 100 brd 10.60.6.63 scope global eth0
       valid_lft forever preferred_lft forever
    inet6 fe80::7eed:8dff:fe57:180b/64 scope link 
       valid_lft forever preferred_lft forever
3: enP52404s1: <BROADCAST,MULTICAST,SLAVE,UP,LOWER_UP> mtu 1500 qdisc mq master eth0 state UP group default qlen 1000
    link/ether 7c:ed:8d:57:18:0b brd ff:ff:ff:ff:ff:ff
    altname enP52404p0s2
```

Your private IPv4 address and prefix length:

```
10.60.6.42/26
```

### Step 2 — Read Your Route

Run the command that shows the routing table.

Command and output:

```
analyst@cf-student-35:~$ ip route
default via 10.60.6.1 dev eth0 proto dhcp src 10.60.6.42 metric 100 
10.60.6.0/26 dev eth0 proto kernel scope link src 10.60.6.42 metric 100 
10.60.6.1 dev eth0 proto dhcp scope link src 10.60.6.42 metric 100 
168.63.129.16 via 10.60.6.1 dev eth0 proto dhcp src 10.60.6.42 metric 100 
169.254.169.254 via 10.60.6.1 dev eth0 proto dhcp src 10.60.6.42 metric 100 
```

Your default gateway:

```
10.60.6.1
```

### Step 3 — Compare to Week 5

Compare this live Ubuntu output to what the CLI Simulator produced in Week 5. What looks the same, what looks different, and what surprised you:

```
Comparing the live Ubuntu output to the Week 5 CLI Simulator reveals that both present network configuration details in a structured, readable text format. While the simulator used pre-scripted mock outputs, this live environment displays real-time network parameters reflecting the actual active interface and routing table. It was surprising to see how closely the real Linux command outputs match the realistic training scenarios built into the simulator.

```

---

## Part B — The Gateway That Does Not Answer

### Step 1 — Ping the Gateway

Ping the default gateway address you recorded. Let it run a few seconds, then stop it.

Command and output:

```
ping 10.60.6.1

213 packets transmitted, 0 received, 100% packet loss, time 217109ms

```

### Step 2 — Interpret It Correctly

You almost certainly got **no replies**. In Azure, the platform gateway commonly does not answer ICMP. This is **expected platform behaviour** and by itself proves nothing about whether your machine or network is broken.

Explain why "the gateway did not answer ping" is weak evidence:

```
A failed ping to a gateway is weak evidence of network failure because many cloud platforms and firewalls intentionally block ICMP traffic by default. The gateway may be actively routing production network packets normally even if security policies suppress ping echo responses. Therefore, an unbacked ping request alone cannot distinguish between an intentionally silent gateway and an actual network disruption.

```

---

## Part C — The Known-Good Target

The **Grid Beacon** at `10.60.6.4` is a machine that is known to be up and known to answer. When your first probe fails, you test against something known-good before you conclude anything.

### Step 1 — Ping the Beacon

```
ping 10.60.6.4
```
Output:

```
10.60.6.4 ping statistics ---
58 packets transmitted, 58 received, 0% packet loss, time 57077ms
rtt min/avg/max/mdev = 1.127/1.539/5.679/0.841 ms
```

### Step 2 — Trace the Path

```
traceroute 10.60.6.4
```
Output:

```
traceroute to 10.60.6.4 (10.60.6.4), 30 hops max, 60 byte packets
 1  grid-beacon.internal.cloudapp.net (10.60.6.4)  1.385 ms  1.330 ms  1.608 
ms
```

### Step 3 — Ask the Application

```
curl http://10.60.6.4
```
Output:

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>GRID BEACON | CVI CyberFoundations</title>
    <style>
        body {
            background: #071426;
            color: #d9f7ef;
            font-family: monospace;
            max-width: 850px;
            margin: 80px auto;
            padding: 30px;
        }
        .beacon {
            border: 1px solid #31d6a6;
            padding: 35px;
        }
        h1 { color: #31d6a6; }
        .label { color: #8ca8ff; }
        .status { color: #31d6a6; }
        .classified {
            margin-top: 30px;
            border-top: 1px solid #31445e;
            padding-top: 20px;
        }
    </style>
</head>
<body>
<div class="beacon">

    <h1>GRID BEACON</h1>

    <p><span class="label">NODE:</span> grid-beacon</p>
    <p><span class="label">NETWORK:</span> CVI Training Grid</p>
    <p><span class="label">STATUS:</span>
       <span class="status">ONLINE</span></p>
 <p>
        Network beacon established.<br>
        If you reached this node, your route is operational.
    </p>

    <div class="classified">
        <p>INVESTIGATION CHECKPOINT</p>

        <p>
            Observe the path that brought you here.
            The destination is only part of the story.
        </p>

        <p>TRACE ID: CF-NET-0604</p>
    </div>

</div>
</body>
</html>
```

> ### ⚠️ Grid Beacon not responding?
> The Grid Beacon is shared course infrastructure and should normally be available. First, confirm your Cloud Heights VM shows **Running** and that you completed the preceding network checks. Then retry the command once after a minute or two.
>
> If the Grid Beacon still does not respond, **stop this part of the lab and contact your instructor.** Record that the shared service was unavailable; do not treat the result as evidence that your VM or your work is incorrect.
>
> Do not change networking, NSGs, firewall rules, routes, DNS, or any Azure settings to try to reach the beacon.
>
> *Instructor note: a confirmed Grid Beacon outage is an environment issue, not a student error. Affected students may complete this portion of Lab 03 after the service is restored, with no penalty.*

### Step 4 — Record the Application Evidence

The beacon returns a banner and a trace ID. Record exactly what you received:

```
 <p>
        Network beacon established.<br>
        If you reached this node, your route is operational.
    </p>

    <div class="classified">
        <p>INVESTIGATION CHECKPOINT</p>

        <p>
            Observe the path that brought you here.
            The destination is only part of the story.
        </p>

        <p>TRACE ID: CF-NET-0604</p>
    </div>

</div>
</body>
</html>
```

Explain the difference between what the `ping` proved and what the `curl` proved:

```
The ping command tests basic network layer connectivity and packet reachability using ICMP, proving whether lower-level network routes are functioning. In contrast, the curl command tests application layer functionality by successfully interacting with an HTTP/HTTPS service, proving that web-based services and application ports are actively responding.

```

### Step 5 — Capture Your Evidence

Two screenshots, both cropped to the terminal only:

**Required filename:** `vm-toolkit-live.png` — your `ip addr` and `ip route` output

**Required filename:** `beacon-reply.png` — your beacon ping/traceroute/curl evidence

---

## Part D — Rewrite the Ladder Rule

Week 5 taught the Ladder Rule: test the near thing before the far thing. Real infrastructure adds a wrinkle — a silent rung is not automatically a broken rung.

Rewrite the Ladder Rule in your own words so that it survives real cloud infrastructure. Your version must include both **route/path evidence** and **a known-good target**:

```
When diagnosing network connectivity failures, always verify the closest infrastructure components and local pathways before troubleshooting distant endpoints or external routing tables. Real-world networks introduce silent complications where a component may appear unresponsive or unverified by standard probing tools while still functioning correctly under the hood. Therefore, a comprehensive evaluation must combine multi-hop path tracing and known-good target validation to isolate whether a failure stems from local configuration, intervening middleboxes, or the remote destination.

```

---

## Analysis Questions

**Analysis Question 1.** Your ping to the gateway failed and your ping to the beacon succeeded. What does that pair of results, taken together, prove about your machine's networking? *(Minimum 3 sentences.)*

```
This specific combination of results proves that your machine's physical network interface card (NIC), local TCP/IP stack, and device driver are fully functional because traffic successfully reached an external beacon beyond the local network. The failure to reach the default gateway typically indicates that local ICMP echo requests are being blocked by a local firewall, security software, or that the gateway is configured not to respond to pings rather than a total loss of local connectivity. Ultimately, because traffic successfully traversed outward to the beacon, your routing table and physical link layer are operational despite the gateway's lack of response.

```

**Analysis Question 2.** Why is `traceroute` useful even when `ping` already answered? What extra thing does it show you? *(Minimum 2 sentences.)*

```
While ping only tells you whether an entire end-to-end path is reachable and measures the total round-trip time, traceroute maps out every individual hop, router, and gateway that your packets traverse along the path to the destination. This granularity allows you to pinpoint the exact location of latency spikes, routing loops, or packet drops caused by specific intermediary devices rather than just knowing that a connection failed overall.

```

**Analysis Question 3.** A service is unreachable and ping to it succeeds. Where would you look next, and why is "the network is fine" an incomplete answer? *(Minimum 3 sentences.)*

```
 Since the ping succeeds, basic network connectivity and layer-3 routing between your machine and the target are operational. However, because the specific service remains unreachable, your next step should be to investigate upper-layer issues such as the application firewall, host-based firewall rules, or verifying whether the specific port and transport layer daemon (like HTTP on port 80 or SSH on port 22) are actively running and listening on the remote host. Stating that "the network is fine" is an incomplete answer because network reachability only proves that packets can travel across the wire; it does not guarantee that the application service is healthy, correctly configured, or willing to accept connections.

```

**Analysis Question 4.** Something already controls what is allowed to reach your machine in Cloud Heights. If you could decide those rules, what would you want to allow, what would you want to block, and who in an organization should get to make that decision? *(Minimum 3 sentences.)*

```
If designing the security rules for Cloud Heights, I would want to allow inbound traffic strictly required for authorized business functions, such as specific management ports or designated API endpoints, while blocking all unsolicited inbound connections and common exploit vectors by default. The decision-making authority should rest with a collaborative team consisting of security operations (SecOps) professionals, infrastructure administrators, and business stakeholders to ensure compliance, operational necessity, and least-privilege access are balanced effectively. Ultimately, security policies must align with both organizational risk tolerance and regulatory requirements rather than being left entirely to individual end-users.

```

---

## Submission Checklist

- [x] `ip addr` output recorded and own private IP/prefix identified (Part A)

- [x] `ip route` output recorded and default gateway identified (Part A)

- [x] Live output compared to the Week 5 simulator (Part A, Step 3)

- [x] Gateway pinged and the silent result interpreted correctly (Part B)

- [x] Beacon `ping`, `traceroute`, and `curl` all run and recorded (Part C)

- [x] Beacon banner and TRACE ID recorded (Part C, Step 4)

- [x] `vm-toolkit-live.png` and `beacon-reply.png` captured, cropped, uploaded to `assets/screenshots/week-06/` (Part C, Step 5)

- [x] Ladder Rule rewritten with route evidence + known-good target (Part D)

- [x] All four Analysis Questions answered (minimum sentence counts met)

- [x] This file is committed to your portfolio repo at `week-06/labs/lab-03-the-grid-for-real.md`

---

## GitHub Commit Subsection

1. Open **Week 6 → Lab 03: The Grid, For Real** in the Lab Portal.
2. Fill in the worksheet fields and upload both screenshots to `assets/screenshots/week-06/`.
3. Click **Submit to GitHub** — the Portal commits to `week-06/labs/lab-03-the-grid-for-real.md`.

---

*CyberVisionaries Institute · Cyber Foundations · Tier I*
