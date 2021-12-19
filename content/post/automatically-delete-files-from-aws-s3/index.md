---
title: Automatically delete files from AWS S3
date: 2021-12-19T22:42:44.331Z
draft: true
featured: false
image:
  filename: featured
  focal_point: Smart
  preview_only: false
---
On the AWS (Amazon Web Service) platform, we can easily automatically delete data from our S3 bucket. We open [Amazon S3](https://s3.console.aws.amazon.com/) and select one bucket from the list, on which we want to enable automatic deletion of files after a specified time.

Go to Management and click Create lifecycle rule.

We give the name of our rule. Select the option saying that our changes are to apply to all objects and select the checkbox that appears. We must be aware that **all objects older than the number of days listed below will be deleted.**



If we want the files to be deleted after 30 days, select the “Expire current versions of objects” option and **enter the number of days after which the files will be deleted.**

![aws s3 - lifecycle rule actions](https://lepczynski.it/wp-content/uploads/2021/04/aws-s3-lifecycle-rule-actions-1024x610.webp)

In summary, we should see something similar to the picture below. When everything is correct, click on **Create rule** and our automatic file deletion rule will be ready.



<!--EndFragment-->