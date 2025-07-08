# ☁️ AWS S3 Data Breach Simulation & Incident Response Lab

> **Project Goal:** Simulate a real-world AWS S3 bucket data breach scenario, where sensitive data is publicly accessible, an attacker exploits the misconfiguration, and you (the cloud security analyst) detect, investigate, and remediate the incident using AWS CloudTrail and Splunk.

---

## 🧠 Scenario Overview

A developer mistakenly set an S3 bucket to public, exposing confidential files. An attacker discovers and downloads the data. This lab simulates that breach, then walks through how a security analyst would:
- Identify the exposed data
- Detect the unauthorized access
- Investigate via CloudTrail logs
- Analyze logs in Splunk
- Remediate the issue and prevent future exposures

---

## 🧪 Lab Architecture

| Component       | Purpose                                      |
|----------------|----------------------------------------------|
| AWS S3 Bucket   | Stores the sensitive file (misconfigured)   |
| Kali Linux VM   | Attacker machine used to access the bucket  |
| AWS CloudTrail  | Captures S3 activity logs                   |
| Splunk          | Used for log analysis and detection         |
| GitHub          | Documentation and final report              |

---

## 🔍 Phases

### ✅ Phase 1: S3 Bucket Setup & Misconfiguration
- Create a vulnerable S3 bucket
- Upload fake sensitive data
- Make the bucket publicly readable

### ✅ Phase 2: Simulated External Attack
- Access and download the exposed file from Kali
- Capture attacker evidence (IP, timestamps)

### ✅ Phase 3: Detection via CloudTrail
- Enable CloudTrail + S3 data events
- Search for `ListBucket`, `GetObject` actions

### ✅ Phase 4: Log Analysis with Splunk
- Ingest CloudTrail logs into Splunk
- Search for attacker behavior using SPL

### ✅ Phase 5: Remediation
- Remove public access from the bucket
- Apply bucket policy to block public access
- Re-test to confirm it’s secure

### ✅ Phase 6: Documentation & Lessons Learned
- Screenshots of each phase
- Markdown write-up with detection queries
- What went wrong and how to prevent it

---

## ⚙️ Tools Used

- AWS Console + CLI
- AWS S3, IAM, CloudTrail
- Kali Linux, cURL, Burp Suite
- Splunk Enterprise (local VM)
- Markdown + GitHub

---

## 📸 Screenshots (Will Be Added)

- [ ] S3 public bucket warning
- [ ] Kali Linux downloading the file
- [ ] CloudTrail log showing `GetObject`
- [ ] SPL search in Splunk
- [ ] Post-remediation ACL status

---

## 🧠 Lessons Learned
_(To be updated as project progresses)_

- Misconfigured S3 buckets are a real threat
- CloudTrail must be enabled to detect abuse
- Splunk helps correlate attacker behavior quickly
- Always validate permissions before deploying to cloud

---

## 📅 Status
> **Work in progress (started: July 8, 2025)**  
> Updates and screenshots will be added as I complete each phase.

---

## 🙌 Author
**Johnson Mathew (jmcoded)**  
Cloud & Security Enthusiast | GitHub: [@jmcoded](https://github.com/jmcoded)

---
