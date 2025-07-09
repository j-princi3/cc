Absolutely! Here's a complete **step-by-step guide** for **Experiment 4: Amazon Elastic File System (EFS)** including the **reasoning ("Why")** behind each step.

---

# 🧪 **Experiment 4: Amazon Elastic File System (EFS)**

---

## ✅ **Why This Experiment?**

Amazon EFS is a **fully managed, elastic, shared file system** that can be mounted simultaneously by multiple EC2 instances. This is useful for:

* Hosting shared application data
* Centralized logging
* Multi-server web environments

This experiment helps you understand how to:

* Create and configure EFS
* Share data across multiple EC2 instances
* Set up networking and security for shared storage access

---

# 🧩 **Task 1: Create Two Security Groups**

### 🔷 **Why?**

Security groups act like firewalls. Separate groups for EC2 and EFS help manage access cleanly and securely.

---

### 🔧 **Steps**

#### A. Create Security Group for EC2

1. Go to **VPC > Security Groups > Create Security Group**

   * **Name**: `EC2-SG`
   * **Description**: For EC2 access
   * Attach it to your **VPC**
2. Under **Inbound Rules**, add:

   * Type: SSH (port 22), Source: Your IP

#### B. Create Security Group for EFS

1. Create another security group:

   * **Name**: `EFS-SG`
   * **Description**: For EFS mount targets
2. Under **Inbound Rules**, add:

   * Type: **NFS (port 2049)**
   * Source: `EC2-SG` (select it from security group list)

✅ This allows only EC2 instances in `EC2-SG` to mount EFS.

---

# 🧩 **Task 2: Launch One EC2 Instance and Mount EFS**

### 🔷 **Why?**

You need at least one EC2 instance to connect and test the file system.

---

### 🔧 **Steps**

#### A. Launch EC2 Instance

1. Go to **EC2 > Launch Instance**

   * Name: `EC2-Instance-1`
   * AMI: Ubuntu Server 22.04
   * Instance Type: t2.micro
   * Key pair: Choose existing
   * Network settings:

     * Select **EC2-SG** under security group

2. Launch the instance and SSH into it:

```bash
ssh -i "your-key.pem" ubuntu@<EC2-IP>
```

#### B. Create an EFS File System

1. Go to **EFS > Create File System**
2. Choose:

   * Name: `MyEFS`
   * VPC: Same as your EC2
   * Enable Automatic backups (optional)
3. Continue with default options
4. For **Access Points**: skip (not required for basic setup)
5. EFS will automatically create **mount targets** in each AZ
6. Under **Network**, attach **EFS-SG**

#### C. Mount EFS on EC2 Instance

1. Install NFS client:

```bash
sudo apt update
sudo apt install nfs-common -y
```

2. Create a directory to mount EFS:

```bash
sudo mkdir /mnt/efs
```

3. Go to the **EFS Console > File Systems > MyEFS > Attach**

   * Copy the **mount command**, example:

```bash
sudo mount -t nfs4 -o nfsvers=4.1 <EFS-DNS>:/ /mnt/efs
```

Replace `<EFS-DNS>` with your actual EFS DNS (like `fs-abc123.efs.us-east-1.amazonaws.com`)

4. Verify it's mounted:

```bash
df -h
```

✅ Your EC2 is now connected to EFS.

---

# 🧩 **Task 3: Create Directory and a File with Your Roll No**

### 🔷 **Why?**

To test if the EFS is writable and persistent.

---

### 🔧 **Steps**

```bash
cd /mnt/efs
mkdir mydata
echo "YourRollNo" > mydata/rollno.txt
cat mydata/rollno.txt
```

✅ This file is saved on the shared EFS storage.

---

# 🧩 **Task 4: Launch Another EC2 and Mount the Same EFS**

### 🔷 **Why?**

EFS allows multiple EC2 instances to access the **same file system** concurrently. This simulates **multi-server environments** sharing data.

---

### 🔧 **Steps**

#### A. Launch Second EC2 Instance

* Name: `EC2-Instance-2`
* AMI: Ubuntu Server 22.04
* Security Group: **EC2-SG**
* Same VPC and subnet as previous instance

#### B. SSH and Mount EFS

```bash
ssh -i "your-key.pem" ubuntu@<New_EC2_IP>

# Install NFS client
sudo apt update
sudo apt install nfs-common -y

# Create mount point
sudo mkdir /mnt/efs

# Mount EFS
sudo mount -t nfs4 -o nfsvers=4.1 <EFS-DNS>:/ /mnt/efs
```

---

# 🧩 **Task 5: Access File Created from First EC2**

### 🔷 **Why?**

Verifying the file shows that **EFS is shared** and **data is persistent** across multiple instances.

---

### 🔧 **Steps**

```bash
ls /mnt/efs/mydata
cat /mnt/efs/mydata/rollno.txt
```

✅ You should see your roll number. The file created on EC2-1 is accessible from EC2-2.

---

# ✅ **Summary Table**

| Task   | Objective                | Reason                                   |
| ------ | ------------------------ | ---------------------------------------- |
| Task 1 | Create Security Groups   | Segregate access: EC2 (SSH), EFS (NFS)   |
| Task 2 | Launch EC2 and Mount EFS | Use EFS from EC2                         |
| Task 3 | Create File              | Validate write to shared storage         |
| Task 4 | Add Second EC2           | Show EFS supports multi-instance sharing |
| Task 5 | Access File from EC2-2   | Prove data is shared across machines     |

---

Would you like a **PDF lab manual**, **automation script**, or **Terraform setup** for this experiment?
