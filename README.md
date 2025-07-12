# ☁️ AWS S3 Data Breach Simulation & Incident Response Lab

> **Goal:** Simulate a real-world AWS cloud data breach caused by a publicly exposed S3 bucket. Then act as the cloud security analyst to detect the breach using AWS CloudTrail, GuardDuty, Lambda, SQS, and Splunk.

---

## 🧠 Scenario Overview

A developer mistakenly made an S3 bucket public, exposing a sensitive file. An external attacker (using Kali Linux) discovers the file and downloads it. This lab walks through the full breach simulation — from misconfiguration and attack to detection, log forwarding, and Splunk-based incident analysis.

You’ll see:
- How attackers can exfiltrate data from misconfigured S3
- How to detect malicious behavior using CloudTrail and GuardDuty
- How to forward AWS logs into Splunk for real-time SIEM visibility
- How to investigate, visualize, and respond to cloud incidents

---

## 🧪 Lab Architecture

| Component         | Role                                              |
|------------------|---------------------------------------------------|
| **S3 Bucket**     | Stores sensitive file (`secret.txt`, `payload.txt`) |
| **Kali Linux VM** | Simulates attacker actions via `curl` and AWS CLI |
| **AWS CloudTrail**| Captures S3 API activity and management events     |
| **GuardDuty**     | Detects suspicious AWS activity                   |
| **Lambda + SQS**  | Transports alerts from AWS to Splunk              |
| **Splunk**        | Centralizes log data for threat hunting & analysis|

---

## 🔍 Phases

### ✅ Phase 1: S3 Bucket Setup & Misconfiguration
- Created public S3 buckets
- Uploaded fake sensitive files (`secret.txt`, `payload.txt`)
- Enabled public access through ACLs and bucket policy

### ✅ Phase 2: Simulated External Attack
- Accessed and downloaded files using `curl` on Kali
- Simulated exfiltration using AWS CLI (API-based breach)

### ✅ Phase 3: Detection & Logging
- Enabled CloudTrail and S3 data event logging
- Confirmed logs showed `GetObject` and `ListBucket` events
- Enabled GuardDuty and confirmed real alerts were generated

### ✅ Phase 4: Forwarding Logs to Splunk
- Created Lambda function to push GuardDuty logs to Splunk via HEC
- Configured SQS to forward S3 events
- Verified log ingestion in Splunk (CloudTrail, GuardDuty, S3)

### ✅ Phase 5: Splunk SIEM Analysis
- Used SPL to search for `GetObject`, `ListBucket`, attacker IPs
- Built basic visualizations (top events chart)
- Confirmed attacker activity via logs in real time

### ✅ Phase 6: Documentation & Wrap-Up
- Included all terminal commands, screenshots, queries
- Wrote up lessons learned, challenges faced, and enhancement ideas

---

## ⚙️ Tools Used

- **AWS Console** (S3, CloudTrail, Lambda, SQS, IAM, GuardDuty)
- **Kali Linux** (curl, AWS CLI)
- **Splunk Enterprise** (local VM with AWS Add-on)
- **Markdown + GitHub** for full documentation

---

## 📸 Screenshots Included

- ✅ S3 misconfiguration and exposed files
- ✅ Terminal view of Kali attack
- ✅ GuardDuty findings for `GetObject` and privilege abuse
- ✅ Splunk search results + visualizations
- ✅ Lambda/SQS/HEC setup and log forwarding pipeline

---

## 🧠 Final Lessons Learned

- Misconfigured S3 buckets are a top attack vector
- CloudTrail doesn’t log unauthenticated access — API-based attacks are more detectable
- GuardDuty is effective for identifying real AWS abuse patterns
- Lambda + SQS + Splunk integration enables real-time SOC visibility
- Testing and troubleshooting permissions is a huge part of cloud security work

---

## 📅 Status
> ✅ **Project Completed:** July 11, 2025  
> Every phase is documented with screenshots, commands, queries, and explanations.

---
![Project Status](https://img.shields.io/badge/status-completed-brightgreen?style=flat-square)
![License](https://img.shields.io/badge/license-MIT-blue?style=flat-square)

# ☁️ AWS S3 Data Breach Simulation & Incident Response Lab

> **Goal:** Simulate a real-world AWS cloud data breach caused by a publicly exposed S3 bucket. Then act as the cloud security analyst to detect the breach using AWS CloudTrail, GuardDuty, Lambda, SQS, and Splunk.

🔗 **Live Project Repo:**[📄 Full Documentation](https://github.com/jmcoded0/-AWS-S3-Data-Breach-Simulation-Incident-Response-Lab/blob/main/documenting.md)

📸 **Preview Screenshot:**  
![preview](https://github.com/user-attachments/assets/4d2d280a-09ee-4287-a173-009913b32da5)

## 🙌 Author

**Johnson Mathew (jmcoded)**  
Cloud & Security Enthusiast  
GitHub: [@jmcoded](https://github.com/jmcoded)

---
