# CircleCI Setup for Node.js Project with S3 Deployment

## Prerequisites

Before using this CircleCI configuration, ensure the following environment variables are set in your CircleCI project settings:

1. **AWS Credentials**:

   - `AWS_ACCESS_KEY_ID`
   - `AWS_SECRET_ACCESS_KEY`
   - `AWS_S3_BUCKET_NAME` (the name of your target S3 bucket)

2. **Install AWS CLI**: The AWS CLI is installed in the `build-deploy` job to manage deployment tasks.

---

## `.circleci/config.yml` Configuration

```yml
version: 2.1

orbs:
  node: circleci/node@5 # Use CircleCI's Node.js orb for convenient Node.js setup and management

jobs:
  test-node:
    # Job to install Node.js dependencies and run tests
    docker:
      - image: cimg/node:18.20.0-browsers # Use Node.js 18 image with browser support
    steps:
      - checkout # Check out the code from the repository
      - node/install-packages:
          pkg-manager: npm # Install dependencies with npm
      - run:
          name: Install ChromeHeadless # Install Chrome for headless browser testing
          command: |
            sudo apt-get update
            sudo apt-get install -y libappindicator1 fonts-liberation libu2f-udev libvulkan1
            wget https://dl.google.com/linux/direct/google-chrome-stable_current_amd64.deb
            sudo dpkg -i google-chrome-stable_current_amd64.deb
            sudo apt-get install -f
            echo $(google-chrome-stable --version)
            echo $(which google-chrome-stable)
      - run:
          name: Run tests # Run the project tests in headless mode
          command: npm run test:headless
      - store_test_results:
          path: test-results # Save test results for CircleCI to access

  build-deploy:
    # Job to build and deploy the Node.js project to AWS S3
    executor: node/default # Use default executor from CircleCI Node.js orb
    steps:
      - checkout # Check out the code from the repository
      - node/install-packages:
          pkg-manager: npm # Install dependencies with npm
      - run:
          name: Build Project # Compile the project for production
          command: npm run build:prod
      - store_artifacts:
          path: dist # Store the compiled files (artifacts) in the 'dist' directory
          destination: ./dist
      - run:
          name: Install AWS CLI # Install the AWS CLI for S3 sync
          command: |
            curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
            unzip awscliv2.zip
            sudo ./aws/install
            rm -rf awscliv2.zip aws
      - run:
          name: Upload to S3 # Deploy the build output to S3
          command: |
            DIST_OUTPUT_DIR=$(find dist -maxdepth 2 -type d | awk 'NR==3')
            aws s3 sync $DIST_OUTPUT_DIR s3://${AWS_S3_BUCKET_NAME}/ --delete
      - run:
          name: CloudFront Invalidation
          command: |
            aws cloudfront create-invalidation --distribution-id ${AWS_CLOUDFRONT_DIST_ID} --paths "/*"

workflows:
  build-and-test:
    jobs:
      - test-node # Run tests first
      - build-deploy:
          requires:
            - test-node # Only deploy if tests pass successfully
```
