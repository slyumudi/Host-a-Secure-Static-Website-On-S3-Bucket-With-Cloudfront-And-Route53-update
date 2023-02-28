# Hosting a Secure Static Website On S3 Bucket With Cloudfront, Route53 And ACM For SSL
In this mini project, you will learn how to:

Create an S3 bucket and set it up for static website hosting
Create a record set in Route 53
Set up a CloudFront distribution and link it with a custom domain
Secure the connection via SSL and AWS Certificate Manager

![1  architecture](https://user-images.githubusercontent.com/99888333/221987387-ce003b10-32fa-40cc-bca3-6077842098f2.jpg)

The diagram above is a graphical representation of the infrastructure we will be setting up. We will be hosting our static website content on Amazon S3 bucket. Then, we will setup a custom domain name with AWS Route53 as our DNS, create SSL certificate to secure our content and finally, configure Cloudfront CDN for content caching and distribution. We are going to create the static website in two ways -

Serve the website publicly via HTTP protocol using S3 website endpoint.
Serve the website publicly on our domain via HTTPS protocol using SSL from ACM and Cloudfront.

**Prerequisite**:

A domain name 

An Amazon account 

A static website (index.html file for example )

Let's get started!

## Static Website Setup With HTTP Protocol Using S3 Website Endpoint
**Setup S3 Bucket**
Login into your AWS account, select your region at top right of your dashboard and search for S3 in the top search bar.

![2](https://user-images.githubusercontent.com/99888333/221988285-2e017334-277a-428d-a5bc-d62e9d6ab8b8.png)


