---
title: 'Setup AWS S3 CLI - Host Web'
date: 2023-09-12T21:47:41+00:00
draft: false
tags:
  - bun
  - javascript
---

## AWS S3 bucket cli

To set up AWS CLI on macOS, you can use the following steps:

1. Install Python: AWS CLI requires Python 2.7.9 or later, or Python 3.4 or later. If you don't have Python installed on your system, you can download it from the official website.
2. Install AWS CLI: You can install AWS CLI using pip, the package installer for Python. Run the following command in your terminal:
    
    ```
    pip install awscli --upgrade --user
    
    ```
    
3. Verify the installation: After installing AWS CLI, you can verify the installation by running the following command:
    
    ```
    aws --version
    
    ```
    
    This command should return the version number of AWS CLI that you just installed.
    
4. Configure AWS CLI: To use AWS CLI, you need to configure it with your AWS credentials. You can do this by running the following command:
    
    ```
    aws configure
    AWS_KEY_ID
    AWS_SECRET
    ```
    
    This command will prompt you to enter your AWS Access Key ID, AWS Secret Access Key, default region name, and default output format. Once you enter these details, AWS CLI will be configured and ready to use.
    

That's it! You have now set up AWS CLI on your macOS system.

To host an S3 bucket, follow these steps:

1. Create an S3 bucket: You can create an S3 bucket using the AWS Management Console or AWS CLI.
2. Configure your bucket as a website: In the AWS Management Console, select your bucket and go to the "Properties" tab. Click on "Static website hosting" and select "Use this bucket to host a website". Enter the index document and error document names.
3. Set permissions: In the AWS Management Console, go to the "Permissions" tab and click on "Bucket Policy". Enter the following policy to allow public access to your website:
    
    ```
    {
        "Version": "2012-10-17",
        "Statement": [
            {
                "Sid": "PublicRead",
                "Effect": "Allow",
                "Principal": "*",
                "Action": "s3:GetObject",
                "Resource": "arn:aws:s3:::YOUR_BUCKET_NAME/*"
            }
        ]
    }
    
    ```
    
4. Upload your website files: Use the AWS Management Console or AWS CLI to upload your website files to your S3 bucket.
5. Access your website: Once the files are uploaded, you can access your website using the bucket's website endpoint URL.

That's it! You have now hosted your S3 bucket as a website.

To upload a `dist` folder to an S3 bucket using AWS CLI, follow these steps:

1. Open your terminal and navigate to the `dist` folder.
2. Run the following command to upload the folder to your S3 bucket:
    
    ```
    aws s3 cp dist s3://YOUR_BUCKET_NAME --recursive
    
    ```
    
    Make sure to replace `YOUR_BUCKET_NAME` with the name of your S3 bucket.
    

That's it! Your `dist` folder has now been uploaded to your S3 bucket using AWS CLI.