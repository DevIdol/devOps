# Create S3 Bucket using GitHub Actions

## Prerequisites

Before using this workflow, ensure you have the following:

1. **AWS Account**: You must have an AWS account to create an S3 bucket.
2. **AWS Credentials**: You need access keys for AWS, which include:
   - `AWS_ACCESS_KEY_ID`
   - `AWS_SECRET_ACCESS_KEY`
   - `AWS_REGION` (e.g., `ap-southeast-1`)
3. **GitHub Repository Secrets**: Store your AWS credentials as secrets in your GitHub repository:
   - Navigate to your repository on GitHub.
   - Go to **Settings** > **Secrets and variables** > **Actions**.
   - Click **New repository secret** to add the above credentials.

## Workflow Configuration

Create a new file in your repository at `.github/workflows/create-s3-bucket.yml` and add the following code:

```yaml
name: Create S3 Bucket

on:
  workflow_dispatch:
    inputs:
      bucketName:
        description: "Enter S3 Bucket Name"
        required: true

jobs:
  create-bucket:
    runs-on: ubuntu-latest

    steps:
      - name: Config AWS Credentials
        uses: aws-actions/configure-aws-credentials@v4.0.2
        with:
          aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
          aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
          aws-region: ${{ secrets.AWS_REGION }}

      - name: Create S3 Bucket
        run: |
          aws s3 mb s3://${{ github.event.inputs.bucketName }}
```
