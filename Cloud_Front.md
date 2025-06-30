Cloud Front
---

## ✅ STEP 1: Create an S3 Bucket

1. Go to **S3 Console**.
2. Click **"Create bucket"**.
3. Give it a **unique name** (e.g., `my-static-site-demo`).
4. **Uncheck "Block all public access"** (you’ll manage access via CloudFront OAC later).
5. Click **Create Bucket**.

---

## ✅ STEP 2: Upload `index.html`

1. Inside the bucket, click **Upload**.
2. Upload your `index.html` file (simple HTML file for your homepage).
3. Confirm the file is uploaded under **Objects**.

---

## ✅ STEP 3: Enable Static Website Hosting

1. Go to **Properties** tab of the bucket.
2. Scroll to **"Static website hosting"**.
3. Choose **"Enable"**.
4. **Index document**: `index.html`.
5. Save settings.
6. Note the **S3 Website Endpoint** URL (you can test it here, but it won’t work publicly yet if blocked).

---

## ✅ STEP 4: Create CloudFront Distribution

1. Go to **CloudFront Console**.
2. Click **"Create Distribution"**.
3. Under **Origin**, choose:

   * **Origin domain**: select your S3 bucket name (should look like `my-static-site-demo.s3.amazonaws.com`)
4. Click **“Create Origin Access Control (OAC)”** if not already created:

   * Name: e.g., `S3-OAC`
   * Sign requests: **Yes (recommended)**
   * Save.
   * Attach this OAC to the origin.

---

## ✅ STEP 5: Configure Distribution Settings

1. Under **Default cache behavior**:

   * Viewer protocol policy: **Redirect HTTP to HTTPS**
   * Allowed HTTP methods: **GET, HEAD**
2. Under **Settings**:

   * **Default root object**: `index.html`
3. Click **Create Distribution**.

---

## ✅ STEP 6: Attach WAF (Optional Security Step)

1. Go to **AWS WAF Console**.
2. Create a **Web ACL** (if not already created).

   * Scope: **CloudFront**
   * Add rules like:

     * IP rate limiting
     * Geo blocking
     * SQL injection protection
3. Attach this Web ACL to your **CloudFront distribution**.

---

## ✅ STEP 7: Update S3 Bucket Policy for OAC

1. Go to **S3 → Permissions** tab → **Bucket Policy**.
2. Add a policy like below to allow access from the CloudFront OAC:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "AllowCloudFrontAccess",
      "Effect": "Allow",
      "Principal": {
        "Service": "cloudfront.amazonaws.com"
      },
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::my-static-site-demo/*",
      "Condition": {
        "StringEquals": {
          "AWS:SourceArn": "arn:aws:cloudfront::<account-id>:distribution/<distribution-id>"
        }
      }
    }
  ]
}
```

> 🔁 Replace `<account-id>` and `<distribution-id>` with your actual values.

---

## ✅ STEP 8: Test the Setup

1. Wait for the CloudFront distribution to finish deploying (can take \~10 minutes).
2. Copy the **CloudFront domain name** (e.g., `d123456abcdef.cloudfront.net`).
3. Open it in a browser: you should see your **`index.html` page rendered**.

---

## 🧹 Final Notes

* You now have a **secure, globally cached** static website powered by CloudFront and backed by S3.
* If you make changes to `index.html`, remember to **invalidate the cache** in CloudFront for the changes to reflect.
* Use WAF to guard against abuse (rate limiting, bot protection).

---

