# Week 5 Lab 02 — The Grid Outage (CLI Simulator)

**Student Name:** Tiwonge Kafera

**Date Completed:** 10/27/26

**Module:** 2 — Networking & Cloud Foundations | **Week:** 5  
**Submission Path:** `week-05/labs/lab-02-the-grid-outage.md`

---

## The Ticket

> **TICKET #GRID-0412 — PRIORITY: HIGH**
> **Reported by:** Foundry District operations staff
> **Summary:** Foundry staff report they cannot reach the relay station. Multiple people, multiple machines, since this morning. No changes reported on their end.
> **Assigned to:** you.
>
> **You are on call.** Find out what is actually broken and write it up.

---

## Overview

Lab 01 handed you the tools. This lab hands you a problem and gets out of your way.

Somewhere on The Grid, something is down. Your job is not to guess which thing — your job is to **find out**, one rung at a time, and then write down what the evidence says. This is the first time in this course you're being asked to do real diagnostic work rather than follow a recipe, and it is a genuine skill. You already have everything you need.

**The method is the Ladder Rule from Lesson 4:**

1. **Check yourself** — do you have a valid address?
2. **Check your gateway** — can you get out of your own neighbourhood?
3. **Check the target by NAME.**
4. **Check the target by IP.**
5. **Trace the path.**

*Work outward, one rung at a time, and let the evidence pick the culprit.*

The reason the ladder exists is that instinct is a terrible investigator. Instinct wants to name a villain in the first thirty seconds — "it's DNS," "it's the firewall," "it's the router" — and then spend an hour proving itself right. Evidence is slower and it is correct. **Do not decide what is broken until Part E.** Run the rungs in order, record what each one actually said, and let the finding assemble itself.

You will also be told, partway through, what somebody else thinks the problem is. Treat that the way you'd treat any other claim: test it.

---

## Lab Environment / Pre-Lab Check

| Component | Details |
|---|---|
| Environment | CyberFoundations CLI Simulator (browser-based, inside the Lab Portal) |
| Shell | **bash** throughout this lab |
| Prerequisite | Week 5, Lessons 1–4 completed; Lab 01 completed first (this lab assumes those tools) |
| Commands used | `ip addr`, `ip route`, `ping`, `dig`, `traceroute` |

**Before you start:** log into the Lab Portal, open **Week 5 → CLI Simulator**, and load the **"The Grid — Outage Response (Bash)"** scenario. It boots you into the same workstation you used in Lab 01, on the same segment, with the same gateway. You didn't contact the relay station in Lab 01, so you have no earlier result to compare it with — start from evidence, not from assumptions about what changed.

> **Two-tab workflow.** Keep this worksheet in one browser tab and open the CLI Simulator in a second tab (right-click the link → *Open in new tab*). Run a step in the CLI Simulator tab, switch back here, record it, then go back. Save Progress here as you go — the CLI Simulator does not save your worksheet answers.

**What these tools can and cannot prove.** Every test this lab uses answers one narrow question. No ICMP reply means *no reply came back to that test* — not proof that the machine is powered off. A row of `* * *` in a trace means *no probe reply came back for that hop* — not proof of exactly where or what the fault is. Write your conclusion at the strength your evidence supports.

---

## Part A — Establish Your Baseline

Rungs 1 and 2 of the ladder. It is tempting to skip straight to the thing users complained about — resist that. If you don't establish that *your own machine and your own way out are healthy*, then every failure you find afterwards is ambiguous. You won't know whether the target is broken or whether you are.

### Step 1 — Check Yourself

Confirm your workstation still holds a valid address on The Grid, and that you know where its traffic leaves from.

Run `ip addr` for your address, then `ip route` for your gateway — the same two commands you used in Lab 01. (Remember: addresses and routes live in different places, so it takes both.)

The full `ip addr` output:

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

The full `ip route` output:

```
default via 10.20.5.1 dev eth0
10.20.5.0/24 dev eth0 proto kernel scope link src 10.20.5.42
```

Your IPv4 address and default gateway, pulled out of those two results:

```
10.20.5.42
deafult gateway- 10.20.5.1
```

### Step 2 — Check Your Gateway

Ping your default gateway. This is the door out of your neighbourhood — if this fails, nothing past it means anything.

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

### Step 3 — State What the Baseline Proves

Two healthy results is not "nothing happened." It's a finding: it makes your own address and your local way out unlikely explanations before you've touched the target once. It does not rule out every possible network problem — just the ones on your side of the door.

What your baseline rules out, and why that matters before you go further:

```
It proves that the local workstation network interface is functioning and that it reached the default gateway with zero packet loss
```

---

## Part B — Test the Target

Rungs 3 and 4. This is the fork in the road, and the two tests must be run **in this order** and read **as a pair**. Either one alone tells you almost nothing. Together they tell you almost everything.

### Step 1 — Test the Target by Name

Ping `relay-station.grid.local` — the host the Foundry staff say they can't reach.

Command you ran:

```
ping relay-station.grid.local
```

Output (the full ping result, including the summary line):

```
PING relay-station.grid.local (10.20.5.30) 56(84) bytes of data.

--- relay-station.grid.local ping statistics ---
4 packets transmitted, 0 received, 100% packet loss, time 3005ms
```

### Step 2 — Read That Output Carefully Before You Move On

There are two separate things happening in a ping, and this output separates them for you. Look at the **first line**, then look at the **summary line**, and notice that they do not agree about whether things are going well.

Did the name turn into an IP address — and if so, which one? Then: what did the packet loss say?

```
100% packet loss
```

### Step 3 — Ask DNS Directly

Ping *told* you the name resolved, but it told you in passing, on one line, while it was busy doing something else. Ask the question on its own so the answer is unambiguous — and so you have it in writing when you escalate.

Run `dig relay-station.grid.local`.

Command you ran:

```
dig relay-station.grid.local
```

Output (the full dig result):

```
; <<>> DiG 9.18.24 <<>> relay-station.grid.local
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 41207
;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 0

;; QUESTION SECTION:
;relay-station.grid.local.			IN	A

;; ANSWER SECTION:
relay-station.grid.local.	3600	IN	A	10.20.5.30

;; Query time: 1 msec
;; SERVER: 10.20.5.10#53(10.20.5.10)
```

Look at the `status:` field in the header and the `ANSWER SECTION`. Does DNS know this host, and if so, what address does it hand back?

What the status field said, and the A record it returned:

```
status: NOERROR, id: 41207
relay-station.grid.local.	3600	IN	A	10.20.5.30

```

This is the single most quotable piece of evidence in the whole investigation, because it settles a question your colleague is about to raise.

### Step 4 — Test the Target by IP

Now take the IP address out of that first line and ping it directly: `10.20.5.30`. You are deliberately removing name lookup from the equation.

Command you ran:

```
ping 10.20.5.30
```

Output (the full ping result, including the summary line):

```
PING 10.20.5.30 (10.20.5.30) 56(84) bytes of data.

--- 10.20.5.30 ping statistics ---
4 packets transmitted, 0 received, 100% packet loss, time 3005ms
student@ivy-workstation:/home/student$

```

### Step 5 — State What the Combination Proves

Now put the two results side by side. The by-name test and the by-IP test each had a job:

- The by-name test asks: *can this name be turned into a number, and does that number answer?*
- The by-IP test asks: *forget names entirely — does that number answer?*

Work through it. If the name failed to become a number, that would point one direction. If the name became a number just fine but the number won't answer, that points somewhere else entirely. And if you removed the name from the equation altogether and the failure **didn't change**, then the name lookup isn't what's failing.

Be precise about the limit: `dig` returned `NOERROR` and an A record of `10.20.5.30`, and the probes by name and by IP both got no reply. That establishes that **resolution succeeds and the target isn't responding to these tests.** It does not tell you *why* — the host could be down, a service or the host's own firewall could be dropping the probes, or something on the path could be filtering them.

What the two tests together prove — and, just as importantly, what they rule out:

```
It proves that DNS name resolution is working correctly and successfully maps the hostname to its corresponding IP address. since both name and IP address test failed, we can rule DNS lookup as the cause of failure.
```

---

## Part C — Trace the Path

Rung 5. Ping tells you *whether* something answers. Traceroute lists each hop that answered its probes. A row of `* * *` means *no reply came back for that hop* — it marks where the visible path ends, not proof of exactly what failed there.

**A same-subnet detail that matters here.** `relay-station.grid.local` is `10.20.5.30` — on your own `10.20.5.0/24` subnet, just like `foundry-archive`. Your workstation sends to it directly, not through the gateway, so there is **no router hop** in between. When the relay doesn't answer, the very first row is `* * *`. Don't expect to see `10.20.5.1` in this trace.

One traceroute on its own is hard to read if you've never seen a healthy one. So you'll run two, and compare.

### Step 1 — Trace to the Broken Target

> **Run →** `traceroute relay-station.grid.local` · **Look for →** whether *any* hop answered — for this same-subnet target, hop 1 is already `* * *` · **Record →** the full trace, then say which hop (if any) answered last

Run `traceroute relay-station.grid.local`.

Command you ran:

```
traceroute relay-station.grid.local
```

Output (the full trace, including the timeout rows):

```
traceroute to relay-station.grid.local (10.20.5.30), 8 hops max, 60 byte packets
  1  * * *
  2  * * *
  3  * * *
  4  * * *
  5  * * *
  6  * * *
  7  * * *
  8  * * *
```

The last hop that answered (write "none — hop 1 was already `* * *`" if nothing answered), and what happened after it:

```
none
```

### Step 2 — Trace to a Known-Good Target for Comparison

Now trace to a host you have no reason to suspect — `cloud-heights.grid.local` (`10.20.7.80`), on a different subnet out on the far edge of The Grid. Because it is *not* a neighbour, this trace does go through your gateway first. (You'll be spending a lot of time in Cloud Heights next week. Consider this an introduction.)

Command you ran:

```
traceroute cloud-heights.grid.local
```

Output (the full trace):

```
traceroute to cloud-heights.grid.local (10.20.7.80), 8 hops max, 60 byte packets
  1  grid-gateway (10.20.5.1)  1.000 ms  1.200 ms  1.100 ms
  2  grid-core (10.20.0.1)  4.000 ms  4.200 ms  4.100 ms
  3  cloud-heights.grid.local (10.20.7.80)  12.000 ms  12.200 ms  12.100 ms
```

The complete list of hops, in order:

```
 grid-gateway (10.20.5.1)
grid-core (10.20.0.1
  cloud-heights.grid.local (10.20.7.80) 
```

### Step 3 — Compare the Two Traces

The comparison is the whole point of running two. One trace completed and one didn't — and where each one stopped is the evidence.

Pay attention to the **first hop** of each. The healthy trace leaves through your gateway, `10.20.5.1`, then `grid-core`, then arrives. The relay trace shows no responding hop at all — expected for a same-subnet target, since there is no router between you and it. So the comparison tells you your gateway and the routed path to another subnet are working; it does *not* show where on the local segment the relay's silence comes from. Stars are a lack of replies, not a diagnosis.

What the comparison tells you about where the problem is — and where it is not:

```
The healthy trace successfully reaches ⁠10.20.7.80⁠ by routing through the local gateway (⁠10.20.5.1⁠) and ⁠grid-core⁠ (⁠10.20.0.1⁠), which confirms that local gateway functionality and inter-subnet routing are fully operational. Conversely, the failed trace to the same-subnet target shows no responding hops at all, which is expected behavior because intra-subnet traffic bypasses default gateways and routers entirely. This comparison proves that the problem is not a general network outage or gateway failure, but rather an issue strictly isolated to the local network segment or the target host itself (e.g., host down, local firewall, or Layer 2 connectivity issue).

```

---

## Part D — The Colleague's Theory

A more experienced colleague on the on-call channel reads your notes and jumps in:

> "Oh, I've seen this one. It's the relay station's DNS entry — it's `relay-station-old.grid.local` that everything actually points at, and that name is busted. This is a DNS problem, not a host problem. Go check it."

They sound confident. They may even be senior to you. **Test the claim anyway.** In security work, "somebody confident said so" is not evidence, and the fastest way to be respectfully wrong-footed is to accept a theory you could have checked in ten seconds.

### Step 1 — Test the Colleague's Host

Ping `relay-station-old.grid.local`.

Command you ran:

```
ping relay-station-old.grid.local
```

Output (the exact error or result — copy it precisely):

```
ping: relay-station-old.grid.local: Name or service not known
```

### Step 2 — Explain Why This Is a Different Kind of Failure

This result does **not** look like Part B. Compare them closely:

- In Part B, the name became a number, and then the packets went nowhere.
- Here, you never got a number at all.

Those are two genuinely different failures, at two different stages, and confusing them is one of the most common mistakes in network troubleshooting. In Lesson 2 you met the pair: **a name that doesn't resolve at all** versus **a name that resolves perfectly to a host that's dead**. One is the directory not having an entry. The other is the directory being completely correct about an address where nobody answers the door.

For context: `relay-station-old.grid.local` was decommissioned. It was retired on purpose, its directory entry was removed on purpose, and it has nothing to do with today's ticket. A retired name failing to resolve is *expected behaviour*, not an outage.

Explain, in your own words, why this failure is a different kind from the one in Part B — and why it is not the cause of today's outage:

```
In Part B, the failure occurred at the connectivity level because the hostname successfully resolved to an IP address, but the target host did not respond to network traffic. In this case, the failure occurred earlier at the DNS resolution level, where ⁠relay-station-old.grid.local⁠ failed to resolve to an IP address at all ("Name or service not known"). This is not the cause of today's outage because ⁠relay-station-old.grid.local⁠ is a decommissioned server whose DNS record was intentionally removed, making this resolution failure expected behavior.

```

### Step 3 — Say How You'd Reply to Your Colleague

You now know they're wrong. Being right is easy; being useful about it is a professional skill. Write the reply you'd actually send — polite, specific, and grounded in what you ran rather than in who's more senior. Point at the evidence, not at the person.

Your reply to your colleague:

```
I checked the logs for ⁠relay-station-old.grid.local⁠, and that name failure is actually expected because the server was intentionally decommissioned and its DNS entry removed. The active outage we're troubleshooting is separate, as seen with ⁠relay-station.grid.local⁠ which resolves to ⁠10.20.5.30⁠ but times out on connection. Therefore, this isn't the root cause of our current network issue.

```

### Step 4 — One Control Test Before You Write It Up

You are about to tell people something is broken. Before you do, prove that *not everything* is broken — otherwise the first question you'll be asked is whether the problem is you.

Ping a host you already know is healthy: `foundry-archive.grid.local`.

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

Why this matters: a report that says "the relay station is not responding" is weaker than one that says "the relay station is not responding to ping or traceroute **while the other hosts I tested answer normally**." The second version makes your own machine and the shared network unlikely culprits — it doesn't prove nothing else could be wrong, but it's the difference between a ticket that gets acted on and one that gets handed back to you.

### Step 5 — Capture Your Evidence (REQUIRED screenshot)

**This screenshot is required.** Between them, your screenshot(s) must show all four results:

1. the failed ping **by name** (`relay-station.grid.local`, 100% loss)
2. the successful **`dig`** (`status: NOERROR`, A record `10.20.5.30`)
3. the failed ping **by IP** (`10.20.5.30`, 100% loss)
4. the failed **`traceroute relay-station.grid.local`** (`* * *` rows)

Either way is fine, and splitting is never penalised.
- **One image:** scroll so all four are visible at a readable size and capture it as `cli-grid-outage.png`.
- **Several images:** if one readable image can't fit everything, name the first `cli-grid-outage.png` and the rest `cli-grid-outage-2.png`, `cli-grid-outage-3.png`, … Each must be readable at full size, and together they must show all four results.

How to capture: **Windows** `Win + Shift + S` · **Mac** `Cmd + Shift + 4`, then drag. Don't shrink the text to make it fit — take another image instead. You'll upload it (or them) and record the filename(s) via the GitHub Commit section below.

An incident note with no attached evidence is just an opinion. This is the attachment.

---

## Part E — Write the Finding

This is the part that makes you a security professional rather than someone who can run commands.

Everything you've done so far is raw evidence. Nobody upstream of you — not your team lead, not the Foundry staff who filed the ticket, not the person who picks this up on the next shift — is going to read six terminal outputs and work out the conclusion themselves. Your job is to hand them the conclusion **and** the reason to believe it.

This is your first incident-report rep. You will do many more; Week 12's capstone asks you to write these for real. So we're giving you the structure now, while the stakes are low. Fill in each field — this is not a blank page, it's a form, and every professional incident note answers these same four questions.

**Keep it short and specific.** A good finding is a paragraph, not an essay. Write what you can defend.

### Step 1 — What Is Broken

One or two sentences. Name the specific thing that is not working, at the strength your evidence supports — not "the network," not "the relay station area," but the actual component your evidence points at. "Not responding to ping or traceroute" is something you observed; "powered off" is something you'd be guessing.

Your statement of what is broken:

```
The host ⁠relay-station.grid.local⁠ (⁠10.20.5.30⁠) is unreachable over the network, failing to respond to ping and traceroute requests. While its DNS name resolves properly, no traffic is being received by the host.

```

### Step 2 — What Evidence Proves It

List the specific tests you ran and what each one returned. This is where your Parts B and C outputs earn their keep. Someone reading this should be able to re-run your tests and reach the same conclusion without asking you a single question.

Your evidence, test by test:

```
ping 10.20.5.30: Resulted in 4 packets transmitted, 0 received, with 100% packet loss (3005ms time).
traceroute relay-station.grid.local (10.20.5.30): Showed 8 hops max, with all hops failing and timing out (* * *) across the entire path.
traceroute ccloud-heights.grid.local / Ping tests to control hosts: Proved that name resolution and network path routing function correctly for other hosts, isolating the failure specifically to relay-station.grid.local.

```

### Step 3 — What You Ruled Out, and How

Just as important as what you found. Say explicitly which possible causes you made unlikely and which specific test did it. At minimum, address: your own workstation, your local network path out, name resolution, and the colleague's theory. Be honest about what's still open — your tests can't tell a powered-off host from one whose firewall or service is dropping probes.

Ruling out possibilities is what separates a finding from a guess. A reader who sees only your conclusion has to trust you. A reader who sees what you eliminated can *check* you.

What you ruled out and the evidence that ruled it out:

```
Your own workstation & local network path out: Ruled out by traceroute and control pings succeeding to other local network targets (grid-gateway and grid-core), proving local outbound connectivity is healthy.
Name resolution (DNS failure): Ruled out because relay-station.grid.local successfully resolves to its IP address (10.20.5.30), unlike a dead/non-existent record.
Colleague’s theory (relay-station-old.grid.local as the outage): Ruled out because the name resolution failure for that old host is expected behavior due to it being intentionally decommissioned, not an active network fault.
What's still open: Cannot definitively distinguish whether the host is powered off or if a firewall/service is dropping probes, as both present identically as unresponded packets.

```

### Step 4 — What Should Happen Next

One or two sentences. Based on your finding, who or what needs to be looked at, and by whom? A good next step investigates what your tests couldn't see: the relay host and its services, any filtering or firewall rules in front of it, and the local path to it. You don't have the access to fix this yourself — and that's normal. Knowing exactly what to escalate, and to whom, is the deliverable.

Your recommended next step:

```
The incident should be escalated to the infrastructure or network security team to inspect the ⁠relay-station⁠ host itself and verify its local service status. Additionally, they need to check any firewall or filtering rules in front of the host that might be dropping probe packets.

```

---

## Analysis Questions

**Analysis Question 1.** In Part B you pinged the target by name and then by IP. Explain why running *both* is necessary — describe what a person would wrongly conclude if they had run only the by-name test and stopped there. *(Minimum 3 sentences.)*

```
Running both tests is necessary because pinging by name tests both DNS resolution and network connectivity, while pinging by IP tests network connectivity in isolation. If someone ran only the by-name test and it failed, they might wrongly conclude that the target host or network is entirely down when the problem is actually just a local DNS failure. Testing by IP allows you to isolate the exact layer of failure, proving whether the host itself is unreachable or if DNS merely failed to translate the hostname.
```

**Analysis Question 2.** A name that returns "Name or service not known" and a name that resolves fine but never replies to a ping are two different failures with two different causes. Describe each one in plain English — what is actually going wrong in each case — and explain why treating them as the same problem would send a troubleshooter down the wrong path. *(Minimum 3 sentences.)*

```
A "Name or service not known" error means the DNS server has no directory record for that hostname, whereas a name that resolves fine but fails to reply indicates that DNS worked correctly, but network traffic could not reach or get a response from the host. In plain English, the first issue is like trying to call a non-existent phone number, while the second issue is like calling a valid phone number that nobody answers. Treating them as the same problem sends a troubleshooter down the wrong path because fixing a missing DNS record requires checking directory configurations, while fixing an unreachable host requires inspecting firewalls, routing, or system power.
```

**Analysis Question 3.** Part A asked you to confirm your own address and your gateway before touching the reported problem at all — even though the ticket had nothing to do with your workstation. Defend that step to a colleague who thinks it's a waste of time on an urgent ticket. *(Minimum 3 sentences.)*

```
Confirming your own workstation address and gateway first establishes a known-good baseline, proving that your local network interface and outbound path are operating normally. Without this step, any failed test to a remote target could simply be caused by a disconnected cable, improper local IP configuration, or a broken default gateway on your own machine. Validating your own setup upfront prevents wasting critical time chasing phantom external outages when the root cause might actually be on the local host.

```

**Analysis Question 4.** Your colleague was confident, possibly senior, and wrong. Describe how you decided their theory was wrong, and what you think that says about how much weight confidence should carry compared to evidence in security work. Where relevant, consider what would have happened if you had simply accepted their theory and reported it upward. *(Minimum 3 sentences.)*

```
We determined the colleague's theory was wrong by testing their specific claim against actual terminal evidence, which showed that the name resolution failure they pointed to was just a decommissioned, unrelated host rather than the active outage. This highlights that in security work, empirical evidence and reproducible test results must always carry significantly more weight than seniority, titles, or confident assumptions. If we had simply accepted their theory without verification and reported it upward, we would have escalated a non-issue, compromised our own credibility, and left the actual network outage completely unresolved.

```

---

## Submission Checklist

- [x] Own address and gateway confirmed (Part A, Step 1)

- [x] Gateway pinged successfully and output recorded (Part A, Step 2)

- [x] Baseline reasoning stated — what a healthy baseline rules out (Part A, Step 3)

- [x] `relay-station.grid.local` pinged by name; resolution and packet loss both read (Part B, Steps 1–2)

- [x] `dig relay-station.grid.local` run; status and A record recorded (Part B, Step 3)

- [x] `10.20.5.30` pinged directly; output recorded (Part B, Step 4)

- [x] The combined proof of the tests stated in writing (Part B, Step 5)

- [x] `traceroute relay-station.grid.local` run; last responding hop identified (Part C, Step 1)

- [x] `traceroute cloud-heights.grid.local` run; all hops listed in order (Part C, Step 2)

- [x] The two traces compared and interpreted (Part C, Step 3)

- [x] `relay-station-old.grid.local` tested and its exact error recorded (Part D, Step 1)

- [x] Explained why the decoy is a different kind of failure (Part D, Step 2)

- [x] Reply to the colleague drafted, grounded in evidence (Part D, Step 3)

- [x] Control test run against a healthy host before escalating (Part D, Step 4)

- [ ] **REQUIRED:** `cli-grid-outage.png` (plus any `cli-grid-outage-2.png`, `-3.png` …) uploaded to `assets/screenshots/week-05/`, together showing the by-name ping, `dig`, by-IP ping and traceroute, and filename(s) recorded (Part D, Step 5)

- [x] Incident note complete — what's broken, evidence, ruled out, next step (Part E, Steps 1–4)

- [x] All four Analysis Questions answered (minimum sentence counts met)

- [x] This file is committed to your portfolio repo at `week-05/labs/lab-02-the-grid-outage.md`

---

## GitHub Commit Subsection

This lab's written answers are submitted through the **CyberFoundations Lab Portal**.

1. Go to the CyberFoundations Lab Portal and sign in.
2. Open **Week 5 → Lab 02: The Grid Outage**.
3. Fill in the worksheet fields — they match the commands, outputs, and questions in this file, in the same order.
4. Connect your GitHub account if you haven't already (one-time setup), and select your portfolio repo.
5. Click **Submit to GitHub**. The Portal commits the completed file to `week-05/labs/lab-02-the-grid-outage.md` for you.

**📸 REQUIRED — your evidence screenshot.** Your incident note in Part E needs something to point at:

1. Go to your portfolio repository on GitHub.com and navigate to `assets/screenshots/week-05/` (create the folder if this is your first Week 5 screenshot).
2. Click **Add file → Upload files**, drag in your screenshot `cli-grid-outage.png` — and any extra `cli-grid-outage-2.png`, `cli-grid-outage-3.png` … (lowercase, hyphens, no spaces).
3. Scroll down and click **Commit changes**.
4. Click the uploaded image's filename to open it and confirm your evidence is readable at full size — if the terminal text is too small to read, retake it.
5. Record the filename below so your grader knows to look for it. If you used more than one image, list every filename in this same box, separated by commas.

The screenshot filename(s) you uploaded:

```
(type the filename here, e.g. cli-grid-outage.png)
```

Your screenshot lives in `assets/screenshots/week-05/` in your repository, alongside the rest of your Week 5 evidence. It does not need to be linked inside this worksheet.

---

*CyberVisionaries Institute · Cyber Foundations · Tier I*
