
---

# 🧪 **Experiment 3: Create and Configure Storage Services using Amazon EBS**

---

## ✅ **Why This Experiment?**

Amazon **Elastic Block Store (EBS)** provides **block-level storage volumes** for use with EC2 instances. This experiment is important because:

* It teaches how to manage and expand storage for applications
* Demonstrates **persistent storage** (data survives instance stops or restarts)
* Shows **how to move data** across instances and **across regions**
* Develops skills useful for **backup, recovery, and migration scenarios**

---

## 🔧 Pre-Requisites

* AWS account
* AWS CLI configured (optional but helpful)
* EC2 Key pair for SSH
* Ubuntu-based EC2 instances

---

# 🧩 **Task 1: Launch an EC2 Instance, Check Volume, Modify Size**

---

### 🔷 **Why?**

By default, EC2 has a root EBS volume. Expanding its size is common in production when applications need more space.

---

### 🔧 **Steps**

#### A. Launch EC2 Instance (via Console)

1. Go to **EC2 > Launch Instance**
2. Choose:

   * **AMI**: Ubuntu Server 22.04
   * **Instance Type**: `t2.micro`
   * **Key Pair**: Select/Create one
   * **Storage**: Default (8GB) → keep it
   * **Security Group**: Allow SSH (port 22)
3. Launch instance

#### B. Check Current Volume Size (SSH into EC2)

```bash
ssh -i "your-key.pem" ubuntu@<EC2_Public_IP>
```

```bash
df -h   # Show current disk space
lsblk   # Lists block devices
```

#### C. Modify the Root Volume Size

1. Go to **EC2 Dashboard > Instances**
2. Select your instance → Click **Storage > Volume ID**
3. Click **Actions > Modify Volume**
4. Increase size (e.g., from 8 GB to 12 GB)
5. Save and wait for volume to update (status = "optimizing")

#### D. Resize the File System (on instance)

```bash
# Check disk name, typically /dev/xvda1 for Ubuntu
df -h
lsblk

# Resize partition (for ext4 file system)
sudo growpart /dev/xvda 1
sudo resize2fs /dev/xvda1
```

---

✅ **Result:** You have successfully increased the root volume size.

---

# 🧩 **Task 2: Create and Attach a New EBS Volume**

---

### 🔷 **Why?**

Creating and attaching a new volume allows you to **separate data**, manage storage better, and **persist files independently** of the root volume.

---

### 🔧 **Steps**

#### A. Create New EBS Volume

1. Go to **EC2 > Volumes > Create Volume**
2. Configure:

   * Size: e.g., 1 GiB
   * Availability Zone: **Same as your EC2 instance**
   * Type: gp3 (default)
3. Click **Create Volume**

#### B. Attach Volume to Instance

1. Select the volume → Click **Actions > Attach Volume**
2. Choose your EC2 instance
3. Device name (default `/dev/sdf` or `/dev/xvdf`)

#### C. SSH into EC2 and Mount the Volume

```bash
# Check new volume device
lsblk

# Create filesystem
sudo mkfs -t ext4 /dev/xvdf

# Create a mount point
sudo mkdir /mnt/ebs

# Mount the volume
sudo mount /dev/xvdf /mnt/ebs

# Create your rollno.txt file
echo "YourRollNumber" | sudo tee /mnt/ebs/rollno.txt

# Verify
cat /mnt/ebs/rollno.txt
```

---

✅ **Result:** New volume attached and contains `rollno.txt`.

---

# 🧩 **Task 3: Detach Volume and Attach to New Instance**

---

### 🔷 **Why?**

Detaching and reattaching volumes is useful for **migrating storage**, **backing up data**, or **recovering from failures**.

---

### 🔧 **Steps**

#### A. Detach Volume from First Instance

1. Go to **EC2 > Volumes**
2. Select the volume → Click **Actions > Detach Volume**
3. Wait until state = “available”

#### B. Launch a Second EC2 Instance

* Same AMI and AZ as first one
* Allow SSH access

#### C. Attach Volume to New Instance

1. Select volume → **Attach Volume**
2. Choose the new instance
3. Device name: `/dev/sdf` or `/dev/xvdf`

#### D. SSH into New EC2 and Mount

```bash
ssh -i "your-key.pem" ubuntu@<New_Instance_IP>

# Mount the device
sudo mkdir /mnt/ebs2
sudo mount /dev/xvdf /mnt/ebs2

# Check file
cat /mnt/ebs2/rollno.txt
```

---

✅ **Result:** You’ve moved the volume and verified the data is intact.

---

# 🧩 **Task 4: Create Snapshot, Copy to Another Region, Attach to Instance**

---

### 🔷 **Why?**

Snapshots provide **backups**, allow you to **replicate data across regions**, and launch new volumes or instances for **disaster recovery**.

---

### 🔧 **Steps**

#### A. Create Snapshot of Volume

1. Go to **EC2 > Volumes**
2. Select the volume → **Actions > Create Snapshot**
3. Name: `MySnapshot` → Create

#### B. Copy Snapshot to Another Region

1. Go to **EC2 > Snapshots**
2. Select your snapshot → **Actions > Copy**
3. Choose a **different region**
4. Give name: `MySnapshotCopy` → Click **Copy Snapshot**

#### C. Switch to New Region (Top-right region selector)

1. Go to **EC2 > Snapshots**
2. Wait until copied snapshot is available

#### D. Create New Volume from Snapshot

1. Select the snapshot → **Actions > Create Volume**
2. Set size, type, and choose **AZ in the new region**
3. Click **Create Volume**

#### E. Launch New EC2 Instance in the Same AZ

* Ubuntu AMI
* Allow SSH

#### F. Attach New Volume to EC2 and Mount

```bash
# After attaching via console
ssh -i "your-key.pem" ubuntu@<New_Instance_IP>

sudo mkdir /mnt/snapvol
sudo mount /dev/xvdf /mnt/snapvol
cat /mnt/snapvol/rollno.txt
```

---

✅ **Result:** Snapshot was used to recreate the volume **in another region**, and your data (`rollno.txt`) is preserved and accessible.

---

# 📝 **Summary Table**

| Task   | Purpose                 | Key Actions                         |
| ------ | ----------------------- | ----------------------------------- |
| Task 1 | Modify root volume      | Resize root disk, update partition  |
| Task 2 | New volume & mount      | Attach, format, create file         |
| Task 3 | Detach & move volume    | Attach to new instance, check data  |
| Task 4 | Snapshot across regions | Copy, recreate, attach, verify data |

---
