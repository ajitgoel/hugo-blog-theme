---
title: Improve performance of T-sql statements containing encrypted columns
date: 2022-03-23T03:14:38.669Z
draft: false
featured: false
tags:
  - Performance-Optimization;SQL-server;T-SQL
image:
  filename: featured
  focal_point: Smart
  preview_only: false
---
<!--StartFragment-->

We can use database level encryption to encrypt sensitive information. A commonly used level of encryption is to only encrypt columns that contain sensitive information like credit card numbers, date of birth etc. Encrypting and decrypting database level information is very CPU resource intensive. If a column is used as a primary key or used in comparison clauses (WHERE clauses, JOIN conditions) the database will have to decrypt the whole column to perform operations involving those columns.

We faced a similar performance issue in one of the previous companies. Database selects based on SSN were very slow as the database had to decrypt the whole column to perform comparison. If you look at the information below(under **Current Problem**) the database performed 4413 logical reads, 4406 read-ahead reads and took 302 milli seconds to perform the operation.

To improve the performance of these kinds of queries, we decided to store the last four digits of the SSN in clear text and index the column as a non-clustered index. First filtering the rows based on last four digits of the SSN and then filtering the rows based on SSN lead to a dramatic improvement in performance. f you look at the information below(under **Improved Solution**) the database performed 27 logical reads, 10 physical reads 10, 8 read-ahead reads and took 71 milli seconds to perform the operation.\
All in all a huge performance improvement.

**Current Problem:**

<!--EndFragment-->