Great — let’s **carry forward your legacy** with a well-structured **Experiment 11: IAM**, keeping in mind:

> 💡 **You’re acting as the Root User** (no IAM users yet), so **you’ll create everything from scratch**, and we’ll clearly label which steps require Root vs IAM user permissions.

---

# ✅ **Experiment 11: Working with AWS IAM**

---

## 🎯 **Goal:**

Set up access control for a dev team using **IAM groups, users, policies, and roles**, and test EC2-S3 interaction using those permissions.

---

## 🛠️ Prerequisites:

* You are signed in as **Root User**
* Region: (Choose default AWS region, e.g. `us-east-1`)
* No existing IAM users or groups yet

---

## 🔐 **Task 1: Create IAM Group "WebDevTeam"**

👤 **Performed by: Root User**

### ✅ Steps:

1. Go to **IAM Console → User groups → Create group**
2. Group name: `WebDevTeam`
3. For now, **do not attach any policy** (we'll attach the custom one later)
4. Click **Create group**

---

## 📜 **Task 2: Create Custom Managed Policy “WebDevPolicy”**

👤 **Performed by: Root User**

### ✅ Policy Includes:

* EC2 full access
* S3 read/write only to a specific bucket (`dev-website-assets`)

### 🔧 Steps:

1. Go to **IAM → Policies → Create Policy**
2. Choose **JSON** tab and paste:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "FullEC2Access",
      "Effect": "Allow",
      "Action": "ec2:*",
      "Resource": "*"
    },
    {
      "Sid": "S3RWDevBucket",
      "Effect": "Allow",
      "Action": [
        "s3:GetObject",
        "s3:PutObject",
        "s3:ListBucket"
      ],
      "Resource": [
        "arn:aws:s3:::dev-website-assets",
        "arn:aws:s3:::dev-website-assets/*"
      ]
    }
  ]
}
```

3. Click **Next → Name it**: `WebDevPolicy`
4. Create the policy.

---

## 📎 **Attach Policy to the Group**

👤 **Performed by: Root User**

1. Go to **IAM → User groups → WebDevTeam → Permissions → Add permissions**
2. Choose **Attach policies**
3. Select `WebDevPolicy`
4. Click **Attach policy**

---

## 👥 **Task 3: Create IAM Users "dev1" and "dev2"**

👤 **Performed by: Root User**

### ✅ Steps:

1. Go to **IAM → Users → Add users**
2. Username: `dev1`
   Access type: ✔️ Programmatic access + ✔️ AWS Management Console
   Set custom password → uncheck "require password reset"
3. On **permissions screen**, choose: **Add to group → WebDevTeam**
4. Click **Create User**
5. Repeat the same for **`dev2`**

📌 Save the **console login URL**, usernames and passwords for testing later.

---

## 🎭 **Task 4: Create IAM Role “EC2WebServerRole”**

👤 **Performed by: Root User**

### ✅ Purpose:

* This role will be **attached to EC2 instances**
* It allows **S3 read-only access to `dev-website-assets`**

---

### 🔧 Steps:

1. Go to **IAM → Roles → Create role**

2. **Trusted entity type**: AWS service → Choose **EC2**

3. Click **Next → Permissions**

4. Attach this **inline policy** (or create managed one):

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "ReadDevS3",
      "Effect": "Allow",
      "Action": [
        "s3:GetObject",
        "s3:ListBucket"
      ],
      "Resource": [
        "arn:aws:s3:::dev-website-assets",
        "arn:aws:s3:::dev-website-assets/*"
      ]
    }
  ]
}
```

5. Name the role: `EC2WebServerRole`
6. Create the role.

---

## 🧪 **Task 5: Test Setup**

---

### 🧍 **Step A: Sign in as IAM user `dev1`**

👤 **Performed by: IAM User (dev1)**

1. Use the **AWS Console URL** saved earlier
2. Login as `dev1`
3. Navigate to EC2 Console

---

### 🚀 **Step B: Launch an EC2 Instance with the IAM Role**

👤 **Performed by: IAM User (dev1)**

1. Launch EC2 Instance (Amazon Linux 2 or Ubuntu)
2. Choose t2.micro (free tier)
3. In **"Configure Instance"**, choose:

   * IAM Role: `EC2WebServerRole`
4. Enable public IP
5. Security Group:

   * Allow SSH (port 22)
6. Launch and connect via SSH

---

### 📂 **Step C: Test S3 Access from EC2**

👤 **Performed by: IAM User (dev1) inside EC2**

1. Connect to instance via SSH:

```bash
ssh -i your-key.pem ec2-user@<public-ip>
```

2. Install AWS CLI (if not already):

```bash
sudo yum install aws-cli -y   # Amazon Linux
```

3. Test bucket access:

```bash
aws s3 ls s3://dev-website-assets
aws s3 cp s3://dev-website-assets/index.html .
```

---

### 🧠 Why This Works:

| Action                            | Reason                                                            |
| --------------------------------- | ----------------------------------------------------------------- |
| EC2 uses IAM Role                 | So it can securely access S3 **without access keys**              |
| dev1 can launch EC2               | Because `WebDevPolicy` allows `ec2:*`                             |
| EC2 instance can access S3        | Because of the role `EC2WebServerRole` and its attached S3 policy |
| Access to specific S3 bucket only | Scoped by ARN: `dev-website-assets`                               |

---

## ✅ Summary Table

| Resource           | Created By   | Description                    |
| ------------------ | ------------ | ------------------------------ |
| IAM Group          | Root         | `WebDevTeam`                   |
| Managed Policy     | Root         | Full EC2 + Limited S3          |
| IAM Users          | Root         | `dev1`, `dev2`                 |
| IAM Role           | Root         | EC2 role for accessing S3      |
| EC2 Instance       | dev1         | With IAM role attached         |
| S3 Access from EC2 | EC2 Instance | Using IAM Role → access bucket |

---
