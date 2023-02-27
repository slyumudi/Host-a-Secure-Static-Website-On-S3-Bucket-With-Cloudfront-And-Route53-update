# Host a Secure Static Website On S3 Bucket With Cloudfront, Route53 And ACM For SSL

Architecure
![1  architecture](https://user-images.githubusercontent.com/99888333/221614737-32b60c4a-ad0f-4e21-9b45-6c12e48120b6.jpg)

The diagram above is a graphical representation of the infrastructure we will be setting up. We will be hosting our static website content on Amazon S3 bucket. Then, we will setup a custom domain name with AWS Route53 as our DNS, create SSL certificate to secure our content and finally, configure Cloudfront CDN for content caching and distribution.
