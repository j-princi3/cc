
---

# ✅ **Exp 12: Working with SQS, SNS and CloudFront**

This experiment is split into **two parts**:

* **A)** Implementing asynchronous order processing using **SNS + SQS**
* **B)** Optimizing static content delivery using **S3 + CloudFront**

We’ll walk through **why** each step is important and provide **step-by-step guidance** using the **AWS Console** (CLI steps available upon request).

---

## 📦 **Part A: Order Processing with SNS + SQS**

---

### 🧠 **Why this setup?**

In a microservices-based system:

* SNS (Simple Notification Service) **broadcasts events**
* SQS (Simple Queue Service) **decouples components**, ensuring **reliable** and **independent processing**
* Using **SNS + multiple SQS queues**, multiple services (Payment, Inventory) can process the **same message** without interfering with each other

---

### 📌 Goal:

* SNS Topic: `order-events`
* SQS Queues: `payment-queue`, `inventory-queue`
* When an order is published to SNS, **both queues receive a copy**

---

### 🔧 Step-by-Step (Console-Based)

---

#### ✅ Step 1: Create SNS Topic

1. Go to **Amazon SNS → Topics → Create Topic**
2. Choose: **Standard**
3. Name: `order-events`
4. Leave defaults, click **Create Topic**

---

#### ✅ Step 2: Create SQS Queues

> Both queues will subscribe to the SNS topic

1. Go to **Amazon SQS → Create Queue**
2. Queue Name: `payment-queue`
3. Type: **Standard**
4. Leave defaults → Create queue
5. Repeat for: `inventory-queue`

---

#### ✅ Step 3: Subscribe SQS Queues to SNS Topic

You need to subscribe each queue to the topic.

1. Go to **SNS → Topics → order-events → Create subscription**

   * **Protocol**: Amazon SQS
   * **Endpoint**: ARN of `payment-queue`
2. Click **Create subscription**

🔁 Repeat for: `inventory-queue`

📌 Note: You’ll need to **allow SNS to send messages** to the queues.

* Go to **SQS → payment-queue → Access Policy → Edit**
* Add this to allow SNS to send:

```json
{
  "Effect": "Allow",
  "Principal": "*",
  "Action": "sqs:SendMessage",
  "Resource": "arn:aws:sqs:region:account-id:payment-queue",
  "Condition": {
    "ArnEquals": {
      "aws:SourceArn": "arn:aws:sns:region:account-id:order-events"
    }
  }
}
```

(Repeat for `inventory-queue`, updating names accordingly)

---

#### ✅ Step 4: Publish Order Message to SNS

1. Go to SNS → Topics → `order-events` → Publish message
2. Message Body (JSON):

```json
{
  "order_id": "ORD1234",
  "customer_id": "CUST5678",
  "amount": 499.99
}
```

3. Click **Publish**

---

#### ✅ Step 5: Validate Delivery to Queues

1. Go to SQS → `payment-queue` → Send and receive messages → **Poll for messages**
2. You should see the order message.
3. Repeat for `inventory-queue`.

---

### ✅ Result:

* **SNS** publishes once
* **Both queues** receive the message
* Microservices can independently consume and process

---

## 🌍 **Part B: Global Static Website with S3 + CloudFront**

---

### 🧠 **Why CloudFront?**

* S3 is great for static files, but it is **region-specific**
* Users far from the bucket experience **high latency**
* **CloudFront CDN** caches content **closer to the user**, dramatically improving speed

---

### 📌 Goal:

* Bucket: `blog-static-files`
* Host HTML/CSS/JS files
* Use CloudFront with `index.html` as root

---

### 🔧 Step-by-Step

---

#### ✅ Step 1: Create S3 Bucket for Static Site

1. Go to **S3 → Create Bucket**
2. Name: `blog-static-files`
3. **Uncheck "Block all public access"**
4. Create bucket

---

#### ✅ Step 2: Upload Static Files

1. Go into the bucket
2. Click **Upload**
3. Upload: `index.html`, CSS, JS, images
4. For each file:

   * Set permission → Make **public** (or do this via **bucket policy**)

📜 Example bucket policy (optional):

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "PublicRead",
      "Effect": "Allow",
      "Principal": "*",
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::blog-static-files/*"
    }
  ]
}
```

---

#### ✅ Step 3: Enable Static Website Hosting

1. Go to **Properties → Static Website Hosting**
2. Enable it
3. Index document: `index.html`
4. Save
5. Note the **Endpoint URL**

---

#### ✅ Step 4: Create a CloudFront Distribution

1. Go to **CloudFront → Create Distribution**
2. **Origin domain**: Choose your S3 bucket from the list
3. Origin Access: Choose **Origin Access Control (recommended)**
4. **Viewer protocol policy**: Redirect HTTP to HTTPS
5. **Default root object**: `index.html`
6. Click **Create distribution**

⏳ Wait for deployment (\~10 mins)

---

#### ✅ Step 5: Test the CloudFront Distribution

* Go to the **CloudFront Domain Name** (e.g. `d1abcxyz.cloudfront.net`)
* Your site should load fast from **anywhere in the world**

---

## ✅ Summary of Outcomes

| Component                                       | Purpose                         |
| ----------------------------------------------- | ------------------------------- |
| SNS Topic (`order-events`)                      | Central event publisher         |
| SQS Queues (`payment-queue`, `inventory-queue`) | Parallel consumers for services |
| CloudFront                                      | Global CDN for static content   |
| S3 Bucket (`blog-static-files`)                 | Hosts static site               |
| index.html                                      | Root of the website             |

---