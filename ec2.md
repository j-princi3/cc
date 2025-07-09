---

# 🧪 Experiment 1: Working with EC2 Instance

---

## ✅ **Why This Experiment?**

Amazon EC2 (Elastic Compute Cloud) allows users to launch and manage virtual servers in the AWS Cloud. This experiment is essential because:

* It teaches **how to provision cloud-based infrastructure**
* Provides experience with **Linux and Windows server environments**
* Offers insight into **remote connections using SSH and RDP**
* Shows how to **host web applications** using tools like NGINX
* Prepares students for **real-world DevOps and cloud deployment** scenarios

---

# 🧩 **Task 1: Launch an EC2 Instance Using Ubuntu OS & Connect via SSH (EC2 Direct Connect)**

> ⚠️ Note: **"EC2 Direct Connect"** might be a misunderstanding. Typically, "Direct Connect" is a dedicated private network service (used for high-speed private access to AWS). For regular SSH access, you connect via the internet using public IP (this is commonly referred to as **EC2 Instance Connect** or SSH). We'll focus on **SSH access**, which is standard for working with EC2.

---

### 🔷 **A. Launch EC2 Instance (Ubuntu) via AWS Console**

1. Sign in to the [AWS Console](https://console.aws.amazon.com/)
2. Navigate to **EC2 > Instances > Launch Instance**
3. Configure:

   * **Name**: `Ubuntu-Instance`
   * **AMI**: Choose **Ubuntu Server 22.04 LTS**
   * **Instance Type**: `t2.micro` (free tier)
   * **Key Pair**: Select existing or create new `.pem` key
   * **Network Settings**:

     * Add security group rule:

       * Type: SSH
       * Port: 22
       * Source: My IP (recommended) or Anywhere (0.0.0.0/0)
4. Click **Launch Instance**
5. Wait until the instance is in **Running** state

---

### 🔷 **B. Connect via SSH**

1. Locate the **Public IPv4 address** of the instance
2. Open terminal and run:

```bash
chmod 400 your-key.pem
ssh -i "your-key.pem" ubuntu@<PublicIP>
```

Replace `<PublicIP>` with the actual public IP of your EC2 instance.

---

### ✅ Outcome:

You are now connected to your Ubuntu EC2 instance via SSH.

---

# 🧩 **Task 2: Install NGINX Server on Ubuntu EC2 & Access via Public IP**

---

### 🔷 **A. Install NGINX on EC2**

Run these commands in the SSH session:

```bash
sudo apt update
sudo apt install nginx -y
```

Start and enable NGINX:

```bash
sudo systemctl start nginx
sudo systemctl enable nginx
```

---

### 🔷 **B. Allow HTTP (Port 80) in Security Group**

1. Go to **EC2 Dashboard > Instances**
2. Select your instance > Click **Security → Security Groups**
3. Under **Inbound rules**, click **Edit inbound rules**
4. Add a rule:

   * Type: **HTTP**
   * Port: **80**
   * Source: **Anywhere (0.0.0.0/0)**

---

### 🔷 **C. Access NGINX Web Page**

* Open a browser and visit:
  `http://<PublicIP>`

You should see the **default NGINX welcome page**.

---

### ✅ Outcome:

NGINX is installed and publicly accessible from your EC2 instance.

---

# 🧩 **Task 3: Launch an EC2 Instance Using Windows OS & Connect via RDP**

---

### 🔷 **A. Launch Windows EC2 Instance (Console)**

1. Go to **EC2 > Launch Instance**
2. Configuration:

   * **Name**: `Windows-Instance`
   * **AMI**: Select **Microsoft Windows Server 2022 Base**
   * **Instance Type**: `t2.micro`
   * **Key Pair**: Use existing or create new
   * **Security Group**:

     * Add RDP rule:

       * Type: RDP
       * Port: 3389
       * Source: My IP (recommended)
3. Launch the instance

---

### 🔷 **B. Connect via RDP**

1. Wait until the instance is in **Running** state
2. Select instance > Click **Connect**
3. Under **RDP Client**:

   * Click **Download Remote Desktop File**
   * Click **Get Password**

     * Upload your `.pem` file to decrypt password
4. Open the `.rdp` file
5. When prompted:

   * **Username**: `Administrator`
   * **Password**: (copy from step above)
6. Accept any warnings and connect

---

### ✅ Outcome:

You're connected to the Windows Server instance using Remote Desktop.

---

# 📝 Summary of Key Steps

| Task   | Description                            | Tool                    |
| ------ | -------------------------------------- | ----------------------- |
| Task 1 | Launch Ubuntu EC2 and connect via SSH  | AWS Console / CLI + SSH |
| Task 2 | Install NGINX and access from browser  | Ubuntu + NGINX          |
| Task 3 | Launch Windows EC2 and connect via RDP | AWS Console + RDP       |

---

## 📌 Optional AWS CLI for Automation

You can script these steps using AWS CLI. Here's a sample to launch a Ubuntu instance via CLI:

```bash
# 1. Create Security Group
aws ec2 create-security-group --group-name MyEC2SG --description "Allow SSH and HTTP"

# 2. Authorize Inbound Rules
aws ec2 authorize-security-group-ingress --group-name MyEC2SG \
  --protocol tcp --port 22 --cidr 0.0.0.0/0

aws ec2 authorize-security-group-ingress --group-name MyEC2SG \
  --protocol tcp --port 80 --cidr 0.0.0.0/0

# 3. Get latest Ubuntu AMI
UBUNTU_AMI=$(aws ec2 describe-images --owners 099720109477 \
  --filters "Name=name,Values=ubuntu/images/hvm-ssd/ubuntu-jammy-22.04-amd64-server-*" \
  --query 'Images | sort_by(@, &CreationDate)[-1].ImageId' --output text)

# 4. Launch instance
aws ec2 run-instances --image-id $UBUNTU_AMI --instance-type t2.micro \
  --key-name YourKeyPairName --security-groups MyEC2SG \
  --tag-specifications 'ResourceType=instance,Tags=[{Key=Name,Value=Ubuntu-Instance}]'
```

---
