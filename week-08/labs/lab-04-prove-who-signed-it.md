# Week 8 Lab 04 — Prove Who Signed It

**Student Name:** Tiwonge Kafera

**Date Completed:** 11/14/26

**Module:** 3 — Practical Cryptography  
**Submission Path:** `week-08/labs/lab-04-prove-who-signed-it.md`

> ## STOP — Protect secrets
> Never paste, upload, commit, or screenshot a password, passphrase, or private key. Do not run `cat` on any private-key file. Submit only the worksheet and the named screenshots. Keep all cryptographic files on the VM.

## How to Use This Lab

- **[TERMINAL]** means type or paste the command into the Cloud Heights terminal.
- **[WORKSHEET]** means type your response in this lab worksheet.
- Run commands in the order shown. Do not type the sample output.
- If your result does not match the stated success check, stop at the Troubleshooting box. Do not improvise with `sudo`, package installation, Azure settings, or SSH server configuration.

## Mission

Create a separate signing key pair, sign the incident report, verify the original, then prove that the same signature fails against a changed copy.

## What You Already Know

A digital signature uses a private key to sign and the matching public key to verify. Verification supports integrity and authenticity claims about the key. It does not encrypt the report or prove which human physically used the private key.

## Lab Environment / Pre-Lab Check

**[TERMINAL] Run:**

```bash
whoami
cf-week8-check
cd ~/cloud-heights/week8-cryptography
test ! -e keys/week8_signing_private.pem &&   test ! -e keys/week8_signing_public.pem &&   echo "READY: signing-key paths are unused" ||   echo "STOP: signing-key file already exists"
```

**Continue only if:** `whoami` prints `analyst`, all checks report `PASS`, and the final line begins with `READY`.

If it begins with `STOP`, do not overwrite or delete anything. Ask the instructor whether to resume or reset.

### If the VM Stops

Return to **My Lab Environment** in the Lab Portal and start your assigned VM. A stopped or deallocated VM is not deleted; saved disk files remain.

## Predict First

**[WORKSHEET]** Should the original signature verify after the report content changes? Explain.

```text
No, the original signature will not verify after the report content changes. A digital signature is created using a cryptographic hash of the exact original file contents. Modifying even a single character in the report produces a completely different hash value, causing the signature verification to fail and proving that the file's integrity was compromised.
```

## Guided Steps

### Step 1 — Generate the Private Signing Key

**[TERMINAL] Run:**

```bash
openssl genpkey   -algorithm RSA   -aes-256-cbc   -pkeyopt rsa_keygen_bits:2048   -out keys/week8_signing_private.pem
```

Create one **signing-key passphrase** when prompted. You will use this same passphrase in Steps 2 and 4. Do not record or screenshot it.

### Step 2 — Derive the Public Key

**[TERMINAL] Run:**

```bash
openssl pkey   -in keys/week8_signing_private.pem   -pubout   -out keys/week8_signing_public.pem
```

Enter the signing-key passphrase from Step 1.

### Step 3 — Confirm Both Files Exist

**[TERMINAL] Run:**

```bash
ls -l keys/week8_signing_private.pem keys/week8_signing_public.pem
```

Do not display the private-key contents.

### Step 4 — Sign the Original Report

**[TERMINAL] Run:**

```bash
openssl dgst -sha256   -sign keys/week8_signing_private.pem   -out signatures/incident-report.sig   evidence/incident-report.txt
```

Enter the signing-key passphrase from Step 1.

### Step 5 — Verify the Original Report

**[TERMINAL] Run:**

```bash
openssl dgst -sha256   -verify keys/week8_signing_public.pem   -signature signatures/incident-report.sig   evidence/incident-report.txt
```

**Required result:** `Verified OK`

**Evidence moment:** Capture this result as `week08-lab04-signature-valid.png`.

### Step 6 — Change a Copy

**[TERMINAL] Run:**

```bash
cp evidence/incident-report.txt signatures/incident-report-modified.txt
echo "Change: post-signature modification." >> signatures/incident-report-modified.txt
```

### Step 7 — Test the Original Signature Against the Changed Copy

**[TERMINAL] Run:**

```bash
openssl dgst -sha256   -verify keys/week8_signing_public.pem   -signature signatures/incident-report.sig   signatures/incident-report-modified.txt
```

**Required result:** `Verification failure`. Additional OpenSSL error lines are normal for this intentional failure.

**Evidence moment:** Capture this result as `week08-lab04-signature-invalid-after-change.png`.

## Stop & Check

Do not create the changed copy until Step 5 returns `Verified OK`.

### Troubleshooting

- Step 2 or 4 cannot read the private key: re-enter the Step 1 signing-key passphrase.
- Step 5 reports failure: confirm you used the original report, public key, and signature paths exactly as shown.
- Step 7 reports `Verified OK`: run `tail -n 2 signatures/incident-report-modified.txt`. If the Change line is missing, repeat Step 6 once and rerun Step 7.

## Explain

**[WORKSHEET]** In 4–5 sentences, explain both verification results and why signing is not encryption.

```text
 In the first verification test, the original report returned Verified OK because its cryptographic hash matched the decrypted hash stored inside the digital signature. In the second test, modifying the copy altered the file's contents and produced a new hash, which caused the verification check to fail. This process demonstrates that digital signatures protect data integrity and authenticity rather than confidentiality. Unlike encryption, which scrambles file contents to keep them secret, digital signing leaves the original document completely readable in plaintext and simply attaches a separate signature file.

```

## Analysis Questions

1. What does `Verified OK` establish within this lab?

```text
It establishes integrity (the incident-report.txt file has not been altered since it was signed) and authenticity (the signature was generated using the specific private key corresponding to week8_signing_public.pem).

```

2. Why did the changed copy fail verification?

```text
2. Why did the changed copy fail verification?
Appending a new line of text changed the file's data, which generated a completely different SHA-256 hash. Because that new hash no longer matched the hash signed by the private key, OpenSSL rejected it with Verification Failure.

```

3. Why is the signed report still readable?

```text
Digital signing is not encryption. The openssl dgst -sign command hashes the report and encrypts only the hash to create a separate .sig file, leaving the original text file untouched and readable in plaintext.

```

4. Why does the signature alone not prove which human used the private key?

```text
A cryptographic signature only proves that a specific private key file (and its passphrase) was used to sign the document. If the private key and passphrase were shared, stolen, or left accessible on a compromised machine, anyone with access to them could generate the exact same valid signature.

```

## Required Evidence

- `assets/screenshots/week-08/week08-lab04-signature-valid.png`
- `assets/screenshots/week-08/week08-lab04-signature-invalid-after-change.png`

## Submission Checklist

- [ ] The original returned `Verified OK`.

- [ ] The changed copy returned `Verification failure`.

- [ ] The private key and passphrase were never displayed or submitted.

- [ ] Both screenshots use the exact filenames.

- [ ] Every worksheet response is complete.

## GitHub / Lab Portal Submission

1. In the Lab Portal, open the matching Week 8 lab.
2. Complete every **[WORKSHEET]** response.
3. Upload only the required screenshots to `assets/screenshots/week-08/`.
4. Select **Submit to GitHub**.
5. Open the committed worksheet and screenshots on GitHub. Confirm they are readable and contain no secrets.

**Never submit:** `.pem` files, files from `~/.ssh/`, passwords, passphrases, private-key contents, or a Bastion shareable URL.
