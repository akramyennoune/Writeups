# TryHackMe: Greenholt Phish — Email Investigation Write-up

<img width="985" height="269" alt="greenph1" src="https://github.com/user-attachments/assets/39a0da0a-8dc1-42a2-822c-33875368a339" />

## Overview

* **Platform:** TryHackMe
* **Challenge:** Greenholt Phish
* **Category:** Phishing Analysis / Email Security
* **Level:** Beginner
* **Status:** Completed
* **Tools and resources:** Email header analysis, IP ownership lookup, DMARC lookup, `sha256sum`, VirusTotal

## 1. Introduction

For this challenge, I investigated a suspicious email that appeared to concern a bank transfer. The goal was to examine the email's details, trace where it came from, and investigate an attachment that could potentially be malicious.

This lab helped me understand that phishing analysis involves more than looking at the message itself. Email headers, sender information, authentication records, and attachment metadata can all provide useful evidence.

## 2. Investigating the Email

### Step 1: Examining the subject and sender

I started by reviewing the email's subject line and sender information.

The subject contained the transfer reference number `09674321`. The displayed sender name was `mr james jackson`, and the sender address was `info@MUTAWAMARINE.COM`.

I also checked the reply-to address, which was `info.mutawamarine@MAIL.COM`.

One thing I learned from this step is that the displayed sender name alone cannot establish who actually sent an email. The sender address and reply-to address provide additional information that should be investigated.

**Key observation:** The sender and reply-to addresses used different domains. This is worth examining further, although the difference alone does not prove that an email is malicious.

<img width="368" height="49" alt="green3" src="https://github.com/user-attachments/assets/c803b70e-08fc-4e3d-8857-27c1e4b7a278" />
<img width="497" height="27" alt="green5" src="https://github.com/user-attachments/assets/3b541349-3307-42f7-a3b4-d70af53a0c32" />
<img width="269" height="43" alt="green4" src="https://github.com/user-attachments/assets/6968008e-989c-4b7d-a999-3c4a39ad1fb9" />




### Step 2: Reviewing the email headers

Next, I inspected the email headers to identify the originating IP address.

The originating IP address identified in the challenge was `192.119.71.157`.

I then investigated the IP address to identify its reported owner. The result was **HostPapa**.

This helped me understand how IP ownership lookups can provide context about the infrastructure associated with an email. However, identifying a hosting provider does not, by itself, identify the person who sent the message or prove malicious activity.

<img width="611" height="80" alt="green6" src="https://github.com/user-attachments/assets/04a2e29e-3188-4fcf-9819-eb1def424316" />
<img width="847" height="312" alt="green7" src="https://github.com/user-attachments/assets/85aecfe0-6cdd-45e6-8dda-b3612d106021" />


### Step 3: Checking the DMARC record

I performed a DMARC lookup for the Return-Path domain specified in the challenge.

The record returned was:

`v=DMARC1; p=quarantine; fo=1`

Breaking it down:

* `v=DMARC1` identifies the DMARC record version.
* `p=quarantine` requests that receiving mail systems treat messages that fail DMARC authentication as suspicious, according to their policies.
* `fo=1` requests failure reports when either SPF or DKIM authentication fails to produce an aligned result, subject to the receiver's reporting configuration.

This step helped me understand how DMARC supports email authentication and how domain owners can communicate policies for handling messages that fail authentication checks.

<img width="792" height="63" alt="green9" src="https://github.com/user-attachments/assets/fec42680-d6b5-4155-b612-fb024035feaa" />
<img width="1095" height="107" alt="green8" src="https://github.com/user-attachments/assets/4ac513a0-0df4-40aa-a99f-a2c37629289f" />


## 3. Investigating the Attachment

### Step 4: Identifying the attachment

I then examined the attachment associated with the email.

The filename was:

`SWT_#09674321____PDF__.CAB`

The filename was interesting because it included `PDF` in its name but ended in `.CAB`. This reminded me that a filename can be misleading and should not be treated as reliable proof of a file's actual format.

<img width="593" height="57" alt="green10" src="https://github.com/user-attachments/assets/165cd5ca-dc43-46c1-833e-bd293839fc49" />


### Step 5: Calculating the SHA-256 hash

After downloading the attachment into my designated lab environment, I used `sha256sum` to calculate its SHA-256 hash.

Command used:

```bash
sha256sum "SWT_#09674321____PDF__.CAB"
```

The SHA-256 hash was:

`2e91c533615a9bb8929ac4bb76707b2444597ce063d84a4b33525e25074fff3f`

A hash acts as a fingerprint for a particular file's contents. It allows analysts to search threat-intelligence services for information about a file without having to rely on its filename.

<img width="784" height="111" alt="green11" src="https://github.com/user-attachments/assets/fac17060-1da4-4ec9-aa0f-896404eb3714" />


### Step 6: Checking the file with VirusTotal

I used VirusTotal to investigate the SHA-256 hash and look for existing information about the attachment.

The challenge identified the attachment's actual file type as **RAR**, despite its `.CAB` extension and the `PDF` text in its filename.

This was an important lesson: file extensions can be misleading. Verifying a file's actual format and checking its hash can reveal information that the filename alone does not.

* **SHA-256:** `2e91c533615a9bb8929ac4bb76707b2444597ce063d84a4b33525e25074fff3f`
* **Actual file type:** RAR
* **File size:** [Add the size reported by VirusTotal in KB]

<img width="343" height="145" alt="green13" src="https://github.com/user-attachments/assets/e0a0d77c-6355-4060-8c72-2a9523549ae5" />
<img width="1276" height="185" alt="green12" src="https://github.com/user-attachments/assets/daf99512-044c-4190-b160-5d4b5681619d" />


## 4. Key Findings

The investigation produced several useful observations:

1. The sender and reply-to addresses were different.
2. The originating IP address was identified as `192.119.71.157`, with HostPapa reported as its owner.
3. The DMARC record specified a quarantine policy.
4. The attachment's filename suggested a PDF, but the challenge identified its actual file type as RAR.
5. A SHA-256 hash provided a way to identify and research the attachment independently of its name.

Taken together, these observations demonstrate why suspicious emails should be investigated using multiple sources of evidence. No single detail should be treated as definitive proof of malicious activity without further context.

<img width="609" height="538" alt="green14" src="https://github.com/user-attachments/assets/c28a340d-1914-41d6-b73f-949ae870ac92" />

## 5. What I Learned

Before this lab, I understood phishing mainly as a deceptive email designed to trick a recipient. This exercise helped me see the technical side of an investigation.

I practised examining email headers, distinguishing sender information from reply-to information, understanding DMARC records, identifying reported IP ownership, calculating file hashes, and checking file metadata using VirusTotal.

My biggest takeaway was to verify what is actually present in an email or file instead of trusting the information displayed to the user.


## 6. Conclusion

The Greenholt Phish challenge gave me practical experience with the first steps of phishing analysis. It strengthened my understanding of email authentication, basic threat intelligence, and attachment investigation.

I plan to continue practising these techniques through additional security labs and documenting what I learn along the way.


## References

* TryHackMe: https://tryhackme.com/
* DMARC overview: https://dmarc.org/
* SHA-256 and checksum utilities: https://www.gnu.org/software/coreutils/manual/
* VirusTotal: https://www.virustotal.com/
