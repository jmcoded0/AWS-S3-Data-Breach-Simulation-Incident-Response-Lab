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
   - `SPLUNK_HEC_URL`: `https://192.168.117.4:8088`
   - `SPLUNK_HEC_TOKEN`: `f05776b0-3e66-436d-955c-237fa63ae445`
6. Assigned execution role with these permissions:
   - `logs:GetLogEvents`
   - `logs:DescribeLogGroups`
   - `logs:DescribeLogStreams`
   - `logs:FilterLogEvents`

📸 **Screenshot – Lambda function**  
![lambda-env-config]<img width="1920" height="1009" alt="image" src="https://github.com/user-attachments/assets/e5f0923d-312c-418c-b9e6-e5e1616a5dd4" />
 + env variables <img width="960" height="505" alt="Screenshot 2025-07-11 022835" src="https://github.com/user-attachments/assets/7c4a6ca6-6473-44fa-9ebc-0e53c50d3ea9" />

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
![cloudwatch-subscription]<img width="1920" height="1010" alt="image" src="https://github.com/user-attachments/assets/0818c6d7-a4b4-4d8f-acba-7d6f03076497" />


---

### ✅ Step 5: Triggered GuardDuty Alert Again from Kali

To confirm everything was wired properly, I triggered the attack again from Kali:

```
aws s3 cp s3://s3-breach-lab-bucket/secret.txt .
```

📸 **Screenshot – Kali terminal showing the `aws s3 cp` command**  
![trigger-alert-kali]<img width="1920" height="909" alt="VirtualBox_Kali Linux_12_07_2025_04_12_44" src="https://github.com/user-attachments/assets/38cb3879-903b-4d0e-bc72-1cb685bfc014" />


---

### ✅ Step 6: GuardDuty Triggered Alerts Again

1. Went to **AWS Console → GuardDuty**
2. After a few minutes, fresh findings appeared:
   - ✅ API call from unusual IP (Kali)
   - ✅ GetObject flagged as suspicious activity

📸 **Screenshot – GuardDuty showing new findings**  
![guardduty-alerts]<img width="1920" height="1010" alt="image" src="https://github.com/user-attachments/assets/5e077fff-447b-4316-87bb-3f1337ec33df" />

### ✅ Step 7: Created SQS Queue to Bridge S3 and Splunk

To forward S3 event notifications (like object access/download) to Splunk, I created an SQS queue:

1. Opened:

    ```
    AWS Console → SQS
    ```

2. Clicked **"Create queue"**
3. Selected:
   - **Queue type:** Standard  
   - **Queue name:** `cloudtrail-to-splunk`
4. Kept default settings, created queue successfully

📸 **Screenshot – SQS Queue created**  
![sqs-created]<img width="1920" height="1010" alt="image" src="https://github.com/user-attachments/assets/a2f8a271-536f-479b-be49-6ff2998ae012" />


---

### ✅ Step 8: Configured S3 Event Notification to Send to SQS

I set up the breached S3 bucket to notify the SQS queue every time the `payload.txt` file was accessed or downloaded.

1. Went to:

    ```
    S3 → s3-breach-lab-bucket → Properties
    ```

2. Under **Event notifications**, clicked **Create event notification**
3. Gave it a name: `ForwardToSQS`
4. Selected:
   - **Event type:** All object read events  
   - **Prefix filter:** `payload.txt`  
   - **Destination:** `SQS`  
   - **Queue:** `S3EventQueue`
5. Saved the configuration

📸 **Screenshot – S3 Event Notification to SQS**  
![s3-event-notification]<img width="1920" height="1010" alt="image" src="https://github.com/user-attachments/assets/85821aa4-b107-4beb-8244-58a795a680cf" />


---

### ✅ Step 9: Attached IAM Permissions to S3 & Lambda

To allow S3 to send events to SQS and allow Lambda to poll them:

1. Created a new IAM role: `S3ToSQSForwarder`
2. Attached the following policy:

    ````json
    {
      "Version": "2012-10-17",
      "Statement": [
        {
          "Effect": "Allow",
          "Action": [
            "sqs:SendMessage",
            "sqs:GetQueueAttributes",
            "sqs:GetQueueUrl"
          ],
          "Resource": "arn:aws:logs:us-east-1:838595597848:log-group:/aws/lambda/ForwardGuardDutyToSplunk"
        }
      ]
    }
    ````

3. Also ensured Lambda had permissions for:
   - `sqs:ReceiveMessage`
   - `sqs:DeleteMessage`

📸 **Screenshot – IAM Role attached with correct policy**  
![iam-policy-attach]<img width="1920" height="1010" alt="image" src="https://github.com/user-attachments/assets/8a34ce42-f45c-439f-af4e-9851033fd6f8" />


---

### ✅ Step 10: Verified SQS Delivery via CloudWatch Logs

To check if the SQS queue was receiving messages from S3:

1. Opened:

    ```
    CloudWatch → Log Groups
    ```

2. Monitored the Lambda log group for new event delivery logs
3. Saw logs showing payload received from SQS (base64-encoded JSON)

📸 **Screenshot – CloudWatch logs showing SQS activity**  
![cloudwatch-sqs-log]<img width="1920" height="1010" alt="image" src="https://github.com/user-attachments/assets/96f29a5d-7114-4929-967b-4ec56ef9c0b9" />


---
### ✅ Step 11: Installed & Configured AWS Add-on for Splunk

To receive logs from GuardDuty and S3 activity, I used the **Splunk Add-on for AWS**.

1. On **Splunk Web Interface (Kali)**:
    ```
    Apps → Manage Apps → Install app from file
    ```
2. Uploaded:
    ```
    splunk-add-on-for-amazon-web-services_####.tgz
    ```
3. Installed and restarted Splunk when prompted

📸 **Screenshot – AWS Add-on Installed on Splunk**  
![splunk-aws-addon-installed]<img width="1920" height="909" alt="VirtualBox_Kali Linux_12_07_2025_04_57_37" src="https://github.com/user-attachments/assets/f8e039d2-466a-47c7-b9bd-7b734f3afc2f" />


---

### ✅ Step 12: Configured AWS Account in Splunk Web

Before creating any inputs, I added my AWS account credentials in Splunk:

1. Went to:

    ```
    Apps → Splunk Add-on for AWS → Configuration → Add AWS Account
    ```

2. Entered:
   - **Name:** `aws-breach-lab`
   - **Key ID & Secret:** IAM credentials with SQS & CloudTrail read access
   - **Account Type:** Keys only

📸 **Screenshot – AWS Account Configured**  
![splunk-aws-account]<img width="1920" height="909" alt="image" src="https://github.com/user-attachments/assets/02c97de8-ac31-4734-8a19-c33b216153b9" />

---

### ✅ Step 13: Manually Edited `inputs.conf` (Optional)

To ensure SQS-based S3 input was picked up correctly, I also edited `inputs.conf` directly:

```bash
sudo nano /opt/splunk/etc/apps/Splunk_TA_aws/local/inputs.conf
```

**Added the config:**

```ini
[aws_sqs_based_s3://aws-guardduty-sqs]
aws_account = aws-breach-lab
sqs_queue = cloudtrail-to-splunk
sourcetype = _json
index = main
```

📸 **Screenshot – Edited inputs.conf File**  
![inputs-conf]<img width="1920" height="909" alt="VirtualBox_Kali Linux_12_07_2025_05_01_26" src="https://github.com/user-attachments/assets/5820f1a9-d87a-40c7-bc04-6d07676bea34" />

---

### ✅ Step 14: Restarted Splunk to Apply Input Settings

```bash
sudo /opt/splunk/bin/splunk restart
```

📸 **Screenshot – Splunk Restart from Terminal**  
![splunk-restart]<img width="1920" height="909" alt="VirtualBox_Kali Linux_12_07_2025_05_02_37" src="https://github.com/user-attachments/assets/e8cc021d-bce0-45ed-88d5-127680ca8621" />

---

### ✅ Step 15: Verified AWS Alert in Splunk Search

1. Opened **Splunk Search**
2. Ran this query:

```spl
index=cloudtrail sourcetype=aws:cloudtrail "GetObject"

```

3. ✅ Verified logs from:
   - GuardDuty alert
   - S3 object access from Kali
   - Lambda forwarding via SQS

📸 **Screenshot – GuardDuty Alert Successfully Logged in Splunk**  
![splunk-final-alert]<img width="1920" height="909" alt="VirtualBox_Kali Linux_12_07_2025_05_17_13" src="https://github.com/user-attachments/assets/2fa6227e-d8a0-4278-8cbe-c30ccc321bec" />

---
---

### 📊 Bonus Step: Visualizing AWS Events in Splunk

To better understand what types of AWS API actions were happening in the account, I used Splunk to generate a chart of the most frequent event names.

#### ✅ Search Query Used:

```spl
index=cloudtrail sourcetype=aws:cloudtrail
| top eventName
```

#### 📈 Result:

This search produced a clean bar chart showing the top API actions (e.g., `GetObject`, `ListBucket`, etc.) captured by CloudTrail. It confirmed that the `GetObject` action used in the attack was logged and visible.

📸 **Screenshot – Splunk Visualization of Top AWS API Events**  
![splunk-top-eventname]<img width="1920" height="909" alt="VirtualBox_Kali Linux_12_07_2025_05_53_04" src="https://github.com/user-attachments/assets/4d2d280a-09ee-4287-a173-009913b32da5" />


---

### 🧠 Final Lessons Learned

- ✅ AWS GuardDuty + S3 alerts can be streamed to Splunk in near real-time
- ✅ Lambda and SQS act as middleware to deliver event data
- ✅ Manual and GUI-based configuration helped troubleshoot ingestion issues
- ✅ Learned how to simulate real AWS security incidents and verify detection in SIEM

---
---

### 🧱 Challenges Faced

- 💤 **CloudTrail log delay**: After triggering S3 events or API activity, logs didn’t appear immediately. Took several minutes to show up in Splunk.
- 🔍 **No logging for anonymous S3 access**: Realized that public (unauthenticated) downloads aren't tracked in CloudTrail. Had to simulate API-based access instead.
- ⚙️ **Lambda + SQS config was tricky**: Took multiple retries to get the IAM roles and policies right before logs would flow.
- 📦 **Splunk input debugging**: Even after setting everything up, I had to manually edit `inputs.conf` and restart Splunk to get the logs flowing.
- 🧪 **Testing was repetitive**: Triggering multiple alerts and verifying them in GuardDuty/Splunk required patience and double-checking.

---

### 🎯 Final Thoughts

This lab pushed me deep into **real-world cloud security operations** — from misconfigurations and simulated data breaches to configuring detection pipelines using **CloudTrail**, **GuardDuty**, **Lambda**, **SQS**, and **Splunk**.

It wasn’t easy. I had to troubleshoot permissions, wait for logs, and manually wire up integrations — but that’s exactly how it works in the field.

🛡️ **This wasn’t just a project. It was hands-on SOC experience.**

I now understand:

- How cloud misconfigurations can lead to data breaches  
- How to simulate realistic cloud attacks from an attacker’s perspective  
- How to configure detection and response tools across AWS + Splunk  
- And how to turn logs into **real, actionable security insights**

---

> ✅ **Project Completed:** _AWS S3 Data Breach Simulation & Cloud Incident Response Lab_

🔗 All screenshots, attack simulations, Splunk queries, and configurations are included.




