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

- A domain name 

- An Amazon account 

- A static website (index.html file for example )

Let's get started!

## Static Website Setup With HTTP Protocol Using S3 Website Endpoint
**Setup S3 Bucket**
Login into your AWS account, select your region at top right of your dashboard and search for S3 in the top search bar.

![2](https://user-images.githubusercontent.com/99888333/221988285-2e017334-277a-428d-a5bc-d62e9d6ab8b8.png)

1. Click on create bucket. We are going to create a single bucket. (Note: This must be the name of your domain, for example `yourdomainname.com`)

2. Enter your bucket name. AWS S3 is a global resource hence, your bucket name must be **globally unique.**

3. Verify and select the region nearest to you or where you would like to serve your content from. I used the `us-east-1 (N.Virginia)` region for this project

![3](https://user-images.githubusercontent.com/99888333/221990333-8a6ceb9b-cc5a-4118-8419-cfe456218e38.png)

4. Uncheck the box under **Block Public Access settings** for this bucket and click on the **checkbox** to acknowledge your content will be visible to the public

![4](https://user-images.githubusercontent.com/99888333/221990643-aa7f4030-ad8e-45fb-8b50-1ba0130c5bd0.png)

5. To upload the website content, Click on the bucket and click **upload**. Navigate to the your files and folders of website from your computer and drag and drop. Then, click upload.

![5](https://user-images.githubusercontent.com/99888333/221990885-8a3083f4-cdcf-4964-8052-79fecd712cde.png)

6.  To configure the bucket, click on the bucket and then **properties tab.**

![6](https://user-images.githubusercontent.com/99888333/221991021-3120ef15-ca3e-47b8-b6dc-98276e1bb6ac.png)

7.  On the properties tab scroll down to the Static website hosting section, click on edit and select **Enable** , under Index document, enter `index.html` and under error document, enter `error.html` and click on save changes.

![7](https://user-images.githubusercontent.com/99888333/221991588-c19f0ea2-51a6-493b-9cfa-c823facce8d3.png)

Finally, we are going to add permission for our content to be viewable to the public. 

8. On the permissions tab of the bucket, scroll down to Bucket policy and click on edit

![8](https://user-images.githubusercontent.com/99888333/221992635-dc51b0b0-cb44-4fdf-af0f-80ddf5b3e1a3.png)

Enter the code snippet below and click on save changes. Make sure to replace _**sensiniblog.click**_ with your _**domain name**_
```bash
{
  "Id": "Policy1669914787641",
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "Stmt1669914786145",
      "Action": [
        "s3:GetObject"
      ],
      "Effect": "Allow",
      "Resource": "arn:aws:s3:::sensiniblog.click/*",
      "Principal": "*"
    }
  ]
}
```

![9](https://user-images.githubusercontent.com/99888333/221994202-adce7b69-124d-4e19-9122-964e53ed8167.png)



Notice the red notification under your bucket name - Publicly accessible, now your website is publicly accessible.

![10](https://user-images.githubusercontent.com/99888333/221994273-e81bb3bc-92fd-425f-8004-728cd5345e91.png)

At this point, the public can view your website content via S3 static website endpoint which look something like this **http://`your bucket name`.s3-website-`your region`.amazonaws.com**.
You can see your S3 static website endpoint by navigating to your bucket properties scrole all the way down to static website hosting section
`Amazon S3 > bucket name > properties > Static website hosting` 

![11](https://user-images.githubusercontent.com/99888333/221994674-ee136714-952b-4e2b-984b-30d11c6e52bf.png)






