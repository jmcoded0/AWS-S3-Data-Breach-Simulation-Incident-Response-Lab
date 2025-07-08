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

