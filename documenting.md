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
- secret.txt file uploaded  
- `secret.txt` permission showing “Everyone: Read”

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
- Terminal output showing stolen credentials

----

### ✅ Outcome:

I successfully simulated a real cloud data breach caused by insecure S3 configuration. This proves how misconfigurations can lead to credential exposure and real-world attacks.

Next, I will act as a **Cloud Security Analyst**, enable CloudTrail, and **detect** this unauthorized access.

----

## 🛡️ Phase 3: CloudTrail Detection & Log Analysis

### 🎯 Goal:
Detect and investigate unauthorized access to a public S3 object using AWS CloudTrail logs.

----

### 🛠️ What I Did:

1. Navigated to **CloudTrail** in the AWS Console  
2. Clicked **Create trail** and used:
   - Trail name: `s3-breach-detection-trail`  
   - New S3 bucket for logs: `jm-cloudtrail-logs`  
   - Log file encryption: left **disabled**  
3. In **Event types**:
   - ✅ Enabled **Management events**  
   - ✅ Enabled **Data events** → selected **S3** → **All current & future buckets**  
4. Left **Insights** and **CloudWatch Logs** disabled  
5. Clicked **Create trail** and waited 2–3 minutes  

----

### 🔄 Simulated Another Attack

To trigger CloudTrail logging:

Switched to my Kali Linux machine and ran:

```bash
curl https://jm-public-lab.s3.us-east-1.amazonaws.com/secret.txt
```

This simulated an attacker re-downloading the exposed file after public exposure.

----

### 🔍 Searched CloudTrail Logs

1. Returned to **CloudTrail > Event history**  
2. Filtered by:
   - **Event source** = `s3.amazonaws.com`  
   - **Event name** = `GetObject`  
3. Found a log showing:
   - **EventTime**: Timestamp of the attack  
   - **Source IP address**: My Kali machine’s IP  
   - **Request parameters**: Showed `secret.txt` was accessed  
   - **UserAgent**: curl/7.x — typical attacker signature  

----

### 📸 Screenshots I Captured:
- CloudTrail **Event history** page showing `GetObject` event  
- Full expanded log details with:
  - sourceIPAddress  
  - requestParameters → `secret.txt`  
  - userAgent = curl  

----

### ✅ Outcome:

CloudTrail successfully captured the unauthorized access to my public S3 object.

This confirmed that:
- S3 Data Events were being logged correctly  
- I could trace attacker actions using native AWS logging tools

Next, I’ll explore how to **secure the bucket**, remove public access, and apply proper IAM policies.

----
