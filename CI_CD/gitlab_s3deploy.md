# GitLab CI/CD Setup for Next.js Static Site Deployment to S3 and CloudFront
---

## Prerequisites

1. **AWS Access and Secret Keys**: Make sure to set environment variables in your GitLab CI/CD settings:

   - `AWS_ACCESS_KEY_ID`
   - `AWS_SECRET_ACCESS_KEY`
   - `AWS_DEFAULT_REGION`
   - `AWS_S3_BUCKET_NAME`
   - `AWS_CLOUDFRONT_DISTRIBUTION_ID`

2. **GitLab CI/CD Configuration Path**:
   - Ensure your `.yml` configuration file is named as `.gitlab-ci.yml`.
   - Alternatively, if you wish to use a different name, set it under **Settings** > **CI/CD** > **General pipelines** > **Custom CI configuration path**.

---

## `.gitlab-ci.yml` Configuration

Here’s a detailed breakdown of the `.gitlab-ci.yml` file:

```yml
# Use Node.js Alpine image for lightweight and efficient environment
image: node:20-alpine # Use Node.js 20 in Alpine Linux for a minimal setup

# Define pipeline stages
stages:
  - build # Stage for building the application
  - deploy # Stage for deploying to S3 and CloudFront

# Build job configuration
build-prod:
  stage: build
  when: manual # Trigger build manually for more control
  script:
    - npm install # Install Node.js dependencies
    - npm run build # Build the project for production
  artifacts:
    paths:
      - node_modules/ # Cache node_modules for faster subsequent builds
      - out/ # Directory where the build output is saved
  only:
    - main # Run build only on the main branch

# Deployment job configuration
deploy-prod:
  stage: deploy
  when: on_success # Deploy only if the build job succeeds
  needs:
    - job: build-prod # Ensure artifacts from build-prod are available
      artifacts: true # Access artifacts for deployment
  script:
    # Install the AWS CLI in the Alpine environment
    - apk add --no-cache aws-cli

    # Configure the AWS CLI using environment variables
    - aws configure set aws_access_key_id $AWS_ACCESS_KEY_ID
    - aws configure set aws_secret_access_key $AWS_SECRET_ACCESS_KEY
    - aws configure set region $AWS_DEFAULT_REGION

    # Sync the build output (out/) to the specified S3 bucket
    - aws s3 sync out/ s3://$AWS_S3_BUCKET_NAME --delete
      # --delete removes files in the bucket not present in the 'out/' directory

    # Invalidate CloudFront cache to make new changes live immediately
    - aws cloudfront create-invalidation --distribution-id $AWS_CLOUDFRONT_DISTRIBUTION_ID --paths "/*"
      # This command purges all cached files, allowing the latest version to load

    # Confirm successful deployment
    - echo "Deployed to production"
  only:
    - main # Deploy only when changes are pushed to the main branch
```

---

This setup enables efficient, controlled deployment of your static Next.js project to AWS S3 and CloudFront through GitLab CI/CD.
