

---

# ✅ **Experiment 8: AWS Lambda Triggered by S3 Upload to Update DynamoDB**

---

## 🎯 **Objective:**

Set up a Lambda function that is **automatically triggered** whenever a **PDF or image file** is uploaded to an S3 bucket. The function captures metadata and updates a **DynamoDB** table with the event details.

---

## 🔧 **Task 1: Create a Lambda Function**

### ✅ **Steps:**

1. Go to **AWS Lambda → Create Function**
2. **Function Name**: `S3ToDynamoMetadataLogger`
3. **Runtime**: `Python 3.12` (or latest available)
4. Choose/Create an **Execution Role** with:

   * **AmazonS3ReadOnlyAccess**
   * **AmazonDynamoDBFullAccess**
5. Click **Create Function**

---

### ✏️ **Paste This Code** (From: `https://tinyurl.com/kmitlambdacode`)

```python
import boto3
from uuid import uuid4

def lambda_handler(event, context):
    s3 = boto3.client("s3")
    dynamodb = boto3.resource('dynamodb')

    for record in event['Records']:
        bucket_name = record['s3']['bucket']['name']
        object_key = record['s3']['object']['key']
        size = record['s3']['object'].get('size', -1)
        event_name = record['eventName']
        event_time = record['eventTime']

        dynamoTable = dynamodb.Table('newtable')
        dynamoTable.put_item(
            Item={
                'unique': str(uuid4()),
                'Bucket': bucket_name,
                'Object': object_key,
                'Size': size,
                'Event': event_name,
                'EventTime': event_time
            }
        )
```

6. **Deploy the code**

---

### 🧠 **Why:**

This Lambda function extracts S3 event metadata (bucket name, object key, size, timestamp) and logs it to a **DynamoDB table** named `newtable`.

---

## 🔧 **Task 2: Set Up S3 Trigger (PDFs and Images)**

### ✅ **Steps:**

1. Go to your **Lambda Function → Configuration → Triggers**
2. **Add Trigger**: Choose `S3`
3. Select the S3 bucket (e.g., `my-upload-bucket`)
4. **Event Type**: `PUT`
5. Add **suffix filter**:

   * `.pdf`
   * `.jpg`
   * `.jpeg`
   * `.png`
6. Enable trigger and click **Add**

---

### 🧠 **Why:**

This ensures the Lambda function is **only triggered** when **PDF or image files** are uploaded — not for other file types.

---

## 🔧 **Task 3: Set Up DynamoDB Table**

### ✅ **Steps:**

1. Go to **DynamoDB → Create Table**
2. **Table Name**: `newtable`
3. **Primary key**:

   * Partition key: `unique` (String)
4. Leave the rest default, and click **Create Table**

---

### 🧠 **Why:**

DynamoDB stores metadata logs like:

* Uploaded object name
* Upload time
* File size
* Event name (e.g., `ObjectCreated:Put`)

This acts as a **real-time upload audit log**.

---

## 🧪 **Test End-to-End**

1. Upload a `.pdf` or `.jpg` to your S3 bucket
2. Wait for Lambda to trigger
3. Go to **DynamoDB → Explore Table → newtable**
4. Check for new entry

✔️ You should see:

```json
{
  "unique": "UUID",
  "Bucket": "my-upload-bucket",
  "Object": "documents/file1.pdf",
  "Size": 24823,
  "Event": "ObjectCreated:Put",
  "EventTime": "2025-07-08T10:52:00.000Z"
}
```

---

## 📘 Architecture Diagram

```plaintext
   [User Uploads .pdf/.jpg]
              │
              ▼
      ┌────────────────────┐
      │    S3 Bucket       │
      │  (upload trigger)  │
      └────────┬───────────┘
               │
        Auto-triggered Event
               │
               ▼
      ┌────────────────────┐
      │     Lambda         │
      │ S3 Metadata Logger │
      └────────┬───────────┘
               │
    Writes metadata to DynamoDB
               │
               ▼
     ┌────────────────────┐
     │     newtable       │
     │ (Upload Records DB)│
     └────────────────────┘
```

---

## ✅ You Now Know How To:

* Create and deploy Lambda with Python
* Use event-driven architecture (S3 → Lambda → DynamoDB)
* Filter events using file suffix
* Automatically log file uploads with metadata

---

Would you like to:

* Add email notifications (via SNS)?
* Visualize logs with CloudWatch?
* Auto-archive large files?