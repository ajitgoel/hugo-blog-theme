---
title: AWS S3 Bucket Permissions - Access Denied
date: 2022-01-18T02:33:24.944Z
draft: false
featured: false
tags:
  - AWS
  - AWS-S3
image:
  filename: featured
  focal_point: Smart
  preview_only: false
---
I am developing an application that allows users to create and edit QR codes. The generated QR codes are stored in AWS S3. I was however getting an `access denied`error when trying to access the publicly available QR codes from the application.  To fix this issue, I \
a. under the `Permissions`=> `Block public access (bucket settings)` section, ensure that the `Block public access to buckets and objects granted through`*`new`* checkbox is unchecked. 

![Block public access (bucket settings)](2022-01-17-20_40_30-window.jpg)

b. Under the `Permissions`tab for the AWS S3 bucket, add the following bucket policy using the `bucket policy editor`. This bucket policy allows a user to access all the resources under the bucket. 

```
{
    "Version": "2012-10-17",
    "Id": "Policy1640324019701",
    "Statement": [
        {
            "Sid": "Stmt1640324016879",
            "Effect": "Allow",
            "Principal": "*",
            "Action": "s3:GetObject",
            "Resource": "arn:aws:s3:::clearcrimsongenerateqrcode/*"
        }
    ]
}
```