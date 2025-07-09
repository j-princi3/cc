## 🎯 **Objective:**

Ensure an **EC2 instance** can securely access an **S3 bucket** using **IAM roles**, not access keys.

---

## 🛠️ Common Setup in All Scenarios:

* **S3 Bucket**: Create an S3 bucket for testing (e.g., `my-test-bucket`).
* **Role Name**: `EC2-S3-Access` (or similar) with permissions like `AmazonS3ReadOnlyAccess`.
* **EC2 Instance**: Use Amazon Linux 2 AMI (has AWS CLI preinstalled).
* Run: `aws s3 ls` inside EC2 to test access.

---

## 🔐 **Scenario 1: Admin Sets Up Everything, IAM User Uses It**

### 👩‍💼 Admin:

1. **Create IAM User**: `developer1`, attach policy `AmazonEC2FullAccess`.
2. **Create IAM Role**: `EC2-S3-Access`

   * Trusted entity: **EC2**
   * Permissions: Attach **AmazonS3ReadOnlyAccess**
3. **Allow IAM users to associate this role to instances** (optional inline permission if needed).

### 👤 IAM User:

1. Login → Launch EC2 instance.
2. **Without attaching role** → `aws s3 ls` → ❌ Error (Access Denied)
3. Admin **attaches IAM Role** to the EC2 instance.
4. IAM user connects via SSH → now `aws s3 ls` → ✅ Success

✅ **Conclusion**: IAM Role must be attached by an Admin/root if user has no permission to modify instance roles.

---

## 🔐 **Scenario 2: IAM User Has Inline Policy to Modify IAM Roles**

### 👩‍💼 Root:

1. Create IAM user `developer2` with `AmazonEC2FullAccess`
2. Create role `EC2-S3-Access` with **S3 ReadOnlyAccess**
3. Create **Inline Policy** allowing user to:

   ```json
   {
     "Version": "2012-10-17",
     "Statement": [
       {
         "Effect": "Allow",
         "Action": "iam:PassRole",
         "Resource": "arn:aws:iam::<ACCOUNT-ID>:role/EC2-S3-Access"
       },
       {
         "Effect": "Allow",
         "Action": "ec2:AssociateIamInstanceProfile",
         "Resource": "*"
       }
     ]
   }
   ```

### 👤 IAM User:

1. Launch EC2 without role → `aws s3 ls` → ❌ Error
2. **Modifies EC2 to attach the role** (thanks to inline policy).
3. Connect via SSH → `aws s3 ls` → ✅ Success

✅ **Conclusion**: IAM user can manage IAM roles on instances **only if allowed with `iam:PassRole` + `ec2:*IamInstanceProfile`**.

---

## 🔐 **Scenario 3: IAM Role & Policy Attached, but Not Used Initially**

### 👩‍💼 Root:

1. Create IAM user `developer3` with `AmazonEC2FullAccess`
2. Create IAM role `EC2-S3-Access`
3. Create **Custom Policy**:

   * Allows `ec2:RunInstances`, `ec2:DescribeInstances`, etc.
   * Allows `iam:PassRole` for the S3 role
4. Attach that policy to the IAM user

### 👤 IAM User:

1. Launch EC2 instance **without attaching role**
2. Connect via SSH → `aws s3 ls` → ❌ Error
3. **Modifies EC2 instance to attach the role**
4. Try again → `aws s3 ls` → ✅ Success

✅ **Conclusion**: Even if role and permissions exist, it must be **attached to the instance** explicitly to work.

---

## ✅ Summary Table

| Scenario | Can IAM User Attach Role?   | EC2 Initial Access to S3 | After Role Attached |
| -------- | --------------------------- | ------------------------ | ------------------- |
| 1        | ❌ No (Admin must attach)    | ❌ No                     | ✅ Yes               |
| 2        | ✅ Yes (Inline policy)       | ❌ No                     | ✅ Yes               |
| 3        | ✅ Yes (via attached policy) | ❌ No                     | ✅ Yes               |

---

## 🧪 Testing Commands on EC2

```bash
# To check instance metadata (confirm IAM role is attached)
curl http://169.254.169.254/latest/meta-data/iam/info

# Test access to S3
aws s3 ls
```

------------UNDERSTANDING-----------------------------------------------
Absolutely! AWS IAM can be a bit confusing at first because there are **multiple types of identities and permissions** involved. Here's a **clear breakdown** to help you understand the **relationship between IAM Users, Roles, Policies**, and **who can do what**.

---

## 🧍‍♂️ 1. **IAM User**

An **IAM User** is a person (like you) who logs into the AWS Management Console or uses the CLI with access keys.

### 🟢 What a User Can Do:

It depends on the **permissions attached** to them via:

* **Managed Policies** (AWS-provided or custom)
* **Inline Policies** (attached directly to the user)

---

## 🧑‍💼 2. **IAM Role**

An **IAM Role** is an identity with **permissions**, but **no username/password**. It's assumed by AWS services (like EC2 or Lambda) or other users.

### 🟢 Who Uses Roles?

* **EC2** (e.g., to access S3)
* **Lambda**
* **Cross-account users**
* **Federated identities**

A role needs **`iam:PassRole`** permission to be passed to a service.

---

## 📝 3. **Policies** (How permissions are given)

### a) ✅ **AWS Managed Policies**

Predefined by AWS. Examples:

* `AmazonEC2FullAccess`
* `AmazonS3ReadOnlyAccess`

🔹 **Attach To**: Users, Groups, or Roles

---

### b) 🛠️ **Customer Managed Policies**

You create these policies yourself and reuse them across users or roles.

🔹 Best if: You want **centralized, reusable, version-controlled policies**.

---

### c) 🧷 **Inline Policies**

Policies that are directly attached to a **single IAM user, group, or role**. They’re not reusable.

🔹 Best if: You want a **one-off permission** tightly bound to a specific identity.

---

## 📋 Example: IAM User Wants EC2 to Access S3

### Scenario:

You (an IAM user) launch an EC2 instance that should access an S3 bucket.

---

### 🔄 Flow of Responsibilities:

| Step | Who              | What They Do                                                 | Required Permission                                      |
| ---- | ---------------- | ------------------------------------------------------------ | -------------------------------------------------------- |
| 1    | **Root/Admin**   | Create IAM user and give them `AmazonEC2FullAccess`          | Use AWS Managed Policy                                   |
| 2    | **Root/Admin**   | Create **IAM Role** with `AmazonS3ReadOnlyAccess`, trust EC2 | Admin only                                               |
| 3    | **IAM User**     | Launch EC2                                                   | Already allowed by `AmazonEC2FullAccess`                 |
| 4    | **IAM User**     | Attach role to EC2                                           | Needs `iam:PassRole` + `ec2:AssociateIamInstanceProfile` |
| 5    | **EC2 instance** | Access S3                                                    | Needs the role with `AmazonS3ReadOnlyAccess` attached    |

---

## ✅ Key Permissions to Understand

| Action                  | Required Permission                                |
| ----------------------- | -------------------------------------------------- |
| Launch EC2              | `ec2:RunInstances`                                 |
| Attach IAM Role to EC2  | `iam:PassRole` + `ec2:AssociateIamInstanceProfile` |
| Replace IAM role on EC2 | `ec2:ReplaceIamInstanceProfileAssociation`         |
| Create Role             | `iam:CreateRole` (Admin usually)                   |
| Access S3 from EC2      | EC2 must have a role with `s3:GetObject`           |

---

## 🎓 Summary of Terms

| Term                                  | Description                                                    |
| ------------------------------------- | -------------------------------------------------------------- |
| **IAM User**                          | Human identity who logs in to AWS                              |
| **IAM Role**                          | Permission container assumed by EC2, Lambda, or other services |
| **Managed Policy (AWS)**              | Ready-to-use permission sets created by AWS                    |
| **Customer Managed Policy**           | Reusable custom permissions created by you                     |
| **Inline Policy**                     | One-off policy attached directly to one user or role           |
| **`iam:PassRole`**                    | Grants the ability to assign a role to an AWS service          |
| **`ec2:AssociateIamInstanceProfile`** | Lets user attach IAM roles to EC2 instances                    |

---

## 👀 Visual Relationship

```plaintext
[IAM User] ← attached policy → [Permissions]
    |
    └── (creates) EC2 instance
           |
           └── (assigned) IAM Role → S3 Access
```

---

Let me know if you'd like a **diagram**, a **policy JSON**, or a **quiz-style review** to reinforce it!
