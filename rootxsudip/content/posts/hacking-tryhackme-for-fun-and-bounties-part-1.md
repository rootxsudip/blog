---
title: "Hacking TryHackMe for Fun and Bounties 👀 Part 1"
date: 2022-05-22T09:04:21+05:30
draft: false
toc: false
images:
tags:
  - bug bounty
---

Hello guys, during my hunting i find two vulnerabilities in THM, today i will talk about the first vuln which is S3 Bucket misconfiguration.  
If you don’t know what is s3 bucket and how it leads to vulns check the references.

### 1\. S3 Bucket Misconfiguation Leads to deletion of any images(profile,room images,banner etc..)

When, uploading my profile pic, i saw it was uploaded to a s3 bucket.  
My first thought was can i perform read, upload and delete operation in the S3 bucket?  
After that i tried 3 operations in the bucket: READ, UPLOAD, DELETE  
**AWS S3 Commands:**

- ❌ READ Contents: aws s3 ls s3://bucket-name/ –no-sign-request
- ❌ Upload Content: aws s3 cp poc.txt s3://bucket-name/poc.txt –no-sign-request
- ✅ Delete Content: aws rm s3://bucketname/user-avatars/my-pic.jpeg –no-sign-request  
  Only delete operation was successfull, this proved that i can delete any pics from the bucket.

**Impact:**  
An attacker can delete any pic in TryHackMe which includes profile pic of anyusers,room icon, room banner etc..

**How I made the POC:**

- Upload a pic in my 2nd account.
- Visit my 2nd account’s public profile from the 1st account.
- Using inspect, i copied the profile pic of that 2nd account.
- Deleted the pic using aws command -> aws s3 rm s3://tryhackme-images/user-avatars/2ndpic.png –no-sign-request

**How to find S3 Buckets?**:

- Check website’s source code(JS,HTML)
- Check CSP headers
- Google and Github dorking
- Use Tools like [https://github.com/sa7mon/S3Scanner](https://github.com/sa7mon/S3Scanner) etc..

**Some References:**

- [https://blog.lightspin.io/how-to-access-aws-s3-buckets](https://blog.lightspin.io/how-to-access-aws-s3-buckets)
- [https://labs.detectify.com/2017/07/13/a-deep-dive-into-aws-s3-access-controls-taking-full-control-over-your-assets/?utm_source=blog&utm_campaign=s3_buckets](https://labs.detectify.com/2017/07/13/a-deep-dive-into-aws-s3-access-controls-taking-full-control-over-your-assets/?utm_source=blog&utm_campaign=s3_buckets)
- [https://medium.com/@janijay007/s3-bucket-misconfiguration-from-basics-to-pawn-6893776d1007](https://medium.com/@janijay007/s3-bucket-misconfiguration-from-basics-to-pawn-6893776d1007)

When I reported the misconfiguation, they fixed it quickly in one day and rewarded me $$$

Thanks for reading and wait for the part 2 :)
