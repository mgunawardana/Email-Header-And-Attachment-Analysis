# The Planet's Prestige – Email Header And Attachment Analysis

## Objective

This project demonstrates a structured forensic investigation of a suspicious email to determine its legitimacy, identify Indicators of Compromise (IOCs), and analyze embedded attachments. The investigation follows a methodology commonly used by Security Operations Center (SOC) analysts, including email header inspection, authentication validation, MIME analysis, attachment verification, metadata extraction, and basic threat infrastructure assessment.

## Scenario

A fictional organization investigating multiple disappearances receives a suspicious email containing an attachment. The objective is to determine whether the email is malicious, identify phishing indicators, inspect the attachment, and collect evidence that may support further incident response activities.

> **Note:** This investigation is based on the **"[The Planet's Prestige](https://blueteamlabs.online/home/challenge/the-planets-prestige-e5beb8e545)"** challenge from **Blue Team Labs Online (BTLO)** and is documented using a real world SOC investigation approach rather than a challenge walkthrough.

## Investigation Summary

A suspicious email was analyzed to determine its legitimacy and identify potential Indicators of Compromise (IOCs). The investigation included inspecting email headers, validating authentication mechanisms (SPF, DKIM, and DMARC), analyzing MIME content, decoding Base64-encoded data, verifying attachment file signatures, extracting metadata, and identifying attacker infrastructure.

Multiple phishing indicators were identified throughout the investigation, including failed sender authentication, mismatched sender identities, a disguised attachment, hidden Base64-encoded content, and evidence linking the email to a fake email service.

## Skills Demonstrated

- Email Header Analysis
- Email Authentication Analysis (SPF, DKIM, DMARC)
- MIME Structure Analysis
- Base64 Decoding
- Attachment Analysis
- File Signature (Magic Number) Validation
- Metadata Analysis
- Threat Infrastructure Assessment
- Basic Threat Intelligence & OSINT
- Indicator of Compromise (IOC) Identification
- Security Investigation Documentation

## Tools Used

| Tool | Purpose |
|------|---------|
| Virtual Machine | Isolated environment for safe analysis |
| 7-Zip | Extract archived files |
| Notepad++ | Inspect raw `.eml` email contents |
| CyberChef | Decode Base64 content and analyze encoded data |
| HxD | Inspect hexadecimal file signatures |
| ExifTool | Extract file metadata |
| Gary Kessler File Signature Database | Verify file types using magic bytes |
| Google Sheets | Inspect extracted spreadsheet |

# Investigation Workflow

```text
Email (.eml)
        │
        ▼
Header Analysis
        │
        ▼
Authentication Review
        │
        ▼
MIME Structure Analysis
        │
        ▼
Base64 Decoding
        │
        ▼
Attachment Validation
        │
        ▼
Magic Number Verification
        │
        ▼
Attachment Extraction
        │
        ▼
Metadata Analysis
        │
        ▼
IOC Identification
        │
        ▼
Final Assessment
```

# Investigation Process

## Step 1 – Preparing the Analysis Environment

A dedicated virtual machine was used to safely analyze potentially malicious files without affecting the host operating system.

The provided email sample was extracted using **7-Zip**, and the raw email (`.eml`) file was opened in **Notepad++** for manual inspection.

**Figure 1 – Email sample opened inside Notepad++**
<br>

![Email sample opened inside Notepad++](https://github.com/voidace2006-netizen/The-Planet-s-Prestige-Email-Header-And-Attachment-Analysis/blob/main/Email%20sample%20opened%20inside%20Notepad%2B%2B.png)

---

## Step 2 – Email Header Analysis

The investigation began by reviewing the email headers to understand how the message travelled from sender to recipient.

The following headers were examined:

- Delivered-To
- Received
- X-Headers
- ARC (Authenticated Received Chain)
- Return-Path
- Authentication-Results
- To
- From
- Subject
- Reply-To
- Message-ID
- Date

Particular attention was given to the **Received** headers because they reveal the mail servers responsible for relaying the message.

The first **Received** header represents the mail server closest to the recipient, while the final **Received** header represents the server closest to the sender.

**Figure 2 – Email header analysis**
<br>

![Email Header Analysis](https://github.com/voidace2006-netizen/The-Planet-s-Prestige-Email-Header-And-Attachment-Analysis/blob/main/email%20header%20analysis%20trial.png)

*You can find the complete walkthrough of analysing email header on my blog site: [Link]*

---

## Step 3 – Authentication Validation

The **Authentication-Results** header was reviewed to verify the sender's authenticity.

Authentication mechanisms inspected included:

- SPF
- DKIM
- DMARC

The investigation identified an **SPF failure**, indicating that the sending server was not authorized by the sender's DNS records to send email on behalf of the claimed domain.

Failed authentication mechanisms are commonly used as indicators during phishing investigations.

**Figure 3 – Authentication Results**
<br>

![Authentication Results](https://github.com/voidace2006-netizen/The-Planet-s-Prestige-Email-Header-And-Attachment-Analysis/blob/main/Authentication%20Result.png)

---

## Step 4 – Sender Identity Validation

The sender's identity was validated by comparing several header fields.

The following fields were reviewed:

- From
- Return-Path
- Reply-To

A mismatch was identified between the sender's domain and the Reply-To domain.

| Header | Value |
|---------|-------|
| From | billjobs@microapple.com |
| Reply-To | negeja3921@pashter.com |

This technique is commonly used in phishing campaigns to redirect user replies to an attacker-controlled mailbox.

**Figure 4 – Sender and Reply-To comparison**
<br>

![Sender and Reply-To comparison](https://github.com/voidace2006-netizen/The-Planet-s-Prestige-Email-Header-And-Attachment-Analysis/blob/main/Sender%20and%20Reply-To%20comparison.png)

---

## Step 5 – MIME Structure Analysis

The MIME boundaries were inspected to understand how the email content was organized.

The first MIME section contained:

- Content-Type: `text/plain; charset=utf-8`
- Content-Transfer-Encoding: `base64`

The Base64-encoded content was copied into **CyberChef** and decoded using the **From Base64** operation.

The decoded output was saved as `email.txt` for further review.

**Figure 5 – Base64 decoded using CyberChef**
<br>

![Base64 decoded using CyberChef](https://github.com/voidace2006-netizen/The-Planet-s-Prestige-Email-Header-And-Attachment-Analysis/blob/main/Base64%20decoded%20using%20CyberChef.png)

---

## Step 6 – Attachment Validation

The second MIME section indicated that the attachment was a PDF document.

Rather than trusting the declared MIME type, the attachment was decoded using **CyberChef** before validating its true file type.

The decoded attachment was converted into hexadecimal format, and the first bytes (magic bytes) were compared against the **Gary Kessler File Signature Database**.

Although advertised as a PDF, the file signature identified the attachment as a **ZIP archive**.

This demonstrates why analysts should verify file signatures instead of relying solely on file extensions or MIME types.

**Figure 6 – Attachment signature verification**
<br>

![Attachment signature verification](https://github.com/voidace2006-netizen/The-Planet-s-Prestige-Email-Header-And-Attachment-Analysis/blob/main/Base64%20decoded%20using%20CyberChef.png)

---

## Step 7 – File Signature Analysis

The extracted ZIP archive contained three files.

Each file was inspected using **HxD** to verify its actual file type by examining its magic bytes.

| File | Actual Type |
|------|-------------|
| File 1 (DaughtersCrown)| JPEG |
| File 2 (GoodJobMajor) | PDF |
| File 3 (Money) | XLSX (Office Open XML Spreadsheet) |

Although modern Office documents share ZIP signatures internally, the file extension and internal structure confirmed the third file as a valid Excel spreadsheet.

**Figure 7 – File signature verification**
<br>

![File signature verificationjpeg](https://github.com/voidace2006-netizen/The-Planet-s-Prestige-Email-Header-And-Attachment-Analysis/blob/main/File%20signature%20verification%20jpeg.png)

![jpeg](https://github.com/voidace2006-netizen/The-Planet-s-Prestige-Email-Header-And-Attachment-Analysis/blob/main/jpeg.png)

![File signature verificationpdf](https://github.com/voidace2006-netizen/The-Planet-s-Prestige-Email-Header-And-Attachment-Analysis/blob/main/File%20signature%20verification%20pdf.png)

![pdf](https://github.com/voidace2006-netizen/The-Planet-s-Prestige-Email-Header-And-Attachment-Analysis/blob/main/pdf.png)

![File signature verificationxlsx](https://github.com/voidace2006-netizen/The-Planet-s-Prestige-Email-Header-And-Attachment-Analysis/blob/main/File%20signature%20verification%20xlsx.png)

![xlsx](https://github.com/voidace2006-netizen/The-Planet-s-Prestige-Email-Header-And-Attachment-Analysis/blob/main/xlsx.png)

---

## Step 8 – Hidden Data Discovery

The recovered spreadsheet was opened using **Google Sheets**.

Initially, no suspicious information appeared visible in the second sheet.

After selecting all cells and clearing formatting, hidden Base64-encoded text became visible within the second worksheet.

The hidden content was decoded using **CyberChef**, revealing an additional message embedded by the attacker.

**Figure 8 – Hidden Base64 content discovered**
<br>

![Hidden Base64 content discovered1](https://github.com/voidace2006-netizen/The-Planet-s-Prestige-Email-Header-And-Attachment-Analysis/blob/main/Hidden%20Base64%20content%20discovered%201.png)

![Hidden Base64 content discovered2](https://github.com/voidace2006-netizen/The-Planet-s-Prestige-Email-Header-And-Attachment-Analysis/blob/main/Hidden%20Base64%20content%20discovered%202.png)

![Hidden Base64 content discovered3](https://github.com/voidace2006-netizen/The-Planet-s-Prestige-Email-Header-And-Attachment-Analysis/blob/main/Hidden%20Base64%20content%20discovered%203.png)

---

## Step 9 – Threat Infrastructure Assessment

The email infrastructure was reviewed to identify attacker-controlled services.

Analysis of the **Received** headers identified **emkei.cz**, which is a web-based fake email service capable of generating spoofed emails.

Although additional reputation checks against the observed infrastructure did not identify known malicious indicators, the presence of a fake email service significantly strengthened the phishing assessment.

**Figure 9 – Email infrastructure review**
<br>

![Email infrastructure review](https://github.com/voidace2006-netizen/The-Planet-s-Prestige-Email-Header-And-Attachment-Analysis/blob/main/Email%20infrastructure%20review%201.png)

![Email infrastructure review2](https://github.com/voidace2006-netizen/The-Planet-s-Prestige-Email-Header-And-Attachment-Analysis/blob/main/Email%20infrastructure%20review%202.png)

---

## Step 10 – Metadata Analysis

As an additional investigative step beyond the challenge requirements, **ExifTool** was used to inspect metadata contained within the recovered files.

Metadata analysis identified the document author's name, providing another attribution clue regarding the individual responsible for creating the attachment.

This step demonstrates how metadata can provide valuable evidence during forensic investigations.

**Figure 10 – Metadata extracted using ExifTool**
<br>

![Metadata extracted using ExifTool](https://github.com/voidace2006-netizen/The-Planet-s-Prestige-Email-Header-And-Attachment-Analysis/blob/main/Metadata%20extracted%20using%20ExifTool.png)

---

# MITRE ATT&CK Mapping

| Technique | ID |
|-----------|-----|
| Phishing | T1566 |
| Spearphishing Attachment | T1566.001 |
| Obfuscated/Encoded Files | T1027 |

# Indicators of Compromise (IOCs)

| Type | Value | Notes |
|------|------|------|
| Sender Domain | microapple.com | Claimed sender |
| Reply-To Address | negeja3921@pashter.com | Different from sender |
| Email Service | emkei.cz | Fake email service |
| Authentication | SPF Failed | Unauthorized sending server |
| Attachment Type | ZIP Archive | Disguised as PDF |
| Hidden Payload | Base64 Encoded Text | Embedded within spreadsheet |
| Suspected C2 Domain | pashter.com | Potential attacker infrastructure |

# Key Findings

- SPF authentication failed, indicating the sending server was unauthorized.
- The Reply-To address differed from the sender domain.
- The email originated through a fake email service.
- The advertised PDF attachment was actually a ZIP archive.
- Hidden Base64 content was embedded inside an Excel worksheet.
- Metadata analysis revealed attribution-related information about the document creator.
- Multiple phishing indicators collectively supported classifying the email as malicious.

# Analyst Assessment

Based on the evidence collected throughout the investigation, the email was assessed as **malicious**.

Multiple independent indicators supported this assessment, including failed SPF authentication, mismatched sender identities, use of a fake email service, a disguised attachment, and hidden Base64-encoded content embedded within an Office document.

Although reputation checks against the observed infrastructure did not reveal known malicious activity, the combination of identified indicators justified treating the email as a phishing attempt and escalating it for further incident response.

# Lessons Learned

This investigation reinforced the importance of validating every component of an email rather than trusting visible information alone.

Email header analysis, authentication verification, MIME inspection, file signature validation, metadata extraction, and infrastructure assessment all contributed to building confidence in the final assessment.

The investigation also demonstrated how attackers may disguise malicious content using misleading file types, hidden payloads, and spoofed sender information, highlighting the importance of a layered analysis methodology when triaging suspicious emails.
