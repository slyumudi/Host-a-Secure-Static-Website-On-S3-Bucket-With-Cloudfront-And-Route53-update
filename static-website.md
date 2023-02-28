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

**`This is fine but not memorable nor user friendly, hence we are going to setup route 53.`**

**Configure S3 website Endpoint with Route 53**
On your AWS dashboard, search for Route 53 in the search bar.

![12](https://user-images.githubusercontent.com/99888333/221997279-29a25fc6-9ac3-4245-ab64-1a43f941096f.png)

**Create a Hosted zone** 
1. Navigate to _Route 53_ > _Hosted zones_ > _Create hosted zone_
2. Under _**Domain name**_, enter your domain name (`yourdomainname.com`)
3. Ensure under _**Type**_, _**Public hosted zone**_ is selected and click _Create hosted zone_.
At this point, two records are created _**NS**_ (Name Servers) and _**CNAME**_(Canonical Name record).

![13](https://user-images.githubusercontent.com/99888333/221997630-15791718-c4ba-4c08-9149-41a5c9fefe73.png)

Finally, We will add an Alias record, mapping your `_**yourdomain.com**_` to the s33 website endpoint

**Add An A Record**
_For the A record_

1. Click on Create record
2. select `simple record` and click **Next**
3. Click define simple Record
4. Leave _**Record name**_ field blank,
5. Toggle the Alias button (If not seen, do not worry)
6. Select the **`A`** record type
7. Under _**Route traffic to**_ select _**Alias to S3 website endpoint**_
8. Choose the region where your bucket was created
9. select the bucket
10. Proceed and create the record

![14](https://user-images.githubusercontent.com/99888333/221998623-f6131d3e-ce85-477f-9287-083c02066f28.png)

It will take few seconds for the records to _SYNC_

_**Test website in the browser**_
Enter your domain in the browser _If your website is showing, sometimes you might need to clear your browser's cache._   

![15](https://user-images.githubusercontent.com/99888333/221999075-5c67e82c-e567-4dc2-8e0b-6051451daf3d.png)

Well, this is fine but **`not secure`** as you can see from the image above.

Let's make our website secure and increase the speed of distribution round the world by configuring a content delivery network - _**`AWS Cloudfront`**_.


## Static Website Setup with HTTPS protocol using AWS Cloudfront and AWS Certificate Manager ACM.

**Create two certificates using AWS certificate Manager (ACM)**

To make ensure your website is secure, you need a Secure Sockets Layer (SSL) certificate. On the search bar, type certificate manager and click enter.

![16](https://user-images.githubusercontent.com/99888333/222000004-222daa1d-64f0-408a-8a1f-b1b8f30f738e.png)

Click on _**`Request certificate`**_, ensure _**`Request a public certificate`**_ is selected and click next

![17](https://user-images.githubusercontent.com/99888333/222000200-14d0d2bd-b6cf-42e4-bf7e-f8a95958ff90.png)

3. Under _**`Fully qualified domain name`**_ enter your first domain name and click on _**Add another name to this certificate**_ to add the second domain name. **`If you have a subdomain you can include it here. Note how i used the wild card *. infront of my main domain. This will automatically include this certificate to any subsequent subdomain i create later on such as www.sensiniblog.click, dev.sensiniblog.click, test.sensiniblog.click etc`**
4. Ensure DNS validation is selected. (This is usually faster). This is done to ensure you are the owner of the domain name.
5. Click on Request.

![18](https://user-images.githubusercontent.com/99888333/222001436-ff3e16aa-215b-4976-8449-59057e59e1e0.png)

1. Click on **View certificate** on the top of the page or click on **List certificates**
2. Click on **Create records in Route 53**_ and click _**create records** at bottom of the page to confirm.

![19](https://user-images.githubusercontent.com/99888333/222002582-d00356cc-276e-4287-ae86-db5b45d26195.png)

This will take some minutes but it's usually faster if purchased your domain from Amazon. Verify that your certifiacte is issued and has changed from `pending state` to `success`

**Setup AWS Cloudfront**

Navigate to AWS Cloudfront on your dashboard.

![20](https://user-images.githubusercontent.com/99888333/222004390-7592fb5f-0828-4ed8-9af2-3ef2bc7282bd.png)

1. Click **Create a CloudFront distribution**
2. Under **Origin domain**_ select the bucket that holds your website content
3. Under **Origin access**, select **Origin access control settings** **NOTE** `you can select OAI option but this will mot only create the access policy for s3, but give you the option to automatically update your s3 bucket with the policy. The sole purpose of using OAC in this demo is to manually update the s3 bucket policy`
4. Under **Origin access control**, click on _**create control setting**_ this will allow amazon to automatically create an access policy for your S3 bucket for cloudfront.

![21](https://user-images.githubusercontent.com/99888333/222004440-df5661c2-77a5-4f20-8a3e-08eb4a20d146.png)

Accept the name and click **creat**

![22](https://user-images.githubusercontent.com/99888333/222004587-35a29226-94fd-4b8a-91e2-61eb47c14217.png)

Scroll down to **Viewer** and select **Redirect HTTP to HTTPS**

![23](https://user-images.githubusercontent.com/99888333/222004758-c174fb7a-7441-459d-b1b4-991e774c1ee4.png)

Scroll down to **Alternate domain name (CNAME)**, click on **Add item** and enter your domain name `yourdomain.com` 
Associate your custom ssl certificate you just created to this distribution as shown below

![24](https://user-images.githubusercontent.com/99888333/222005327-1197df67-214a-494d-b81d-7c89b37fce8a.png)

- Scroll down to **Default root object**, enter default page name - in this **`index.html`** and click on **create distribution**

![25](https://user-images.githubusercontent.com/99888333/222005764-68fe8e12-e975-4c53-85bb-c66e2ee9e3dc.png)

**Finally, we need to give permission to cloudfront on S3.** 
1. Click on the newly created distribution and click on the **origins tab**
2. Under **Origins** selected the origin and click on edit

![26](https://user-images.githubusercontent.com/99888333/222005950-952ae856-5939-4040-8476-6be955c9a5bc.png)

Scroll down to **Bucket policy** and click on **Copy policy**

![27](https://user-images.githubusercontent.com/99888333/222006126-e062efd7-b0c0-4026-a467-d50a097f0cb4.png)

Navigate to your S3 bucket **permissions** tab, scroll down to **Bucket policy**, click on edit, remove the initial policy that made our bucket public, and paste the policy you copied from the cloudfront distribution and save changes. Because this policy contains the cloudfront distribution ID and OAC/OAI, cloudfront is able to access your bucket and serve its content to the public without exposing the bucket to the public.

![28](https://user-images.githubusercontent.com/99888333/222007781-209694bf-85fa-4e6e-9695-7c8976935197.png)
Cloudfront will take some minutes to deploy the configurations

**The last step is to add the records in Route 53.**
_**Add Cloudfront Distribution to Route 53**_
1. Navigate to Route 53, click on create record **before creating an A record for the same domain to your distribution, ensure the initial A record created for this domain mapping it to s3 endpoint is deleted as you cant have two A records of same domain pointing to different targets**
2. For this A record, leave the _**Record name**_blank
3. Toggle the Alias button and select _**Alias Cloudfront Distribution under**_ _**Route traffic to**_ field.
4. select the cloudfront distribution for `yourdomain.com`
5. click on _**create record**_.
The record will take some seconds to _**SYNC**_

![30](https://user-images.githubusercontent.com/99888333/222008345-36841853-684d-4e24-a753-0d1e70de6d7d.png)

**`Test your secured website with low-latency in the browser`**

Enter your domain in the browser `yourdomain.com`.

![29](https://user-images.githubusercontent.com/99888333/222008510-d763d48e-b5f5-4f91-bc15-320203fab749.png)

Notice the lock icon in the URL, this means your website is secure.

**`Congratulation!!!`** You have successfully deployed a static website via HTTP and HTTPS protocols in AWS. Navigate to **`cleanup.md`** in this repo to cleanup your account.














