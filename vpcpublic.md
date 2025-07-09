---

# ✅ **Experiment 5: Amazon VPC – Step-by-Step with Explanations**

---

## **🎯 Objective:**

Build a secure, isolated network in AWS using a custom VPC, public/private subnets, route tables, Internet Gateway, and NAT Gateway. Access a private EC2 only via a public EC2 (jump host/bastion).

---

### 🔧 **Task 1: Creation of VPC**

#### ✅ **Step:**

* Go to **VPC → Your VPCs → Create VPC**
* Name: `MyVPC`
* IPv4 CIDR block: `10.0.0.0/16`
* Tenancy: default
* Create

#### 🧠 **Why:**

A VPC defines your **own isolated virtual network**. The `10.0.0.0/16` CIDR block gives you **65,536 private IPs** to allocate to subnets.

---

### 🔧 **Task 2: Create Two Subnets (Public and Private)**

#### ✅ **Steps:**

1. Go to **VPC → Subnets → Create subnet**
2. Choose `MyVPC`

🔷 **Public Subnet**

* Name: `PublicSubnet`
* AZ: `us-east-1a`
* CIDR: `10.0.1.0/24`

🔷 **Private Subnet**

* Name: `PrivateSubnet`
* AZ: `us-east-1b`
* CIDR: `10.0.2.0/24`

#### 🧠 **Why:**

Subnets split your VPC into logical groups:

* **Public** subnet can access the Internet.
* **Private** subnet is isolated from direct internet access — ideal for databases, backend servers, etc.

---

### 🔧 **Task 3: Launch Two EC2 Instances (Public + Private)**

#### ✅ **Steps:**

🔷 **Public EC2:**

* Launch instance
* Name: `Public-EC2`
* AMI: Amazon Linux 2
* Subnet: `PublicSubnet`
* **Enable Auto-assign Public IP**
* Security group: allow SSH (port 22) from **your IP**
* Key pair: select or create `.pem`

🔷 **Private EC2:**

* Launch another instance
* Name: `Private-EC2`
* Subnet: `PrivateSubnet`
* **Do NOT assign public IP**
* Security group: allow SSH **from Public-EC2's security group**

#### 🧠 **Why:**

* Public-EC2 acts as a **jump host** (bastion) to reach the private one.
* Private-EC2 is isolated and can’t be accessed from the internet directly.

---

### 🔧 **Task 4: Create Internet Gateway (IGW)**

#### ✅ **Steps:**

* Go to **VPC → Internet Gateways → Create**
* Name: `MyIGW`
* Attach it to `MyVPC`

#### 🧠 **Why:**

An Internet Gateway allows **public subnets** in your VPC to **send/receive traffic to/from the internet**.

---

### 🔧 **Task 5: Create Route Tables and Associate with IGW / NAT**

#### ✅ **Public Route Table**

1. Go to **VPC → Route Tables → Create Route Table**
2. Name: `PublicRT`
3. VPC: `MyVPC`
4. Create
5. Under **Routes → Edit routes**

   * Destination: `0.0.0.0/0`
   * Target: `MyIGW`
6. Under **Subnet Associations → Edit**

   * Associate with `PublicSubnet`

#### 🧠 **Why:**

Tells the public subnet how to reach the internet via the Internet Gateway.

---

#### ✅ **Private Route Table + NAT Setup (Optional for outbound internet)**

**If internet access is needed from the Private EC2:**

1. Allocate an **Elastic IP**
2. Go to **VPC → NAT Gateways → Create**

   * Subnet: `PublicSubnet`
   * Elastic IP: one you just allocated
   * Name: `MyNATGW`
3. Create **Private Route Table**:

   * Name: `PrivateRT`
   * Add route: `0.0.0.0/0` → target: `MyNATGW`
   * Associate with `PrivateSubnet`

#### 🧠 **Why:**

A **NAT Gateway** lets private instances access the internet **without exposing them publicly**.

---

### 🔧 **Task 6: Access Private EC2 via Public EC2**

#### ✅ **Steps:**

🔷 **A. SSH to Public EC2 from your local system:**

```bash
ssh -i my-key.pem ec2-user@<PublicEC2-Public-IP>
```

🔷 **B. Copy your PEM key to the public EC2:**

```bash
scp -i my-key.pem my-key.pem ec2-user@<PublicEC2-Public-IP>:/home/ec2-user/
```

On Public EC2:

```bash
chmod 400 my-key.pem
```

🔷 **C. Find the Private IP of `Private-EC2`**

* Go to **EC2 Dashboard → Private-EC2 → Networking tab**

🔷 **D. From Public EC2, SSH to Private EC2:**

```bash
ssh -i my-key.pem ec2-user@<Private-EC2-Private-IP>
```

✅ You are now inside the **private instance**, safely accessed through a **secure bastion**.

---

## ✅ **Validation (Optional):**

From Private EC2:

```bash
ping google.com
```

If this works, it means:

* NAT Gateway is working (if configured)
* Routing is set correctly
* Internet access is secure and indirect

---

## 🧠 **Overall Architecture Diagram (Logical)**

```plaintext
           [Internet]
               │
         ┌─────▼──────┐
         │  IGW       │
         └────┬───────┘
              │
     ┌────────▼────────┐
     │  Public Subnet  │
     │  (10.0.1.0/24)  │
     └────────┬────────┘
              │
         ┌────▼────┐
         │ NAT GW  │─────── Elastic IP
         └────┬────┘
              │
     ┌────────▼────────┐
     │ Private Subnet  │
     │  (10.0.2.0/24)  │
     └────────────────┘
```

---

## ✅ Final Recap Table

| Task | Description    | Reason                                     |
| ---- | -------------- | ------------------------------------------ |
| 1    | Create VPC     | Isolated private network                   |
| 2    | Create Subnets | Separate public and private workloads      |
| 3    | Launch EC2s    | One public, one private instance           |
| 4    | Create IGW     | Internet access for public subnet          |
| 5    | Route Tables   | Enable proper traffic flow                 |
| 6    | Bastion Access | Securely access private EC2 via public EC2 |

---