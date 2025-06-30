## 🧠 What’s the Purpose of SNS and SQS?

### 📦 **SQS (Amazon Simple Queue Service):**

* **A queue** that temporarily holds messages.
* Other services (like EC2, Lambda) can **pull** messages from it.
* Used to **decouple** systems — so if one service is slow/down, others aren't affected.

### 📣 **SNS (Amazon Simple Notification Service):**

* A **publish-subscribe** (pub/sub) system.
* Sends a message to **multiple subscribers at once**.
* Subscribers can be:

  * Email 📧
  * SQS Queue 📦
  * Lambda ⚙️
  * HTTP endpoint 🌐

---

## 🎯 Goal of Experiment 14:

You're going to:

1. Create an **SNS Topic**.
2. Add **two subscribers**:

   * Email (so a human gets notified)
   * SQS Queue (so an app or service gets the message)
3. **Publish a message** to SNS — both subscribers receive it via different channels.

---

## ✅ Step-by-Step: Decoupled Services with SNS & SQS

---

### 🔸 Step 1: Create an SNS Topic

1. Go to **SNS → Topics → Create topic**
2. Choose:

   * Type: **Standard**
   * Name: `MyTopic`
3. Click **Create topic**

🧠 **Why?**
The topic is the central point where you "publish" a message — all subscribers will get it.

---

### 🔸 Step 2: Add an Email Subscription to the Topic

1. Inside your `MyTopic` → Click **"Create subscription"**

2. Set:

   * Protocol: **Email**
   * Endpoint: your email address (e.g., `abc@example.com`)

3. Click **Create subscription**

4. Check your email and **confirm the subscription** (very important)

🧠 **Why?**
This means a human can now receive alerts via email whenever a message is published.

---

### 🔸 Step 3: Create an SQS Queue

1. Go to **SQS → Create Queue**
2. Choose:

   * Type: **Standard Queue**
   * Name: `MyQueue`
   * Keep rest default, click **Create Queue**

🧠 **Why?**
This is where **systems** (like EC2, Lambda, etc.) can read messages for automated processing.

---

### 🔸 Step 4: Subscribe SQS to SNS Topic

1. Go back to **SNS → MyTopic**
2. Click **Create subscription**
3. Protocol: **Amazon SQS**
4. Select the queue: `MyQueue`
5. Click **Create subscription**

🧠 **Why?**
Now, whenever SNS gets a message, it also forwards it to this queue — for apps or services to process.

---

### 🔸 Step 5: Publish a Message to SNS Topic

1. In **SNS → MyTopic**, click **Publish message**
2. Set:

   * Subject: `Test Message`
   * Message body: `This is a test message for SNS-SQS`
3. Click **Publish**

🧠 **Result?**

* Email is sent to the email subscriber
* Message is delivered to the SQS queue

---

### 🔸 Step 6: View Message in Email and SQS

#### 📧 For Email:

* Go to your inbox → You'll see the message

#### 📦 For SQS:

1. Go to **SQS → MyQueue**
2. Click **Send and receive messages**
3. Scroll to **"Receive messages"** pane → Click **"Poll for messages"**
4. You’ll see the same message that SNS published

---

## 🔁 Summary of What You Built

| Component         | Role                              |
| ----------------- | --------------------------------- |
| SNS Topic         | Central place to publish messages |
| Email Subscriber  | Human-friendly notification       |
| SQS Queue         | Machine-friendly message storage  |
| SNS → Email & SQS | Decouples senders and receivers   |

---

## 📦 Real-World Use Case Example

Imagine:

* Your app uploads images to S3.
* You want to:

  * Alert the **admin** via email
  * Also **queue the image metadata** for processing

With this setup:

* SNS publishes: “New image uploaded”
* Email notifies admin
* SQS queues metadata for a Lambda to process

---
