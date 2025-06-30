## 🧪 **Experiment 11: Lambda Triggers on S3 Upload to Update DynamoDB**

**🧠 Goal:**
Whenever a file is uploaded to an **S3 bucket**, a **Lambda function** is triggered that **inserts file info into a DynamoDB table**.

---

### ✅ STEP 1: **Create an S3 Bucket**

1. Go to **AWS S3 → Create Bucket**
2. Set bucket name: e.g., `my-trigger-bucket`
3. **Uncheck**:

   * `Block all public access`
4. Enable:

   * ✅ **ACLs**
   * ✅ **Versioning**
5. Click **Create bucket**

🧠 **Why?**
ACLs and versioning help track uploads. Public access is optional — not needed here unless Lambda needs to read content publicly.

---

### ✅ STEP 2: **Create DynamoDB Table**

1. Go to **AWS DynamoDB → Tables → Create table**
2. Set table name: e.g., `S3UploadLogs`
3. Set Partition key: `fileName` (String)
4. Leave everything else default and click **Create Table**

🧠 **Why?**
DynamoDB will store file info like name, size, time, etc. We use `fileName` as the primary key to uniquely track each object.

---

### ✅ STEP 3: **Create the Lambda Function**

1. Go to **AWS Lambda → Create function**

2. Choose:

   * **Author from scratch**
   * Function name: `S3UploadHandler`
   * Runtime: `Python 3.x` or `Node.js` (your choice)
   * Permissions: Use an existing role like `LabRole` (must have access to S3 & DynamoDB)

3. Click **Create function**

🧠 **Why?**
Lambda is the automation here. It responds when something is uploaded to the S3 bucket.

---

### ✅ STEP 4: **Add S3 Trigger to Lambda**

1. Inside the Lambda function page → Click **Add trigger**
2. Select **S3**
3. Bucket: `my-trigger-bucket`
4. Event type: **All object create events**
5. Click **Add**

🧠 **Why?**
This links S3 with Lambda. Now, **every time an object is uploaded**, the Lambda function will be **automatically triggered**.

---

### ✅ STEP 5: **Write Lambda Function Code**

> Example (Python):

```python
import json
import boto3

dynamodb = boto3.resource('dynamodb')
table = dynamodb.Table('S3UploadLogs')

def lambda_handler(event, context):
    for record in event['Records']:
        bucket = record['s3']['bucket']['name']
        key = record['s3']['object']['key']
        size = record['s3']['object']['size']
        
        table.put_item(Item={
            'fileName': key,
            'bucket': bucket,
            'size': size
        })
        
    return {
        'statusCode': 200,
        'body': json.dumps('Data added to DynamoDB!')
    }
```

Then:

* Click **Deploy**

🧠 **Why?**
This function grabs the file info (name, bucket, size) from the S3 event and adds it to the DynamoDB table.

---

### ✅ STEP 6: **Test the Setup**

1. Go to **your S3 bucket**
2. Upload any file (e.g., `testfile.jpg`)
3. Wait a few seconds

🧠 Behind the scenes:

* S3 triggers Lambda
* Lambda reads event, extracts file info
* Lambda writes into DynamoDB

---

### ✅ STEP 7: **Verify in DynamoDB**

1. Go to **DynamoDB → Tables → S3UploadLogs**
2. Click **Explore Table Items**
3. You should see the entry:

   ```json
   {
     "fileName": "testfile.jpg",
     "bucket": "my-trigger-bucket",
     "size": 14520
   }
   ```

---

## 🎯 Summary of Resources and Why They Were Used:

| AWS Service  | Role in Experiment                                      |
| ------------ | ------------------------------------------------------- |
| **S3**       | Holds uploaded files and triggers Lambda                |
| **Lambda**   | Acts on the upload event to extract and process info    |
| **DynamoDB** | Stores metadata (file name, bucket, size) for reference |

---
