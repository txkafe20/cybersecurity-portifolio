# Week 7 Lab 04 — Prove the Guard Works ★ Deliverable 2

**Student Name:** Tiwonge Kafera

**Date Completed:** 11/7/26

**Module:** 2 — Networking & Cloud Foundations | **Week:** 7  
**Submission Path:** `week-07/labs/lab-04-prove-the-guard-works.md`

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

Assemble Deliverable 2 evidence by proving both halves of least privilege: the intended source is allowed and an unintended source is denied. A single successful test is not enough.

## What You Already Know

A network security rule is a decision about traffic. Rules are evaluated from the lowest priority number to the highest, and the first matching rule wins. Inbound and outbound traffic use separate ledgers. A configured service, a security rule, a test result, and an evidence screenshot answer different questions.

## Lab Environment / Pre-Lab Check

| Component | Details |
|---|---|
| Required setup | Lab 03 rule present; Python listener still running |
| Allowed source | Grid Beacon — `10.60.6.4` |
| Unintended source | Other Test Source — `10.60.6.10` |
| Deliverable | Security group configuration + verification evidence |
| Time | 30–40 minutes |

- [ ] I am using my assigned `cf-student-XX` VM through the CyberFoundations Lab Portal.

- [ ] The VM shows **Running**.

- [ ] I can identify every protected rule in my actual baseline (legacy 100/110/120/1000 or newer 100/110/4096).

- [ ] I understand that my editable priority range is 200–999.

### Cloud Heights Idle Stop

Cloud Heights may warn you that the VM is idle. Return to the Lab Portal and choose **I'm still working** if you are active. If the VM is stopped or deallocated, it was not deleted: restart it from **My Lab Environment**. Your disk files and saved configuration remain.

## Predict First

**Prerequisite from Lab 03 (required):** the Python listener is running on TCP 8080, and your narrow inbound Allow for `10.60.6.4` port `8080` exists in priorities **200–999**. If either is missing, finish Lab 03 first.

**Expected results:** Grid Beacon `10.60.6.4` is **ALLOWED** by your student Allow; Other Test Source `10.60.6.10` is **DENIED** by the protected fallback in your actual ledger: **1000** `deny-tcp8080-student-subnet` on legacy rooms or **4096** `deny-other-inbound` on newer rooms.

**DO NOT** create an additional deny rule, broaden your allow, or modify any protected rule to produce these results.

Without changing the Lab 03 rule, predict the result from each test source.

| Source | Prediction | Deciding rule/reason |
| --- | --- | --- |
| Grid Beacon `10.60.6.4` | Allowed | Matched student Allow rule (priority 200–999) for TCP 8080 from 10.60.6.4. |
| Other Test Source `10.60.6.10` | Denied | Fallback default deny rule (deny-tcp8080-student-subnet at priority 1000, or deny-other-inbound at priority 4096). |

## Guided Steps

### Step 1 — Verify the Final Configuration

Confirm the listener is running and the student Allow remains inbound TCP 8080 from exactly `10.60.6.4`.

### Step 2 — Test the Intended Source

Select **Grid Beacon (10.60.6.4)** and run **Test My Rule**. Record the verdict and compare it with your prediction.

```text
 ALLOWED Explanation: The inbound TCP traffic on port 8080 sent from 10.60.6.4 matched the custom student inbound Allow rule (priorities 200–999), permitting the traffic through to the listening service.
```

### Step 3 — Test the Unintended Source

Wait at least 10 seconds. Select **Other Test Source (10.60.6.10)** and run the same fixed TCP 8080 test.

```text
DENIED Explanation: Traffic from 10.60.6.10 did not match the specific 10.60.6.4 student Allow rule. Because rules are evaluated in order and no higher-priority rule allowed this source IP, it hit the fallback deny rule (priority 1000/4096), which explicitly blocked access.

```

Expected verdict: `DENIED`, produced by the protected fallback (1000 on legacy rooms; 4096 on newer rooms).

If either result differs from expected, **stop making changes**: capture the complete rule list in evaluation order plus the test result, and report it to your instructor in this worksheet. Do not add a deny rule, broaden the allow, or modify protected rules.

## Stop & Check

Your evidence pair should now prove:

- the intended connection is permitted;
- the unintended connection is not permitted;
- the service was listening during both tests;
- the rule source is narrow rather than Any.

## Test Summary

| Evidence question | Result |
| --- | --- |
| Is the service listening? | yes |
| Is Grid Beacon allowed? | yes |
| Is Other Test Source denied? | yes |
| Which rule produces the intended Allow? | Student Allow rule (TCP 8080 from 10.60.6.4) |

## Capture Evidence

Capture the final rule plus both result cards. Screenshots must show the selected source and verdict. These images are the core evidence for Deliverable 2.

## Explain — Deliverable 2 Statement

Write a concise professional statement covering what you configured, the source/port scope, the two tests, and how the results prove least privilege.

```text
In this lab, I configured and validated an inbound security rule applying the principle of least privilege. The configuration permits inbound TCP traffic on port 8080 specifically from the single authorized host 10.60.6.4 (Grid Beacon). To ensure access was scoped strictly rather than using a broad Any source, I conducted two testing steps against the active Python listener. Testing from 10.60.6.4 resulted in an ALLOWED verdict, verifying that legitimate functional access is open. Testing from 10.60.6.10 resulted in a DENIED verdict via the default fallback rule, proving that non-permitted sources are properly restricted. Together, these tests demonstrate both positive access authorization and negative enforcement control.
```

## Required Evidence

Save screenshots in `assets/screenshots/week-07/`:

- `week07-lab04-final-rule.png`
- `week07-lab04-grid-beacon-allowed.png`
- `week07-lab04-other-source-denied.png`

Open each image at full size before submission. Confirm that no password, Bastion shareable URL, browser address bar, or unrelated private information is visible.

## Analysis Questions

**Analysis Question 1.** Why are one ALLOWED result and one DENIED result stronger evidence together than either result alone? (Minimum 4 sentences.)

```text
Testing both an allowed result and a denied result provides complete validation of the least-privilege security model. An ALLOWED result alone only proves that legitimate traffic reaches the application, but it does not confirm if the rule is too open (such as allowing 0.0.0.0/0). Conversely, a DENIED result alone only confirms traffic is blocked, which could mean the service is completely unreachable or offline. Combining an ALLOWED test for the intended source with a DENIED test for an unintended source proves that access is explicitly granted to the authorized host while restricting all others.
```

**Analysis Question 2.** If the Other Test Source were ALLOWED, what would you inspect before changing anything? (Minimum 4 sentences.)

```text
If the Other Test Source (10.60.6.10) returned an ALLOWED verdict, I would immediately inspect the inbound security rule order and source scope before altering any configuration. I would first verify whether the student Allow rule was incorrectly set to an overly broad source scope like Any or a wide CIDR block rather than 10.60.6.4/32. Next, I would check if another high-priority rule (prior to the default deny) was matching and allowing the traffic. Finally, I would verify that the rule evaluation order hasn't bypassed the intended fallback block before making any rule edits.
```

**Analysis Question 3.** How does this evidence distinguish configuration from observed enforcement? (Minimum 3 sentences.)

```text
Active testing distinguishes intended configuration from observed enforcement by verifying that live network behavior matches static firewall logic. Static rules show how traffic should be evaluated, but live testing proves the service is actively listening and rules are actively being evaluated in the expected priority sequence. Testing both allowed and denied paths confirms that traffic filtering is enforced dynamically on actual packets rather than existing only as passive configuration settings.
```

## Submission Checklist

- [ ] Final rule screenshot shows narrow source and TCP 8080

- [ ] Grid Beacon `ALLOWED` evidence captured

- [ ] Other Test Source `DENIED` evidence captured

- [ ] Deliverable 2 statement completed

- [ ] `week07-lab04-final-rule.png` captured

- [ ] `week07-lab04-grid-beacon-allowed.png` captured

- [ ] `week07-lab04-other-source-denied.png` captured

- [ ] All protected rules in my actual baseline were left unchanged.

- [ ] Every rule I created or edited used priority 200–999.

- [ ] No password, Bastion URL, or browser address bar appears in my files.

- [ ] This worksheet is committed to `week-07/labs/lab-04-prove-the-guard-works.md`.

## GitHub / Lab Portal Submission

1. Open **Week 7 → Lab 04: Prove the Guard Works ★ Deliverable 2** in the CyberFoundations Lab Portal.
2. Complete every worksheet field and confirm the listed evidence filenames.
3. Upload screenshots to `assets/screenshots/week-07/`.
4. Confirm your portfolio repository is connected, then choose **Submit to GitHub**.
5. Open the committed worksheet and each image on GitHub to verify formatting, legibility, and redaction.

*CyberVisionaries Institute · CyberFoundations · Tier I*

**Interpreting results:** A TCP connection success proves reachability at that moment, not which rule matched. A timeout marked DENIED means traffic appears blocked; it is not a flow-log verdict and can also reflect a routing or host-firewall problem. SERVICE_NOT_LISTENING means an immediate refusal; check the listener before altering rules. TEST_ERROR is not denial evidence. Each test opens a fresh connection; after a rule change, allow Azure to finish, refresh the ledger and wait at least 10 seconds before retesting. Never invent a successful or denied result.
