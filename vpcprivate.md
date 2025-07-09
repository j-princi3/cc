
---

# ✅ **Experiment 6: Amazon VPC – NAT Gateway Configuration**

---

## 🎯 **Objective:**

Configure a NAT Gateway to enable **outbound internet access** for **EC2 in a private subnet**, while keeping it **inaccessible from the internet**.

---

## 🔧 **Task 1: Create a VPC**

### ✅ **Steps:**

* Go to **VPC → Your VPCs → Create VPC**
* Name: `NAT-VPC`
* IPv4 CIDR block: `10.1.0.0/16`
* Leave other settings default
* Create

### 🧠 **Why:**

You need a dedicated VPC for isolated networking. `10.1.0.0/16` gives you **65K+ IPs** to carve into subnets.

---

## 🔧 **Task 2: Create Two Subnets (Public + Private)**

### ✅ **Steps:**

1. Go to **VPC → Subnets → Create Subnet**
2. Choose `NAT-VPC`

🔷 **Public Subnet**

* Name: `PublicSubnet-NAT`
* AZ: `us-east-1a`
* CIDR: `10.1.1.0/24`

🔷 **Private Subnet**

* Name: `PrivateSubnet-NAT`
* AZ: `us-east-1b`
* CIDR: `10.1.2.0/24`

### 🧠 **Why:**

You need:

* **Public Subnet** for hosting the **NAT Gateway**
* **Private Subnet** for secure EC2 that should only go outbound (e.g., software updates)

---

## 🔧 **Task 3: Launch 2 EC2 Instances (One in Public, One in Private Subnet)**

### ✅ **Steps:**

🔹 **Public EC2 Instance**

* Name: `Public-EC2-NAT`
* AMI: Amazon Linux 2
* Subnet: `PublicSubnet-NAT`
* Auto-assign Public IP: **Enabled**
* Security Group: allow **SSH (port 22)** from your IP
* Key Pair: Use or create `.pem`

🔹 **Private EC2 Instance**

* Name: `Private-EC2-NAT`
* Subnet: `PrivateSubnet-NAT`
* Auto-assign Public IP: **Disabled**
* Security Group: allow **SSH from Public EC2's Security Group**
* Same key pair

### 🧠 **Why:**

* Public EC2: lets you SSH into the VPC
* Private EC2: stays hidden from the Internet but needs NAT access for outbound traffic

---

## 🔧 **Task 4: Create an Internet Gateway**

### ✅ **Steps:**

* Go to **VPC → Internet Gateways → Create IGW**
* Name: `NAT-IGW`
* Attach it to `NAT-VPC`

### 🧠 **Why:**

This gateway connects **your public subnet** to the internet — critical for NAT Gateway and the public EC2.

---

## 🔧 **Task 5: Create Route Tables**

### ✅ **Public Route Table**

1. Go to **VPC → Route Tables → Create Route Table**
2. Name: `Public-RT-NAT`
3. VPC: `NAT-VPC`
4. Add route:

   * Destination: `0.0.0.0/0`
   * Target: `NAT-IGW`
5. Associate with `PublicSubnet-NAT`

### 🧠 **Why:**

Enables the **public subnet** (where NAT Gateway lives) to connect to the internet.

---

## 🔧 **Task 6: Create a NAT Gateway**

### ✅ **Steps:**

1. Go to **EC2 → Elastic IPs → Allocate new address**

   * Name it: `EIP-NAT`
2. Go to **VPC → NAT Gateways → Create NAT Gateway**

   * Name: `MyNATGW`
   * Subnet: `PublicSubnet-NAT`
   * Elastic IP: `EIP-NAT`
3. Wait until NAT Gateway status is **Available**

### 🧠 **Why:**

This is the core of the lab — a **NAT Gateway** allows **private EC2** to go **outbound to the internet** (e.g., `yum update`, `curl`), while staying invisible from the outside.

---

## 🔧 **Task 7: Configure Route Table for Private Subnet**

### ✅ **Steps:**

1. Go to **Route Tables → Create Route Table**

   * Name: `Private-RT-NAT`
   * VPC: `NAT-VPC`
2. Edit Routes:

   * Add route:

     * Destination: `0.0.0.0/0`
     * Target: `MyNATGW`
3. Subnet Associations → Associate with `PrivateSubnet-NAT`

### 🧠 **Why:**

Without this, private EC2 instances have **no way to reach the outside**. This sends outbound traffic to the NAT Gateway.

---

## ✅ Final Test: Access Internet from Private Subnet

### ✅ **Steps:**

1. **SSH to Public EC2 from your local machine:**

```bash
ssh -i my-key.pem ec2-user@<Public-EC2-IP>
```

2. **From Public EC2, SSH into Private EC2:**

```bash
ssh -i my-key.pem ec2-user@<Private-EC2-Private-IP>
```

3. **On Private EC2, test outbound internet:**

```bash
ping google.com
curl http://example.com
sudo yum update -y
```

### 🧠 **Why:**

These confirm that **internet access from private EC2** is working via **NAT Gateway**. If ping/curl fails, double-check:

* NAT GW status
* Private route table
* Security group allows outbound HTTP/HTTPS

---

## 🧱 **Summary Diagram:**

```plaintext
           [Internet]
               │
          ┌────▼─────┐
          │  IGW     │
          └────┬─────┘
               │
       ┌───────▼────────┐
       │ Public Subnet  │
       │ (NAT Gateway)  │
       └───────┬────────┘
               │
          ┌────▼────┐
          │ NAT GW  │───────Elastic IP
          └────┬────┘
               │
      ┌────────▼─────────┐
      │ Private Subnet   │
      │ (Private EC2)    │
      └──────────────────┘
```

---

## ✅ You Now Know:

* How to build secure networks with **public and private subnets**
* How to give **private instances internet access** without public IP
* How to configure **NAT Gateway** properly

---