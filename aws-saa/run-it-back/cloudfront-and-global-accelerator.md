---
description: 22/10/24 we resume
---

# CloudFront & Global Accelerator

CDN, caches content at the edge and users reach to these edge locations first - DDoS protection, integrated with Shield and AWS WAF

No private VPC CONNECTIVITY, your resources must be public

### Origin (What is this even caching?)

Use this for S3 to distribute files and cache them at edge, also to upload files to S3 (ingress)\
Enhanced security with Cloudfront Origin Access Control (OAC)

Also, custom HTTP such as ALB, EC2, S3 static website or any HTTP backend

<div align="left"><figure><img src="../../.gitbook/assets/image (5) (1) (1) (1).png" alt="" width="563"><figcaption><p>allow the public IP via security policys (og stuff)</p></figcaption></figure></div>

Restrict access via allowlist or blocklist

### Cache Invalidation

If you change content before TTL, Cloudfront wouldn't know right. We can force a partial refresh via invalidation, all files (\*) or a specific directory (/images/\*)

## Global Accelerator



<div align="left"><figure><img src="../../.gitbook/assets/image (98).png" alt="" width="278"><figcaption></figcaption></figure></div>

Instead of going over public internet and increase hops along w latency, use internal AWS network to route our application.

Create 2 anycast IP, these IP send traffic directly to edge location\
Works with Elastic IP, EC2, ALB, NLB, **public or private**

**Cloudfront vs Global Accelerator??**
