#Create and configure a Static Website With S3 And CloudFront
In this project, I have hosted static website using the Amazon Simple Storage Service (S3). It is secure, fast, protected against data loss, and can scale to support enterprise-level traffic. To do that, I have to store my website files on Amazon S3 and also use S3 to deliver my content to visitors to my website.

After setting up the static website on S3, this project will show that how to use Amazon CloudFront to create a content delivery network (CDN). A CDN makes website content available from data centers around the world, called edge locations. Using edge locations improves the speed of your website by reducing latency. Doing so is especially important if your website displays large media files such as high-resolution images, audio, or video.

Project Objectives
Configure static website hosting on Amazon S3
Configure static websites to work with CloudFront distributions
Prerequisites
Creating and navigating S3 buckets
Creating CloudFront distributions
Step 1: Logging In to the Amazon Web Services Console
login to your AWS account using your credentials.

Step 2: Creating an Amazon S3 Bucket for a Static Website
In the AWS Management Console search bar, enter S3, and click the S3 result under Services:

To start creating a new Amazon S3 bucket, in the top-right, click Create bucket:

Under General configuration, enter the following: Bucket name: Enter

demo-bucket- (Append a unique number to the end of demo-bucket-)
Region: Ensure US West (Oregon) us-west-2 is selected
You have added a unique number to the bucket name because Amazon S3 bucket names must be unique regardless of the AWS region in which the bucket is created.

Make sure to select ACLs Enabled:

In the Block Public Access section, un-check the Block all public access check box:

To acknowledge that you want to make this bucket publicly accessible, check I acknowledge that the current settings might result in this bucket and the objects within becoming public:

To finish creating your Amazon S3 bucket, scroll to the bottom of the form and click Create bucket:

Enable static website hosting for your bucket:
In the list, click the name of your bucket:

In the row of tabs under Bucket overview, click Properties:

The Properties tab allows you to enable and disable various Amazon S3 bucket features.

Scroll to the bottom of the Properties page and in the Static website hosting section, on the right, click Edit:
The Edit static website hosting form will load.

In the Static website hosting field, select Enable:

Enter the following, leaving all other fields at their defaults:

Index document: Enter index.html
Error document: Enter error/index.html
To finish enabling static website hosting, scroll to the bottom, and click Save changes:
Your Amazon S3 bucket is ready to host. In this step, you created an Amazon S3 bucket and you configured it to host a static website.

In the next step, you will upload an example static website to your bucket and modify your bucket's permissions to allow access.

Step 3: Uploading a Static Site to an Amazon S3 Bucket
Set a bucket policy:
In the Amazon S3 console, in the row of tabs, click Permissions:

Scroll down to the Bucket policy section, and on the right, click Edit.

In the Policy editor, copy and paste the following and replace YOUR_BUCKET_NAME with the name of your S3 bucket:

{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "AddPerm",
      "Effect": "Allow",
      "Principal": "*",
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::YOUR_BUCKET_NAME/*"
    }
  ]
}
This policy will allow public access to all objects in your S3 bucket.

To save your bucket policy, at the bottom of the page, click Save changes.
Upload objects in s3 bucket:
In the row of tabs, click Objects.

To begin uploading the website to your Amazon S3 bucket, scroll down and click Upload:

In the Files and folders section, click Add files:

Using the file picker, select all files and folders from inside the Website folder.

Note: ensure you upload all the files and folders inside it but not the Website folder itself. To be able to access the website, the index.html file must be at the top-level of your Amazon S3 bucket.

Scroll to the bottom of the page and click Upload.

To exit the Upload form, on the right, click Close.

Test that your website is accessible:
To retrieve the endpoint for your bucket, click the Properties tab, scroll to the bottom, and click the copy icon next to the Bucket website endpoint:

Paste the endpoint into the address bar of a new browser tab.

You will see a website.

In this step, you created a bucket policy, uploaded an example website, and confirmed it was accessible publicly in a web browser.

AWS doesn't recommend serving websites directly from Amazon S3. Instead, they recommend using Amazon S3 as an origin for a Content Delivery Network (CDN). A CDN pulls copies of the site from the origin and stores them in multiple global locations. The main benefit of using a CDN is lower latency for users when they access the site.

In the next step, you will put your website behind a CDN by creating an Amazon CloudFront distribution.

Step 4: Creating an Amazon CloudFront Distribution for the Static Website
Amazon CloudFront is a global Content Delivery Network (CDN) that delivers data securely and efficiently. CloudFront pulls your website out to the edge of the network, reducing latency when accessed from different global locations.

In this step, you will set up an Amazon CloudFront distribution for your static site hosted in your Amazon S3 bucket, update the bucket policy to allow access to the CloudFront distribution, and update permissions to block public access to the S3 bucket.

Set up an Amazon Cloudfront distribution:
In the AWS Management Console search bar, enter CloudFront, and click the CloudFront result under Services:

To start creating a distribution, click Create a CloudFront Distribution:

Under Origin, in the Origin Domain text-box, enter the Amazon S3 static website hosting endpoint that you created earlier:

Under Origin, in the Origin access, select Origin access control settings and click Create control setting:

Under Create control setting, enter the following values:

Name: Enter demo-s3cf- (Append a unique number to the end of demo-s3cf-)
Signing behavior: Ensure Sign requests is selected
Origin access control secures S3 origins by allowing access to only designated distributions. This follows AWS best practice of using IAM service principals to authenticate with S3 origins.

Click Create
Leave all other fields in this section as well as the Default cache behavior section as their default values.

Select Do not enable security protections under Web Application Firewall (WAF):

Scroll down to Settings, and in the Price class selection, select Use only North America and Europe:

Amazon CloudFront minimizes end-user latency by delivering content from its entire global network of edge locations. Price Classes let you reduce your delivery prices by excluding Amazon CloudFront’s more expensive edge locations from your Amazon CloudFront distribution. In these cases, Amazon CloudFront will deliver your content from edge locations within the locations in the price class you selected and charge you the data transfer and request pricing from the actual location where the content was delivered.

In the Default root object field, enter index.html.
You are setting this field because Amazon CloudFront doesn't always transparently relay requests to the origin. If you did not set a default root object on the distribution you would see an AccessDenied error when you access the CloudFront distribution's domain later in lab step.

To finish configuring your distribution, at the bottom of the page, click Create distribution:
CloudFront automatically assigns an ID (top of the page) and a Distribution domain name to the distribution and starts updating the edge locations to serve your content:

Update the bucket policy:
Click Copy policy at the top of the page:
This policy provides a new bucket policy that provides the s3:GetObject permission to the distribution. This allows for blocking all public access to the S3 bucket while maintaining access to the website via the distribution.

To implement the policy, return to the bucket you created, click the Permissions tab, scroll down to the Bucket policy section, click Edit, paste the policy you copied, and click Save changes:
Block public access:
Under the Block public access (bucket settings) section click Edit, select the Block all public access box, click Save changes, enter confirm into the field, and click Confirm:
The previous Bucket website endpoint will no longer work and will return a 403 error.

Return to the CloudFront Distributions table:
It can take up to 15 minutes to deploy a new Amazon CloudFront distribution. Once complete, the Status will change to Enabled. Once your deployment is complete, continue with the instructions.

To view details of your distribution, click the random alphanumeric ID:

Copy the value of the Distribution Domain Name field:

Paste the domain name into the address bar of a new browser tab.

You will see the website that you uploaded to your Amazon S3 bucket display:

You are accessing the website through your Amazon CloudFront distribution.

Summary:
In this project, you created an Amazon S3 bucket and used it to host a static website. You created an Amazon CloudFront distribution and configured it to use your S3 bucket as an origin. You tested that you could access the static website directly through Amazon S3, and through your Amazon CloudFront distribution. You updated the S3 bucket policy to restrict access to only the CloudFront distribution and blocked all public access to the S3 bucket.
