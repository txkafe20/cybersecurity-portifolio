# Week 6 Lab 01 — Claiming Your Room in Cloud Heights

**Student Name:** Tiwonge Kafera

**Date Completed:** 11/1/2026

**Module:** 2 — Networking & Cloud Foundations | **Week:** 6  
**Submission Path:** `week-06/labs/lab-01-claiming-your-room.md`

---

> ### 🔒 Cloud Heights Security Rule
> Your Bastion link and Cloud Heights password are **private access credentials**. Never paste either into a worksheet, screenshot, GitHub repository, Circle post, or chat message. When taking screenshots, crop out the browser address bar and all login information.

---

## Overview

Week 5 was practice. This week the machine is real. Cloud Heights is a live Ubuntu 22.04 server running in Azure, and one of its rooms has **already been reserved for you** — you do not create it, provision it, or pay for it. Your job in this lab is to walk in the front door, prove you are standing inside your own room, and understand where that room came from.

This is a **guided** lab. Every step tells you what to do and what to record. Expect 30–40 minutes.

---

## Lab Environment / Pre-Lab Check

| Component | Details |
|---|---|
| Environment | Cloud Heights — live Ubuntu 22.04 VM in Azure, reached through Azure Bastion in your browser |
| Access | Lab Portal → **My Lab Environment** → your Cloud Heights card |
| Username | `analyst` |
| Password | Provided to you separately. Never typed into this worksheet. |
| Commands used | `hostname`, `whoami`, `pwd` |
| Auto-shutdown | Your VM stops automatically after 15 minutes of inactivity. A warning with **Keep Working** appears first. |

**Before you start:** open **My Lab Environment** in the Portal. If your VM shows **Stopped**, click **Start VM** and wait until the status reads **Running** — this takes a minute or two. Only then click **Open Cloud Heights**.

---

## Part A — Walking In

### Step 1 — Start the Room

In **My Lab Environment**, check your Cloud Heights status. Start the VM if it is stopped and wait for **Running**.

The status you saw before you started, and the status you saw after:

```
start VM machine
Running

```

### Step 2 — Open Cloud Heights and Sign In

Click **Open Cloud Heights**. A browser-based session opens through Azure Bastion. Sign in with username `analyst` and the password you were given separately.

**Do not record the password, the link, or any part of the login screen anywhere.**

Describe what you saw once the session opened — what kind of screen greeted you:

```
welcome to Ubuntu screen popped up with dark background.
```

### Step 3 — Ask the Machine Its Name

Run:
```
hostname
```
Output:

```
student 35
```

### Step 4 — Ask Who You Are

Run:
```
whoami
```
Output:

```
analyst
```

### Step 5 — Ask Where You Are Standing

Run:
```
pwd
```
Output:

```
home/analyst
```

---

## Part B — What Those Three Answers Prove

### Step 1 — Read Them as Evidence

Each of those three commands answered a different question: *which machine*, *which identity*, *which location in the filesystem*. Together they are the proof that you are inside your own room and not somebody else's.

Explain, in your own words, what each output proves:

```
The hostname command proves exactly which target machine or server system that I am logged into. The whoami command proves the specific user account identity and privilege level running the current session. Finally, the pwd command proves my exact current working directory location within the filesystem. Together, these three outputs verify my identity, environment, and precise location on the system.

```

### Step 2 — Capture Your Evidence

Take a screenshot of your terminal showing the three commands and their outputs.

**Required filename:** `bastion-session.png`

**Crop rules — not optional.** The screenshot must show the terminal and prompt. It must **not** show the browser address bar, the Bastion link, any login screen, or any password field. Crop before you upload.

Upload it to `assets/screenshots/week-06/` in your portfolio repository, then paste its link here:

![Cloud Heights session — hostname, whoami, pwd](https://github.com/txkafe20/CyberVisionariesInstitute-cyberfoundations-student-portfolio/blob/main/bastion-session.png?raw=true)

---

## Part C — Where Your Room Came From

### Step 1 — The Golden Image Idea

Every student's Cloud Heights room was built from the **same standardized image** — a known-good snapshot of a configured Ubuntu machine. Nobody hand-built 20 servers. One machine was configured correctly once, captured, and stamped out repeatedly.

Explain in your own words what a standardized (golden) image is and why an organization would build one:

```
A standardized or "golden" image is a pre-configured, known-good snapshot of an operating system, such as a configured Ubuntu machine—that serves as a verified baseline template. Instead of manually building multiple servers from scratch, an organization configures a single machine correctly once and stamps out identical copies repeatedly. Organizations build golden images to ensure consistent configurations, eliminate manual setup errors, and dramatically speed up deployment times.
```

### Step 2 — Same Start, Different Rooms

Your room started identical to everyone else's, and from today it starts to diverge as you work in it.

Explain what stays the same across all the rooms and what becomes yours alone:

```
While every room initially starts out identical as a shared copy of the base image, they begin to diverge as users actively work and make modifications within their own environments. The foundational operating system template and baseline configuration remain the same across all rooms, but the custom files, unique work, and specific changes made during active use become mine alone.

```

---

## Analysis Questions

**Analysis Question 1.** Why does it matter that a standardized image can be *restored*, not just deployed? Describe a realistic situation where restoring from a known-good image is the fastest safe fix. *(Minimum 3 sentences.)*

```
Being able to restore a standardized image matters because operating systems inevitably experience file corruption, misconfigurations, or security compromises during active use. For example, if an analyst accidentally executes a destructive command or gets infected by malware during a lab session, manually troubleshooting every altered file would take hours. Restoring from a known-good golden image provides the fastest safe fix by instantly wiping away the damage and returning the environment to a clean, trusted baseline state.

```

**Analysis Question 2.** Conceptually, how is a snapshot different from a separate backup? Consider what each one protects against and where each one lives. *(Minimum 3 sentences.)*

```
Conceptually, a snapshot captures the state and incremental changes of a virtual machine and typically lives on the same primary storage array as the active environment. In contrast, a separate backup is an independent copy of data exported to decoupled or secondary storage (such as an off-site server or separate cloud repository). While snapshots protect against quick, short-term operational errors or bad software updates for rapid rollbacks, separate backups protect against catastrophic hardware failures, ransomware encrypting primary storage, or site-wide disasters.

```

**Analysis Question 3.** Your room was reserved for you rather than created by you. What does that tell you about how cloud access is usually handed out in a real organization, and why would an employer prefer that model? *(Minimum 2 sentences.)*

```
The fact that your room was pre-reserved rather than built from scratch indicates that in a real organization, cloud environments and user access are centrally provisioned and managed by IT administrators. Employers prefer this model because it ensures strict security compliance, enforces standardized baseline configurations across all team members, and prevents unauthorized resource sprawl or misconfigurations.

```

---

## Submission Checklist

- [x] VM started from My Lab Environment and confirmed **Running** (Part A, Step 1)

- [x] Signed in through Bastion as `analyst` — no credentials recorded anywhere (Part A, Step 2)

- [x] `hostname`, `whoami`, and `pwd` run and outputs recorded (Part A, Steps 3–5)

- [x] Explained what each of the three outputs proves (Part B, Step 1)

- [x] `bastion-session.png` captured, address bar and login data cropped out, uploaded to `assets/screenshots/week-06/` (Part B, Step 2)

- [x] Standardized/golden image explained in your own words (Part C)

- [x] All three Analysis Questions answered (minimum sentence counts met)

- [x] This file is committed to your portfolio repo at `week-06/labs/lab-01-claiming-your-room.md`

---

## GitHub Commit Subsection

1. Open **Week 6 → Lab 01: Claiming Your Room in Cloud Heights** in the Lab Portal.
2. Fill in the worksheet fields — they match this file, in the same order.
3. Connect your GitHub account if you haven't already, and select your portfolio repo.
4. Click **Submit to GitHub**. The Portal commits the completed file to `week-06/labs/lab-01-claiming-your-room.md`.
5. Upload `bastion-session.png` to `assets/screenshots/week-06/` in your repo before you submit.

---

*CyberVisionaries Institute · Cyber Foundations · Tier I*
