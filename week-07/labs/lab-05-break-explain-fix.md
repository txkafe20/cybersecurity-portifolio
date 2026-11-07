# Week 7 Lab 05 — Break It, Explain It, Fix It

**Student Name:** Tiwonge Kafera

**Date Completed:** 11/7/26

**Module:** 2 — Networking & Cloud Foundations | **Week:** 7  
**Submission Path:** `week-07/labs/lab-05-break-explain-fix.md`

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

Create a controlled priority failure inside your student range, diagnose it from evidence, remove the problem, and retest. The goal is method: UNDERSTAND → PREDICT → CHANGE → TEST → VERIFY, then FORECAST → EXECUTE → VERIFY → REMEDIATE → RETEST.

## What You Already Know

A network security rule is a decision about traffic. Rules are evaluated from the lowest priority number to the highest, and the first matching rule wins. Inbound and outbound traffic use separate ledgers. A configured service, a security rule, a test result, and an evidence screenshot answer different questions.

## Lab Environment / Pre-Lab Check

| Component | Details |
|---|---|
| Required setup | Lab 03 narrow Allow present; listener running |
| Safe failure | Student-created Deny only; every actual protected baseline rule untouched |
| Temporary rule priority | An unused number lower than your Lab 03 Allow, within 200–999 (example: Allow 300 → Deny 250) |
| Time | 45–55 minutes |

- [ ] I am using my assigned `cf-student-XX` VM through the CyberFoundations Lab Portal.

- [ ] The VM shows **Running**.

- [ ] I can identify every protected rule in my actual baseline (legacy 100/110/120/1000 or newer 100/110/4096).

- [ ] I understand that my editable priority range is 200–999.

### Cloud Heights Idle Stop

Cloud Heights may warn you that the VM is idle. Return to the Lab Portal and choose **I'm still working** if you are active. If the VM is stopped or deallocated, it was not deleted: restart it from **My Lab Environment**. Your disk files and saved configuration remain.

## UNDERSTAND

Your working Lab 03 Allow is at a priority you chose (often 300). A new matching Deny with a **lower** number is evaluated first and makes the later Allow unreachable for that traffic.

**Choose your fault priority before you change anything.**

1. Look up the priority of your Lab 03 Allow in your ledger.
2. Pick an **unused** number that is lower than that Allow and still at least 200. Example: Allow 300 → Deny 250. Allow 450 → Deny 400.
3. **If your Allow is at 200, or every lower number from 200 up is already used, stop and ask your instructor before making any change.** Do not move, edit or delete your Allow on your own, and never reuse an occupied priority.

In the rest of this lab, *your fault priority* means the number you chose here.

## Predict First — FORECAST

Write your Allow priority and your chosen fault priority, then predict Grid Beacon's verdict after adding an inbound Deny for TCP 8080 from `10.60.6.4` at your fault priority, while leaving the working Allow in place.

```text
My narrow Allow rule is set to priority 300, and my temporary Deny rule (deny-grid-beacon-8080-test) is set to priority 250.

 The predicted verdict for Grid Beacon traffic on port 8080 from IP 10.60.6.4 is DENIED.

 The first matching rule evaluated by the network ledger will be the temporary Deny rule because lower priority numbers take precedence in security rule evaluation order. Since the incoming packet matches the source IP, destination port (8080), and TCP protocol of the Deny rule first, evaluation stops before reaching the higher-numbered Allow rule.

```

## CHANGE / EXECUTE

### Step 1 — Create the Temporary Fault

Create a student rule named `deny-grid-beacon-8080-test`:

- priority: your chosen fault priority — unused, lower than your Lab 03 Allow, and within 200–999
- Inbound / Deny / TCP
- source `10.60.6.4`; source port Any
- destination your assigned VM name or recorded private IP; destination port `8080`
- description: intentional Week 7 troubleshooting fault

Do not edit or delete the Lab 03 Allow. Do not touch any protected rule — your actual fallback (1000 legacy or 4096 newer) stays unchanged throughout this lab.

### Step 2 — Capture the Broken Ledger

Capture both your temporary Deny (at your fault priority) and the later Allow in the same ordered rule view, so the screenshot shows the Deny listed first.

## TEST / VERIFY

Wait at least 10 seconds, select Grid Beacon, and run **Test My Rule**. Expected verdict: `DENIED`.

```text
The actual test result returned DENIED when testing Grid Beacon traffic on port 8080 from 10.60.6.4. This result matched my forecast.

 The lower priority Deny rule successfully intercepted incoming traffic prior to reaching the Allow rule as predicted.

```

### Stop & Check — Diagnose Before Fixing

Confirm these healthy facts before remediation:

- VM is Running.
- Python listener is still active on 8080.
- The original Allow is still present and correctly scoped.
- The temporary Deny has a lower priority number than the Allow, so it is evaluated first.

```text
The Virtual Machine status remains active and fully provisioned.
The Python listener process on port 8080 was verified as running actively on the target VM.
Traffic from sources other than 10.60.6.4 or non-blocked rules continues to reach expected endpoints, confirming that host service processes and network stack layers are functional.

```

## REMEDIATE

Delete only the temporary `deny-grid-beacon-8080-test` rule you created. Removing the controlled fault returns the ledger to the known-good least-privilege state. Refresh only rereads rules; there is no portal baseline-reset button. Restarting a VM does not reset its rules. To return to the initial baseline after evidence is complete, remove only your own Week 7 student rules, never infrastructure rules. For this lab's retest, keep the narrow Allow and remove only the temporary Deny.

## RETEST

1. Wait at least 10 seconds and retest Grid Beacon: expected `ALLOWED`.
2. Wait at least 10 seconds and retest Other Test Source: expected `DENIED`.

```text
Grid Beacon Retest Result: ALLOWED (matched expectation after deleting the temporary Deny rule).
Other Test Source Retest Result: DENIED (matched expectation as traffic outside the scoped rule set remains properly restricted).

```

## Capture Evidence

Your sequence must show broken rules, observed denial, repaired rules, and final retest results.

## Explain — Incident Note

```text
Problem: Grid Beacon traffic on port 8080 from source IP 10.60.6.4 failed connectivity checks and returned a DENIED status.
Evidence: Network evaluation tests returned a DENIED verdict on port 8080 despite the existence of a valid Allow rule. Screenshot captures confirmed an active temporary Deny rule (deny-grid-beacon-8080-test) placed at priority 250 above the Allow rule at priority 300.
Healthy conditions ruled out: VM instance failure, host operating system crash, and stopped Python listener service processes were ruled out after confirming the VM was running and the listener application was actively bound to port 8080.
Root cause: Network security rules are processed sequentially in ascending order of priority value (lowest number first). The temporary Deny rule (priority 250) shadowed the existing Allow rule (priority 300), catching matching inbound traffic first and dropping it before the Allow rule could be evaluated.
Remediation: Removed the temporary deny-grid-beacon-8080-test rule from the network security ledger while leaving the baseline scoped Allow rule intact.
Retest: Re-executed connectivity tests: Grid Beacon traffic returned an ALLOWED status, and non-permitted test sources returned a DENIED status as intended.
Prevention: Enforce priority assignment conventions in security group rule bases, audit rule order prior to deploying changes, and utilize pre-deployment simulation/forecasting to prevent accidental rule shadowing.
```

## Required Evidence

Save screenshots in `assets/screenshots/week-07/`:

- `week07-lab05-broken-rules.png`
- `week07-lab05-observed-denial.png`
- `week07-lab05-fixed-rules.png`
- `week07-lab05-retest-results.png`

Open each image at full size before submission. Confirm that no password, Bastion shareable URL, browser address bar, or unrelated private information is visible.

## Analysis Questions

**Analysis Question 1.** Why did the correct Allow stop working even though it was never edited? (Minimum 4 sentences.)

```text
Network security engines evaluate rules sequentially using first-match logic based on numerical priority, where lower priority numbers take precedence. Even though the Allow rule was never modified, a new Deny rule was inserted with a lower priority number ahead of it in the evaluation order. Because the incoming traffic matched the parameters of the newly inserted Deny rule first, the firewall engine applied that action immediately and stopped further rule evaluation. Consequently, the packet was dropped before the firewall ever reached or evaluated the unedited Allow rule located further down the ledger.
```

**Analysis Question 2.** Why is diagnosing from evaluation order better than changing rules by trial and error? (Minimum 4 sentences.)

```text
Diagnosing network access issues by analyzing rule evaluation order provides a systematic, deterministic understanding of how traffic traverses security ledgers. Trial-and-error modifications frequently introduce overlapping, unnecessary, or overly permissive rules that undermine least-privilege security principles. By identifying rule shadowing and priority order directly from evidence, administrators can target exact faults without creating unintended security gaps. This structured approach reduces downtime, prevents secondary misconfigurations, and ensures the environment remains audit-compliant.
```

**Analysis Question 3.** What made this failure safe and recoverable in the course environment? (Minimum 3 sentences.)

```text
The lab scenario was designed around controlled fault creation using a temporary, isolated Deny rule rather than modifying core baseline rules. The underlying infrastructure, virtual machine states, and service processes remained completely operational throughout the exercise. Recovery required simply deleting the temporary test rule, restoring the network ledger to its known-good baseline state without risking permanent configuration loss or server downtime.

```

## Submission Checklist

- [ ] Forecast written before the change

- [ ] Temporary fault used an unused priority lower than my Lab 03 Allow and within 200–999 (or I asked my instructor first)

- [ ] Broken ledger and DENIED result captured

- [ ] VM, listener, and original Allow checked before remediation

- [ ] Only the temporary Deny removed

- [ ] Grid Beacon retested `ALLOWED` and Other Test Source retested `DENIED`

- [ ] Incident note completed

- [ ] All protected rules in my actual baseline were left unchanged.

- [ ] Every rule I created or edited used priority 200–999.

- [ ] No password, Bastion URL, or browser address bar appears in my files.

- [ ] This worksheet is committed to `week-07/labs/lab-05-break-explain-fix.md`.

## GitHub / Lab Portal Submission

1. Open **Week 7 → Lab 05: Break It, Explain It, Fix It** in the CyberFoundations Lab Portal.
2. Complete every worksheet field and confirm the listed evidence filenames.
3. Upload screenshots to `assets/screenshots/week-07/`.
4. Confirm your portfolio repository is connected, then choose **Submit to GitHub**.
5. Open the committed worksheet and each image on GitHub to verify formatting, legibility, and redaction.

*CyberVisionaries Institute · CyberFoundations · Tier I*

**Interpreting results:** A TCP connection success proves reachability at that moment, not which rule matched. A timeout marked DENIED means traffic appears blocked; it is not a flow-log verdict and can also reflect a routing or host-firewall problem. SERVICE_NOT_LISTENING means an immediate refusal; check the listener before altering rules. TEST_ERROR is not denial evidence. Each test opens a fresh connection; after a rule change, allow Azure to finish, refresh the ledger and wait at least 10 seconds before retesting. Never invent a successful or denied result.
