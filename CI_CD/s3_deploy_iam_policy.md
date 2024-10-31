# AWS IAM Policy for S3 and CloudFront Deployment

---

## Step 1: Create an IAM User

1. **Navigate to the IAM Console:**
   Go to the AWS IAM Console [here](https://console.aws.amazon.com/iam/).

2. **Create a New User:**
   - Click **Users** > **Add User**.
   - Enter a **User Name** (e.g., `s3-deploy-user`).

---

## Step 2: Add Policy JSON

Add the following JSON to allow the user full permissions on the specified S3 bucket and CloudFront distribution.

- **IAM** > **Users** > **s3-deploy-user** > **Permissions** > **Add Permissions** > **Select Create inline policy** > **Select Json**.

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": ["s3:*"],
      "Resource": [
        // "arn:aws:s3:::${AWS_S3_BUCKET_NAME}/*"
        "arn:aws:s3:::devidol-s3-testing",
        "arn:aws:s3:::devidol-s3-testing/*"
        //...etc
      ]
    },
    {
      "Effect": "Allow",
      "Action": ["cloudfront:*"],
      "Resource": [
        //"arn:aws:cloudfront::${AWS_ACCOUNT_ID}:distribution/${AWS_CLOUDFRONT_DIST_ID}"
        "arn:aws:cloudfront::209479304651:distribution/E29EL332O61ASJ"
      ]
    }
  ]
}
```

### Notes:

- **S3 Bucket ARN**:

  - Go to **AWS S3 Console** > Select your bucket.
  - Bucket ARN format: `arn:aws:s3:::your-bucket-name` (for the bucket itself) and `arn:aws:s3:::your-bucket-name/*` (for all objects within the bucket).

- **CloudFront Distribution ARN**:
  - Go to **AWS CloudFront Console** > Find your distribution in the list.
  - Distribution ID will be in the format `arn:aws:cloudfront::account-id:distribution/distribution-id`.

---
