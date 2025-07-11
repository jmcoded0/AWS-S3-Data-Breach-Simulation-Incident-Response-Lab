## ☁️ AWS S3 Data Breach Simulation & Incident Response Lab

> **Project Goal:** Simulate a real-world cloud data breach caused by a misconfigured public S3 bucket. Then act as the cloud security analyst to detect the attacker using AWS CloudTrail logs and respond to the incident.

----

## ✅ Phase 1: S3 Bucket Setup & Misconfiguration

### 🎯 Goal:
To simulate a common cloud misconfiguration where a developer accidentally exposes sensitive data by making an S3 object public.

----

### 🛠️ What I Did:

1. Logged into my AWS account using the browser  
2. Opened the **S3** service and created a new bucket named `jm-public-lab`  
3. During bucket creation, I unchecked:
   - `Block all public access`  
4. On my terminal, I created a fake sensitive file:

    ```bash
    echo "DB_USERNAME=admin" > secret.txt
    echo "DB_PASSWORD=SuperSecret123!" >> secret.txt
    ```

5. Uploaded `secret.txt` to the S3 bucket using the AWS Console  
6. Enabled public access by:
   - Changing Object Ownership to allow ACLs  
   - Unchecking all "Block Public Access" settings  
   - Editing the object permissions to allow "Everyone (public access)" to `Read`

----

### ✅ Result

I verified that the file became publicly accessible at:

```
https://jm-public-lab.s3.us-east-1.amazonaws.com/secret.txt
```

This means **anyone** with the link can download it — no login required.

----

### 📸 Screenshots I Captured:
- secret.txt file uploaded ![image](https://github.com/user-attachments/assets/63410375-4d99-46c5-9736-4f8a354cca85)


- `secret.txt` permission showing “Everyone: Read” ![image](https://github.com/user-attachments/assets/5c544945-60e1-4656-9443-0b5ebc61dd06)


----

## 🔓 Phase 2: Simulating External Attacker Access

### 🎯 Goal:
Simulate an attacker discovering and stealing exposed data using Kali Linux and basic tools like `curl`.

----

### 🧰 Tools Used:
- Kali Linux terminal  
- `curl` for HTTP request  

----

### ⚙️ What I Did:

1. Switched to my Kali Linux attacker machine  
2. Ran the following command:

    ```bash
    curl https://jm-public-lab.s3.us-east-1.amazonaws.com/secret.txt
    ```

3. The output returned the sensitive content:

    ```
    DB_USERNAME=admin
    DB_PASSWORD=SuperSecret123!
    ```

4. This confirmed that the file was fully exposed to the public

----

### 📸 Screenshots I Captured:
-![VirtualBox_Kali Linux_08_07_2025_02_34_10](https://github.com/user-attachments/assets/16c06d5f-7a1c-4558-a0a4-0c9ffab49ff6)

----

### ✅ Outcome:

I successfully simulated a real cloud data breach caused by insecure S3 configuration. This proves how misconfigurations can lead to credential exposure and real-world attacks.

Next, I will act as a **Cloud Security Analyst**, enable CloudTrail, and **detect** this unauthorized access.

----

### ✅ Phase 3: Simulating an S3 Data Breach via Public Bucket Exposure

In this phase, I simulated a common AWS misconfiguration: a public S3 bucket exposing sensitive data.

#### 🔹 Actions Performed:

- Created a new S3 bucket: `s3-breach-lab-bucket`
- Uploaded a dummy sensitive file: `payload.txt`
- Disabled **Block Public Access** for full public exposure
- Configured **bucket policy** and **ACLs** to allow anonymous access
- Simulated a breach by accessing the file from:
  - My **Kali Linux VM**
  - A browser on my host machine

#### 🔹 Logging Setup:

- Enabled **Server Access Logging**, forwarding logs to `jm-access-logs-bucket`
- Enabled **CloudTrail**, including:
  - **Management events** (ALL)
  - **Data events** for ALL S3 buckets (read + write)

#### ❌ What Didn’t Work:

Despite correctly configuring logging and waiting for hours, **no access logs or CloudTrail events appeared** — even after repeated anonymous file downloads.

This may be due to AWS processing delays, permission conflicts, or S3 log delivery constraints (e.g., CloudTrail not logging unauthenticated access unless from signed API requests).

---

#### 💡 Lessons Learned:

- CloudTrail is reliable for **API-level monitoring**, but not perfect for **anonymous S3 object access**
- **Server Access Logging** can be slow to deliver, especially in test accounts
- Always test logging setups with multiple access methods and verify delivery paths early

---

#### 🔐 Next Steps:

- Explore alternative detection methods (e.g., CloudFront + WAF logs)
- Integrate GuardDuty for broader visibility
- Try same breach scenario in a production-grade IAM/Org structure

## 🛡️ Phase 4: Enabling Amazon GuardDuty for Threat Detection

> **Goal:** Enable GuardDuty to monitor my AWS account for suspicious behavior such as API misuse, data exfiltration, or privilege abuse.

....

### 🎯 Objective:

Set up Amazon GuardDuty in the same region (`us-east-1`) as my S3 and CloudTrail services, and prepare the environment to detect future attacks.

---

### 🛠️ What I Did:

1. Logged into the AWS Console  
2. Opened the **GuardDuty** service  
3. Set region to **us-east-1** (matching my lab setup)  
4. Clicked **“Enable GuardDuty”**  
5. Accepted default settings:
   - ✅ CloudTrail event monitoring  
   - ✅ VPC Flow Logs  
   - ✅ DNS Logs  

---

### ✅ Result:

GuardDuty is now actively monitoring my AWS account and ready to detect malicious or unusual activity.

Since this was enabled **after initial setup**, no findings appeared immediately.

---

### 📸 Screenshots Captured:

- GuardDuty showing “Enabled” with no alerts yet  
  ![guardduty-enabled-no-alerts](https://github.com/user-attachments/assets/b9ebe9b6-6abc-47a9-a3e7-82ca2772b670)

---

### 🔍 Next Step:

Move forward to simulate a realistic AWS CLI-based attack using stolen credentials from Kali Linux to trigger real GuardDuty alerts.

---

## 🚨 Phase 4.5: GuardDuty Alert Triggered via AWS CLI Exfiltration

> **Goal:** Simulate an actual API-based data breach using AWS CLI on Kali, and confirm if GuardDuty detects it.

---

### 🧪 What I Did:

1. On Kali Linux, I installed and configured AWS CLI with stolen access keys
2. Ran the following commands:

    ```bash
    aws s3 ls s3://s3-breach-lab-bucket
    aws s3 cp s3://s3-breach-lab-bucket/secret.txt .
    ```

   - `s3:ListBucket` was blocked, but still logged
   - `s3:GetObject` successfully downloaded the sensitive file

---

### 🎯 Outcome:

After ~5 minutes, GuardDuty detected the API activity and triggered 2 findings:

- **Medium Severity:** `GetObject` API invoked from remote Kali host  
- **Low Severity:** `GetBucketOwnershipControls` invoked using root credentials

---

### 📸 Screenshots Captured:

- ✅ Terminal download from Kali using AWS CLI  
  ![kali-awscli-download]<img width="1920" height="909" alt="VirtualBox_Kali Linux_11_07_2025_00_57_44" src="https://github.com/user-attachments/assets/ab24735b-0657-43a6-90fa-4b5ab32e670c" />

- ✅ GuardDuty showing real findings:  
  ![guardduty-detection]<img width="1920" height="1010" alt="image" src="https://github.com/user-attachments/assets/975eec1f-c2db-4a34-9a82-88d342c81de5" />

---

### 🧠 Lessons Learned:

- GuardDuty is highly effective for detecting real AWS misuse patterns
- Even **blocked attempts** like `ListBucket` are logged and flagged
- Combining CloudTrail + GuardDuty gives strong incident response visibility

## 🛰️ Phase 5.2: Forwarding GuardDuty Alerts to Splunk via CloudWatch & Lambda

> **Goal:** Simulate a real cloud SOC setup by forwarding AWS GuardDuty alerts to **Splunk** using **CloudWatch Logs** and a Lambda function. This allows me to visualize real-time detection alerts in my SIEM.

### ✅ Step 1: Created a CloudWatch Log Group for GuardDuty Alerts

Since GuardDuty no longer allows direct export to CloudWatch Logs from its settings, I manually created a dedicated log group where GuardDuty alerts will be pushed through EventBridge.

1. Opened **AWS Console → CloudWatch**
2. Navigated to **Log Groups**
3. Clicked **“Create Log Group”**
4. Named it:
   ```
   /aws/guardduty/logs
   ```
5. Created the log group successfully

📸 **Screenshot – CloudWatch Log Group created**  
![cloudwatch-log-group]<img width="1920" height="1009" alt="image" src="https://github.com/user-attachments/assets/f4b77ff4-7d0e-44f1-be4d-f9af49b00d08" />


---

### ✅ Step 2: Forwarded GuardDuty Alerts Using EventBridge Rule

Instead of exporting from GuardDuty settings, I created an EventBridge rule to catch all **GuardDuty findings** and forward them into my log group.

1. Opened **EventBridge → Rules → Create Rule**
2. Named it: `forward-guardduty-to-logs`
3. Selected **Event source**:  
   - Service: `GuardDuty`  
   - Event type: `GuardDuty Finding`
4. Set the **Target** to:
   - CloudWatch Log Group: `/aws/guardduty/logs`
5. Created and activated the rule

📸 **Screenshot – EventBridge rule connected to log group**  
![eventbridge-rule]<img width="960" height="505" alt="Screenshot 2025-07-11 014207" src="https://github.com/user-attachments/assets/cbf44e41-ac0c-4e90-aa1d-e133d12c0dde" />

### ✅ Step 2: Created HEC Token in Splunk

1. Opened **Splunk Web Interface** on Kali
2. Navigated to:
   ```
   Settings → Data Inputs → HTTP Event Collector
   ```
3. Enabled HEC if it was off
4. Created a new token:
   - Name: `aws-guardduty`
   - Source type: `_json`
   - Index: `main` or `aws`
   - Kept port: `8088`

📸 **Screenshot – Splunk HEC token configuration**  
![splunk-hec-token]<img width="1920" height="909" alt="VirtualBox_Kali Linux_11_07_2025_02_10_00" src="https://github.com/user-attachments/assets/c5556803-7dec-4675-aa5f-2f614d995963" />

---

### ✅ Step 3: Created Lambda to Forward Logs

1. Went to:
   ```
   Lambda → Create Function → Use a blueprint
   ```
2. Searched for:
   ```
   splunk-cloudwatch-logs-processor
   ```
3. Named the function: `ForwardGuardDutyToSplunk`
4. Runtime: Python 3.x
5. Set the environment variables:
   - `SPLUNK_HEC_URL`: `https://<kali-ip>:8088`
   - `SPLUNK_HEC_TOKEN`: `your-hec-token`
6. Assigned execution role with these permissions:
   - `logs:GetLogEvents`
   - `logs:DescribeLogGroups`
   - `logs:DescribeLogStreams`
   - `logs:FilterLogEvents`

📸 **Screenshot – Lambda function + env variables**  
![lambda-env-config](https://github.com/user-attachments/assets/your-screenshot-link-here)

---

### ✅ Step 4: Subscribed Log Group to Lambda

1. Went to:
   ```
   CloudWatch → Log Groups → /aws/guardduty/logs
   ```
2. Clicked **“Create subscription filter”**
3. Set destination to:  
   `ForwardGuardDutyToSplunk` (Lambda)
4. Completed and saved the subscription

📸 **Screenshot – CloudWatch subscription to Lambda**  
![cloudwatch-subscription](https://github.com/user-attachments/assets/your-screenshot-link-here)

---

### ✅ Step 5: Triggered a GuardDuty Alert Again

1. Switched to Kali
2. Replayed the same simulated attack:
   ```bash
   aws s3 cp s3://s3-breach-lab-bucket/secret.txt .
   ```
3. Waited a few minutes to let GuardDuty detect it

📸 **Screenshot – Terminal command to trigger alert**  
![trigger-alert-kali](https://github.com/user-attachments/assets/your-screenshot-link-here)

---

### ✅ Step 6: Verified Alerts in Splunk

1. Opened Splunk Search
2. Searched:
   ```spl
   index=main sourcetype=_json guardduty
   ```
3. Found alert from GuardDuty forwarded as JSON log

📸 **Screenshot – GuardDuty alert in Splunk search**  
![splunk-alert-result](https://github.com/user-attachments/assets/your-screenshot-link-here)

---

### 🧠 Lessons Learned

> This phase showed how real cloud incidents can be wired into SIEM platforms using native AWS tools like CloudWatch and Lambda. It simulates enterprise-grade security monitoring.

---

### 🧪 Future Enhancement

- Parse and extract fields in Splunk using `props.conf` for cleaner dashboards  
- Add email/Slack alerting using CloudWatch Alarms  
- Automate IAM key revocation on alert using Lambda + EventBridge


