# Week 3 Lab 03 — Command Line Scavenger Hunt (CLI Simulator)

**Student Name:** Tiwonge Kafera

**Date Completed:** 10/1/26

**Module:** 1 — Digital Infrastructure & CLI | **Week:** 3  
**Submission Path:** `week-03/labs/lab-03-command-line-scavenger-hunt.md`

---

## Overview

Labs 01 and 02 walked you through each command step by step. This lab is Week 3's wrap-up challenge: a deeper, more independent folder structure with three hidden files to track down, using the navigating and reading commands from Lessons 3A/3B, the creating and organizing commands from Lesson 3C, and your own judgment about when to ask for help. There's less hand-holding here on purpose — this is your chance to prove to yourself that the blinking cursor from the start of Lesson 3A doesn't intimidate you anymore.

**Nothing here can break anything real.** Same consequence-free CLI Simulator as Labs 01 and 02. Getting "lost" in the folder tree costs you nothing but a few extra `cd` moves.

---

## Lab Environment / Pre-Lab Check

| Component | Details |
|---|---|
| Environment | CyberFoundations CLI Simulator (browser-based, inside the Lab Portal) |
| Shell | Your choice — bash or PowerShell |
| Prerequisite | Labs 01 and 02 completed |

**Before you start:** here is how to open this lab's practice area.

1. Sign in to the Lab Portal and open **CLI Simulator** (in the top menu, or the **Open the CLI Simulator** link on the Week 3 page).
2. Scroll down to the heading **Week 3 Labs**.
3. Pick **one** box: **Foundry District Archive Room — Bash** or **Foundry District Archive Room — PowerShell**. Each box is its own terminal; the box decides the shell.

This tree goes several folders deeper than Labs 01 and 02, and includes a few similarly-named folders on purpose — read carefully before you `cd` into anything.

**How the 8 challenges match this worksheet.**

| Challenge | What you do | Worksheet part |
|---|---|---|
| 1 | Find and read the shift log file | Parts A and B |
| 2 | Find and read the maintenance note file | Parts A and B |
| 3 | Find and read the supply inventory file | Parts A and B |
| 4 | Create the `sorted-findings` folder in your home folder | Part C, Step 1 |
| 5 | Move the shift log into `sorted-findings` | Part C, Step 2 |
| 6 | Move the maintenance note into `sorted-findings` | Part C, Step 2 |
| 7 | Move the supply inventory into `sorted-findings`, then list the folder | Part C, Steps 2–3 |
| 8 | Look up an unfamiliar command (`chmod` in bash, `Get-Acl` in PowerShell) | Part D, Step 1 |

**This is not one long terminal session.** Each challenge loads its own prepared files and starts in your home folder, `/home/archivist`. The terminal still shows your earlier commands, but your location and files reset to that challenge's setup — that is normal, not lost work. At the start of each challenge, run `pwd`/`Get-Location` and `ls`/`dir`. Press **Next** (or Enter on an empty line) after each goal is met, and use **Previous** to review. **Restart challenge** gives fresh files and keeps challenges you already saved. Your worksheet answers are saved separately on this page.

**Command reference:** at the top of the CLI Simulator page, click **Command reference**, then search (for example "move" or "folder") and filter to **Bash** or **PowerShell**. Its examples use sample names that may not exist in your challenge.

---

## Part A — The Hunt

Find all three of the following, hidden at different depths in the Archive Room tree:

- A file related to a **shift log**
- A file related to a **maintenance note**
- A file related to a **supply inventory**

**Challenges 1–3** — one file per challenge, each starting fresh in `/home/archivist`. For each one, use `pwd`/`Get-Location` and `ls`/`dir` as many times as you need while you search, then record the **full path** once you find it.

Shift log file — full path once found:

```
home/morgan/logs

```

Maintenance note file — full path once found:

```
home/archivist/records/records-2025/maintenance-note.txt

```

Supply inventory file — full path once found:

```
home/archivist/records/records-2024/supply-inventory.txt
```

---

## Part B — Read and Report

Reading each file is what completes Challenges 1, 2, and 3. For each of the three files you found in Part A, use `cat` (bash) or `type`/`Get-Content` (PowerShell) to read it and record what it says.

Shift log contents:

```
shift log-foundry District Archive room
07:00 -Archive opened, no incidents overnight.
15:00 - Routine filing complete
```

Maintenance note contents:

```
conveyor belt 3 serviced, next check due in 90 days
```

Supply inventory contents:

```
supply inventory -Q4 2024
Gloves -400 units
Masks -250 units
Tape - 60 rolls
```

---

## Part C — Organize Your Findings

Now that you've located and read all three files, clean up after yourself the way a professional would — don't leave your findings scattered across the tree.

### Step 1 — Create a Sorted-Findings Folder

**Challenge 4.** Create a new folder called `sorted-findings` in your home directory, `/home/archivist` (bash: `mkdir sorted-findings`; PowerShell: `New-Item sorted-findings -ItemType Directory`). It must be a real folder — an empty file with that name does not count.

Command you ran:

```
mkdir sorted-findings
```

### Step 2 — Move All Three Files Into It

**Challenges 5, 6, and 7** — one move per challenge. Move the shift log, maintenance note, and supply inventory files — the same three you found in Part A — into `sorted-findings`, using `mv` (bash) or `Move-Item` (PowerShell). A copy does not count: the file must end up in `sorted-findings` **and** be gone from its old folder, with its contents unchanged.

Each move challenge is freshly prepared from your home folder. Challenge 5 already has an empty `sorted-findings` folder. Challenge 6 already has the shift log in it. Challenge 7 already has the shift log and maintenance note in it — you move the third file, and all three must be there, intact. If you moved around, return home first with `cd ~`.

Commands you ran:

```
mv shift-log.txt sorted findings
mv operations/ops-log/shift-log.txt sorted findings
mv records/ records-2024/supply-inventory.txt sorted findings/
mv maintenance-note.txt sorted findings
```

### Step 3 — Confirm the Move

**Challenge 7 (last part).** In the same challenge as your last move, list the contents of `sorted-findings` (`ls sorted-findings` or `Get-ChildItem sorted-findings`) to confirm all three files are now there.

Command you ran:

```
ls
```

Output:

```
maintenance-note.txt shift-log.txt supply-inventory.txt

```

---

## Part D — When You Get Stuck

At some point in the Archive Room, you'll likely run across a command or folder name you don't immediately recognize.

### Step 1 — Ask the Terminal

When that happens, use `--help`, `man`, or `Get-Help` instead of guessing. **Challenge 8** asks you to look up one specific command (`chmod --help` in bash, `Get-Help Get-Acl` in PowerShell) — you can record that one, or anything else you looked up while exploring. Record what you looked up and what you learned.

Command or term you looked up:

```
chmod
```

What the help text (or the folder's contents) told you:

```
command not found
```

### Step 2 — Describe a Wrong Turn

Everyone takes at least one wrong turn in a tree this size. Describe one moment you ended up somewhere unexpected, and how you used `pwd`/`Get-Location` and `cd ..` to recover.

```
(your answer here — minimum 2 sentences)
```

---

## Analysis Questions

### Analysis Question 1

Which of the three files in Part A took the longest to find, and what was it about the tree's structure (depth, similarly-named folders, etc.) that made it harder?

```
reading logs file because I forgot to change directory to logs and the open the file.
```

### Analysis Question 2

Compare how you felt starting this lab to how you felt at the very start of Lesson 3A, looking at a blank blinking cursor for the first time. What changed?

```
I was confused on where to start especially where to type but after attending the live class session I was able to start using the CLI. After completing 3A at least I felt confident changing directory, making a copy, creating new folder and reading a file.
```

### Analysis Question 3

Week 4 moves from managing your own files to controlling who's allowed to do what to them — permissions — plus your first look at what a virtual machine is. Based on everything you've practiced this week, what's one thing you're curious about or looking forward to?

```
I am looking forward to learning more about virtual machines and managing files.
```

---

## Submission Checklist

- [x] All three target files located, with full paths recorded (Part A)

- [x] All three target files read and their contents recorded (Part B)

- [x] `sorted-findings` folder created and all three files moved into it, confirmed with a listing (Part C)

- [x] At least one command or term looked up with `--help`/`man`/`Get-Help`, with what you learned recorded (Part D, Step 1)

- [x] One wrong-turn moment described, including how you recovered (Part D, Step 2 — minimum 2 sentences)

- [x] All three Analysis Questions answered (minimum sentence counts met)

- [x] This file is committed to your portfolio repo at `week-03/labs/lab-03-command-line-scavenger-hunt.md`

---

## GitHub Commit Subsection

Same mechanism as Labs 01 and 02: fill out this lab's worksheet in the **CyberFoundations Lab Portal** (Week 3 → Lab 03) and click **Submit to GitHub** — the Portal commits the completed file to `week-03/labs/lab-03-command-line-scavenger-hunt.md` automatically. No manual typing or commit needed.

**📌 Optional:** a CLI Simulator session screenshot can be added the same way as Labs 01 and 02 — upload to `assets/screenshots/week-03/`, then right-click the uploaded image and choose **Copy image address**/**Copy Image Link** to embed it — but it isn't required and won't affect your grade.

---

*CyberVisionaries Institute · Cyber Foundations · Tier I*
