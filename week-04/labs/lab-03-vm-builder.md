# Week 4 Lab — Build Your First Virtual Machine (VM Builder Simulator) ★ Deliverable 1

**Student Name:** Tiwonge Kafera

**Date Completed:** 10/26/26

**Module:** 1 — Digital Infrastructure & CLI | **Week:** 4  
**Submission Path:** `week-04/labs/lab-03-vm-builder.md`

---

## Overview

Lessons 3 and 4 taught you what a virtual machine is and how one lives and dies. This capstone lab hands you the keys: in the VM Builder Simulator you'll provision a machine of your own through the full five-question wizard (Part A), handle whatever provisioning throws at you (Part B), and run the complete lifecycle — stop, start, snapshot, delete — while a billing meter runs (Part C). This lab is the heart of **★ Deliverable 1: VM concepts + CLI screenshots** — its two screenshots join the two from Labs 01 and 02 in your portfolio repo.

**The simulator will push back on purpose.** Taken names, refused passwords, quota limits, and a region that sometimes fails are all part of the exercise — reading an error calmly and fixing the right thing *is* the skill being graded. Errors here are progress, not mistakes.

---

## Lab Environment / Pre-Lab Check

| Component | Details |
|---|---|
| Environment | CyberFoundations VM Builder Simulator — runs entirely in your web browser; nothing to install, no account needed, no real servers, no real money |
| Prerequisite | Week 4, Lessons 3 and 4 completed; Labs 01 and 02 recommended first |
| Concept Checks | The simulator gates progress on four Concept Checks — all four are covered in Lessons 3 and 4 |
| Time | Plan for 30–45 minutes, including this worksheet |

### How to Open the Simulator — Step by Step

1. Open your web browser (Chrome, Edge, Firefox, or Safari all work — use a computer, not a phone, so your screenshots capture the full screen).
2. Go to this address (you can also click the **VM Builder Simulator** link on the Lab Portal's Week 4 page — same destination):

```
https://cybervisionariesinstitute.github.io/cyberfoundations-simulators/vm-builder.html
```

3. Confirm you're in the right place: you should see a dark purple header reading **"Foundry District Cloud Annex"** and a page titled **"Mission Briefing: Provision Your First Virtual Machine."** If you see anything else, re-check the address.
4. Read the Mission Briefing all the way down — especially the **"How to use this simulator"** box. It explains the six steps, the Concept Checks, and the two screenshot moments.
5. Get your screenshot tool ready before you begin: **Windows:** press `Win + Shift + S` · **Mac:** press `Cmd + Shift + 4`, then drag to capture. You will need it twice, at moments the simulator announces with a 📸 banner.
6. Keep this worksheet open in a **second browser tab**, side by side with the simulator — you'll record answers as you go.

**⚠️ One thing to know before you start:** refreshing the simulator page **resets the entire simulation** — nothing is saved between visits. That's safe (it's a training environment), but capture each screenshot when prompted, before moving on, and don't refresh mid-run unless you want a fresh start.

**Also before you start:** have Lesson 4's Resource Pack open to its Quick Reference page (the lifecycle/billing table). You'll want it.

---

## Part A — Provision Your Machine

### Step 1 — Name It Like a Professional

Work through the Basics screen: choose a VM name that passes the naming rules *and* would tell a stranger what this machine is for. Note: at least one obvious name is already taken — if you hit **NameNotAvailable**, that's the simulator doing its job; pick another and record what happened.

The name you chose, and whether you hit the taken-name error first:

```
cyber-kafera
```

### Step 2 — Choose a Region, and Say Why

Pick a region on the Basics screen. Each option describes a trade-off (latency, capacity). Record your choice and one sentence of reasoning — professionals never pick a region at random.

Your region and your reasoning:

```
Foundry Central because I want my commands to reach the VM fast.
```

### Step 3 — Choose Your Guest OS

Pick an operating system and record why. There's no wrong answer, but there is a *reasoned* answer — think about which shell you'd rather manage it with, and what the license fee note tells you.

Your OS choice and reasoning:

```
I picked Ubuntu server because it does not require a licensing fee.
```

### Step 4 — Size It, and Do the Money Math

Pick a size tier. Record its specs and hourly rate, then do Lesson 4's monthly reflex: hourly rate × 24 × 30. Would you leave this machine running for a month?

Your size tier, its specs, and its hourly rate:

```
I choose B2s because it is the same size my lab will need
```

Your monthly math (rate × 24 × 30):

```
0.046*24*30= $33.12
```

### Step 5 — Create the Admin Account

Create the administrator username and password. The simulator blocks guessable usernames and refuses weak passwords — if it pushes back, record what it rejected and what you learned from the rejection.

What (if anything) got rejected, and your final username (never record the password):

```
username- foundry-admin
password-lintrail20*K
```

### Step 6 — Capture Screenshot 1 (REQUIRED — Deliverable 1)

On the Review & Create screen — before you click Create — take the screenshot the simulator prompts for: your full configuration summary, including the total hourly cost. Name it exactly **`vm-config-summary.png`**. Upload instructions are in the GitHub Commit section.

---

## Part B — Survive Provisioning

### Step 1 — Create, and Read What Happens

Click **Create Virtual Machine** and watch the provisioning stages. Depending on your Part A choices, provisioning may fail with a readable error — **QuotaExceeded** (your size is bigger than the subscription allows) or **AllocationFailed** (your region ran out of capacity). If it fails: read the error, identify which wizard choice it points at, fix *that one thing*, and retry.

What happened on your first Create attempt (success, or the exact error name):

```
VM was created successfully
```

If you hit an error: what it told you, and what you changed:

```
no error
```

### Step 2 — Confirm You're Running

Once provisioning completes, confirm on the dashboard: status **Running**, and the billing meter ticking at the rate your size card promised. Record the rate the meter shows.

The running rate shown on your dashboard:

```
$11.45
```

---

## Part C — Run the Full Lifecycle

Complete all four lifecycle tasks on the dashboard, in this order, and answer the simulator's Concept Checks as they appear.

### Step 1 — Stop, and Watch the Meter

Stop (deallocate) your VM. Watch what happens to the billing rate — it should not go to zero. Record the stopped rate and what it's paying for.

The stopped rate, and what a stopped VM still pays for:

```
$12.08
```

### Step 2 — Start It Again

Start the VM and confirm the full rate resumes. One sentence: where did your files go while it was stopped?

Your one-sentence answer:

```
files went to disk storage.
```

### Step 3 — Take a Snapshot

Take a snapshot and record its name from the snapshot list. One sentence: what exactly did you just photograph, and when would you be glad you have it?

Snapshot name and your one-sentence explanation:

```
snapshot-1
This snapshot shows the current state of the VM. This will come in handy if we need to audit anything in the future and compare it to other future VM if necessary.
```

### Step 4 — Capture Screenshot 2 (REQUIRED — Deliverable 1)

With your VM **Running** and at least one snapshot visible, take the dashboard screenshot. Name it exactly **`vm-dashboard-running.png`**.

### Step 5 — Delete, and Read the Warning

Delete your VM. Read the confirmation dialog before you click — record what it warns you is about to happen, then confirm and record your final total cost from the completion banner.

What the delete warning said, in your own words:

```
deleting the VM will destroy the disk as well as stop all billing. All information will be lost.
```

Your final simulated cost:

```
$30.96
```

---

## Analysis Questions

**Analysis Question 1.** Your stopped VM kept billing a small amount. Explain the "locker fee" in your own words — what physical thing still exists when a VM is stopped, and why is deletion the only true zero? *(Minimum 2 sentences.)*

```
The virtual disk exists still when a VM is stopped to help preserve data. Deleting the VM is the only true zero because it frees up the storage space.
```

**Analysis Question 2.** Lesson 4 revealed that your real Weeks 6–12 lab machines are stamped from golden snapshots your instructor built. Using what you did in Part C, Step 3, explain how a golden snapshot works and why it means every student's machine starts identical. *(Minimum 3 sentences.)*

```
It acts as a master disk image which contact pre-configured operating system. When the lab is deployed the system creates a copy rather than building from scratch. This makes each machine to have identical configuration.
```

**Analysis Question 3.** If you hit a provisioning error in Part B (or even if you didn't): why do you think this lab *wants* you to encounter errors in a simulator before Week 6 hands you real infrastructure? *(Minimum 2 sentences.)*

```
This lab wants us to encounter errors so that we can learn and troubleshoot them here before we start practicing in the live environment. Making errors in a simulation environment is different from real infrastructure because it can be costly.
```

**Analysis Question 4.** Defend your Part A size choice to an imaginary manager watching the budget: why was your tier the right rent for this job, and what would have made you pick a bigger or smaller one? *(Minimum 2 sentences.)*

```
The choice that was made was right because we stayed within budget of less than $33. The tier was perfect for the class size. If I chose a bigger one, then it could have costed much more and still have more unused stuff.
```

---

## Submission Checklist

- [x] VM named within the rules, taken-name error handled if encountered (Part A, Step 1)

- [x] Region chosen with recorded reasoning (Part A, Step 2)

- [x] Guest OS chosen with recorded reasoning (Part A, Step 3)

- [x] Size tier recorded with hourly rate and monthly math (Part A, Step 4)

- [x] Admin account created; any rejections recorded — password NOT written anywhere (Part A, Step 5)

- [x] **REQUIRED:** `vm-config-summary.png` captured at the Review screen (Part A, Step 6)

- [x] First Create attempt recorded — success or exact error + fix (Part B)

- [x] Stop / start / snapshot completed with meter observations recorded (Part C, Steps 1–3)

- [x] **REQUIRED:** `vm-dashboard-running.png` captured with VM running + snapshot visible (Part C, Step 4)

- [x] VM deleted; warning paraphrased and final cost recorded (Part C, Step 5)

- [x] All four Concept Checks passed in the simulator

- [x] All four Analysis Questions answered (minimum sentence counts met)

- [x] This file is committed to your portfolio repo at `week-04/labs/lab-03-vm-builder.md`

---

## GitHub Commit Subsection — ★ Deliverable 1

This lab completes **Deliverable 1: VM concepts + CLI screenshots**. Two things get committed:

**1. This worksheet, via the Lab Portal:**

1. Go to the CyberFoundations Lab Portal and sign in.
2. Open **Week 4 → Lab 03: Build Your First Virtual Machine**.
3. Fill in the worksheet fields and click **Submit to GitHub**. The Portal commits the completed file to `week-04/labs/lab-03-vm-builder.md`.

**2. Your four Deliverable 1 screenshots, uploaded to `assets/screenshots/week-04/`:**

| Screenshot | From | Filename |
|---|---|---|
| Permissions audit | Lab 01 | `cli-permissions-audit.png` |
| Archive investigation | Lab 02 | `cli-search-investigation.png` |
| VM configuration summary | Lab 03, Part A | `vm-config-summary.png` |
| VM dashboard, running + snapshot | Lab 03, Part C | `vm-dashboard-running.png` |

For each: on GitHub.com, navigate to `assets/screenshots/week-04/`, click **Add file → Upload files**, drag the image in (exact filenames above — lowercase, hyphens, no spaces), and **Commit changes**. Then open each uploaded image, right-click directly on it, choose **Copy image address** (Chrome/Edge) or **Copy Image Link** (Firefox), and paste the two VM links into the embeds below:

![VM configuration summary](https://github.com/txkafe20/CyberVisionariesInstitute-cyberfoundations-student-portfolio/blob/main/vm-config-summary.png)

![VM dashboard — running, with snapshot](https://github.com/txkafe20/CyberVisionariesInstitute-cyberfoundations-student-portfolio/blob/main/vm-dashboard-running.png)

**If right-click doesn't show that option:** click the small download-arrow icon in the top-right of the image preview instead, then copy the URL from your browser's address bar.

**Commit message tip (from Lesson 4):** when GitHub asks for a commit message on your uploads, write one that says what the work is — *"Add Deliverable 1: VM lifecycle and CLI evidence"* — not "stuff." An employer reading your repo sees discipline in details like that.

---

*CyberVisionaries Institute · Cyber Foundations · Tier I*
