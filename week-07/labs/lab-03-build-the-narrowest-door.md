# Week 7 Lab 03 — Build the Narrowest Door

**Student Name:** Tiwonge Kafera

**Date Completed:** 11/7/26

**Module:** 2 — Networking & Cloud Foundations | **Week:** 7  
**Submission Path:** `week-07/labs/lab-03-build-the-narrowest-door.md`

> ## Cloud Heights Protected-Rules Safety Rule
> Your room uses one of two protected baselines. Check your own **INBOUND — EVALUATION ORDER** list to see which one you have:
>
> | Baseline | Protected rules you will see | Fallback for TCP 8080 |
> |---|---|---|
> | **Legacy (four rules)** | 100 `allow-ssh-from-bastion`, 110 `allow-icmp-intra-vnet`, 120 `deny-ssh-student-subnet`, 1000 `deny-tcp8080-student-subnet` | Priority **1000** denies TCP 8080 from `10.60.6.0/26` |
> | **Newer (three rules)** | 100 `allow-ssh-from-bastion`, 110 `allow-icmp-intra-vnet`, 4096 `deny-other-inbound` | Priority **4096** explicitly denies all other inbound traffic (any protocol, source and port) |
>
> **Protected** rules (priorities 100/110, plus 120 and 1000 in legacy rooms, or 4096 in newer rooms) are course infrastructure: they are locked, and you must never modify them. Azure also has built-in default rules (priorities 65000–65500) that the Portal does not list; do not record them unless your ledger actually shows them. **Your editable range is 200–999.** Every student rule is read before either fallback. Write down the rule set your Portal actually shows; do not copy rules from the other baseline or invent missing ones. Use only inbound TCP 8080 rules from `10.60.6.4` to your assigned VM in Labs 03–05; never create SSH, ICMP, Any-protocol or outbound rules for these exercises. Bastion SSH and ICMP are matched by the earlier protected allows at 100/110 in both baselines. Honest diagnosis of a student-range mistake is not a grading penalty.

> **Evidence safety:** Never include a Cloud Heights password or Bastion shareable URL. Crop browser address bars and login information before committing screenshots.

---

## Mission

Run a temporary web service on TCP 8080 and create the least-privilege inbound rule: only Grid Beacon (`10.60.6.4`) may reach the service. You will predict the outcome before changing the ledger and verify the result afterward.

Your Allow at a student priority (recommended `300`) is evaluated **before** the protected fallback in your actual ledger: **1000** `deny-tcp8080-student-subnet` on legacy rooms or **4096** `deny-other-inbound` on newer rooms. So before you create the Allow, Grid Beacon's TCP 8080 traffic is denied by your room's fallback (1000 legacy / 4096 newer); after your narrow Allow exists and the listener is running, the same traffic is allowed because the lower priority number wins.

## What You Already Know

A network security rule is a decision about traffic. Rules are evaluated from the lowest priority number to the highest, and the first matching rule wins. Inbound and outbound traffic use separate ledgers. A configured service, a security rule, a test result, and an evidence screenshot answer different questions.

## Lab Environment / Pre-Lab Check

| Component | Details |
|---|---|
| Service command | `python3 -m http.server 8080` |
| Allowed source | Grid Beacon — `10.60.6.4` |
| Test | Portal **Test My Rule**, fixed TCP 8080 |
| Time | 35–45 minutes |

- [x] I am using my assigned `cf-student-XX` VM through the CyberFoundations Lab Portal.

- [x] The VM shows **Running**.

- [x] I can identify every protected rule in my actual baseline (legacy 100/110/120/1000 or newer 100/110/4096).

- [x] I understand that my editable priority range is 200–999.

### Cloud Heights Idle Stop

Cloud Heights may warn you that the VM is idle. Return to the Lab Portal and choose **I'm still working** if you are active. If the VM is stopped or deallocated, it was not deleted: restart it from **My Lab Environment**. Your disk files and saved configuration remain.

## Predict First

Before starting the service or creating a rule, predict both conditions:

1. What verdict should Grid Beacon receive while no student Allow exists?
2. What verdict should it receive after the narrow Allow is created and the service is listening?
3. Which rule decides each case — your student Allow, or the protected fallback (1000 on legacy rooms; 4096 on newer rooms) deny?

```text
 Before the student Allow rule is created, Grid Beacon (10.60.6.4) will receive a DENIED verdict. This outcome occurs because the incoming traffic matches the protected fallback rule (Priority 1000 deny-tcp8080-student-subnet or Priority 4096 deny-other-inbound), which denies inbound traffic on TCP 8080. After the student Allow rule (allow-grid-beacon-8080) is created at Priority 300 and the Python service is listening, Grid Beacon will receive an ALLOWED verdict. The student Allow rule takes precedence over the fallback deny rule because Network Security Groups process rules in numerical order, where a lower priority number (300) evaluates before a higher priority number (1000 or 4096).

```

## Guided Steps

### Step 1 — Start the Listener

Open your assigned VM through Bastion and run this in its terminal, not your own computer's terminal. This starts a temporary web server on TCP 8080 and keeps the terminal occupied; it does not change firewall rules. First move to an empty practice directory so the server does not expose personal files:

Run in that VM terminal:

```bash
python3 -m http.server 8080
```

The terminal stays busy while the server runs. Leave it open. `Ctrl+C` stops it when the lab is complete.

### Step 2 — Stop & Check the Service State

Confirm the terminal shows that it is serving on port 8080. If it says 'Address already in use', check the existing listener; do not start another or kill an unknown process. A serving message alone does not prove the network permits access. If the Portal later reports `SERVICE_NOT_LISTENING`, return here before changing rules.

### Step 3 — Create the Narrow Rule

In Security Rules, add one student rule with these values:

| Field | Required value |
|---|---|
| Name | `allow-grid-beacon-8080` (or a clearly equivalent name) |
| Priority | One unused value from 200–999; recommended `300` |
| Direction | Inbound |
| Action | Allow |
| Protocol | TCP |
| Source | `10.60.6.4` |
| Source port | Any |
| Destination | Type your assigned cf-student-XX name or recorded private IP; there is no prefilled default |
| Destination port | `8080` |
| Description | Week 7 least-privilege test service |

Do not broaden the source to the subnet or Any.

### Step 4 — Record the Change

```text
Name: allow-grid-beacon-8080
Priority: 300
Direction: Inbound
Action: Allow
Protocol: TCP
Source: 10.60.6.4
Source Port: Any
Destination: Assigned student VM name / recorded private IP
Destination Port: 8080
Description: Week 7 least-privilege test service

```

## Test

Choose **Test My Rule**, select **Grid Beacon (10.60.6.4)**, and run the fixed TCP 8080 test. Wait at least 10 seconds before repeating a test.

Expected verdict: `ALLOWED`.

If you see `SERVICE_NOT_LISTENING`, the rule may be correct but the Python listener is not running. If you see `TEST_ERROR`, verify the VM is running, wait 10 seconds, and retry once before requesting support.

## Capture Evidence

Capture the completed narrow rule and the Grid Beacon `ALLOWED` result. The images must show the source and destination port clearly.

## Explain

Explain why allowing one source to one TCP port is narrower than allowing the whole subnet or Any source, even though all three might make the intended test pass.

```text
Restricting access to a single source IP address (10.60.6.4) and a specific destination port (8080) applies the principle of least privilege by reducing the overall attack surface of the virtual machine. While granting access to an entire subnet or Any source would allow the test to pass, it opens communication channels to unauthorized hosts that do not require access. Restricting the source IP ensures that only the intended host (Grid Beacon) can establish a connection, preventing rogue endpoints on the local subnet or external network from probing the port. Limiting the rule to a specific port prevents other application ports on the host from being inadvertently exposed to incoming traffic. Therefore, defining precise source and port parameters minimizes exposure while granting only the absolute minimum connectivity required for the service to function.
```

## Required Evidence

Save screenshots in `assets/screenshots/week-07/`:

- `week07-lab03-rule-created.png`
- `week07-lab03-beacon-allowed.png`

Open each image at full size before submission. Confirm that no password, Bastion shareable URL, browser address bar, or unrelated private information is visible.

## Analysis Questions

**Analysis Question 1.** The Python service and the NSG rule perform different jobs. What does each one control? (Minimum 3 sentences.)

```text
dynamics of network access versus local host processing. The Network Security Group (NSG) rule operates at the network interface layer to filter inbound network traffic before it reaches the operating system, deciding whether packets arriving from 10.60.6.4 on port 8080 are allowed or dropped. Conversely, the Python service (python3 -m http.server 8080) operates at the application layer inside the host operating system, binding to port 8080 to listen for incoming HTTP requests and serve responses once traffic passes through the firewall. Without the NSG rule, legitimate network packets cannot reach the host, and without the Python service actively listening, the host will drop or reject connection attempts even if the NSG rule permits the traffic.
```

**Analysis Question 2.** Why is `10.60.6.4` a stronger source value than Any for this task? (Minimum 4 sentences.)

```text
Using 10.60.6.4 as the source value enforces strict network perimeter security by explicitly defining which host is authorized to communicate with the service. If the source parameter is set to Any, any device inside or outside the network can attempt connections to TCP port 8080, increasing the risk of unauthorized scanning or exploitation. Specifying 10.60.6.4 ensures that only traffic originating from the validated Grid Beacon IP address is accepted by the network interface. This practice directly aligns with zero-trust and least-privilege architecture by ensuring network paths are only opened for identified, necessary assets rather than granting blanket network exposure.
```

**Analysis Question 3.** What does an `ALLOWED` result prove, and what does it not prove about other sources? (Minimum 3 sentences.)

```text
: An ALLOWED result proves that the specific test traffic sent from 10.60.6.4 on TCP port 8080 successfully matched an active inbound rule (Priority 300) and reached the listening Python service without being blocked. However, this result does not prove that traffic from other IP sources or traffic aimed at different ports would be blocked or handled correctly by the network security configuration. To verify that unauthorized traffic from other sources is properly restricted, separate tests originating from non-permitted IP addresses or alternative ports must be conducted against the fallback deny rules.
```

## Submission Checklist

- [x] Listener started before testing

- [x] Narrow inbound Allow created

- [ ] Source is exactly `10.60.6.4` and destination port exactly `8080`

- [ ] Grid Beacon result is `ALLOWED`

- [ ] `week07-lab03-rule-created.png` captured

- [ ] `week07-lab03-beacon-allowed.png` captured

- [ ] All protected rules in my actual baseline were left unchanged.

- [ ] Every rule I created or edited used priority 200–999.

- [ ] No password, Bastion URL, or browser address bar appears in my files.

- [ ] This worksheet is committed to `week-07/labs/lab-03-build-the-narrowest-door.md`.

## GitHub / Lab Portal Submission

1. Open **Week 7 → Lab 03: Build the Narrowest Door** in the CyberFoundations Lab Portal.
2. Complete every worksheet field and confirm the listed evidence filenames.
3. Upload screenshots to `assets/screenshots/week-07/`.
4. Confirm your portfolio repository is connected, then choose **Submit to GitHub**.
5. Open the committed worksheet and each image on GitHub to verify formatting, legibility, and redaction.

*CyberVisionaries Institute · CyberFoundations · Tier I*

**Interpreting results:** A TCP connection success proves reachability at that moment, not which rule matched. A timeout marked DENIED means traffic appears blocked; it is not a flow-log verdict and can also reflect a routing or host-firewall problem. SERVICE_NOT_LISTENING means an immediate refusal; check the listener before altering rules. TEST_ERROR is not denial evidence. Each test opens a fresh connection; after a rule change, allow Azure to finish, refresh the ledger and wait at least 10 seconds before retesting. Never invent a successful or denied result.
