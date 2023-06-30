# Intro
This project provides a high level overview for creating a static website. It covers the front end, AWS S3 static web hosting, and demonstrates AWS web service tools (DNS, CND, ect). By following the instructions and modifying the code, this guide equips you with the necessary tools to build a solid understanding of web development.
# HTML
HTML is the backbone of your website; it contains all the content and brings together your styling, scripts, and code. You can find templates [here](https://html5up.net/) or learn HTML from scratch to code a fully personalized website. Usually, the main HTML homepage is named `index.html`.

Here is a very basic example of HTML:
```
<!DOCTYPE html>
<html>
    <head>
        <title>Example</title>
    </head>
    <body>
        <p>This is an example of a simple HTML page with one paragraph.</p>
    </body>
</html>

```
To add words to the website, use the following code:
```
<p>write some words here so they show up on your website</p>
```
To add a link within text, you can put the following code in the middle of the previous code to have a link in the text:
```
<a href="example.com" target="_blank">example text</a>
```
To add icons (using [Font Awesome](https://fontawesome.com/v4/icons/)), modify the following code, replacing "icon brands fa-github" with the appropriate icon class:
```
<li><a href="example.com" class="icon brands fa-github" target="_blank"><span class="label">Github</span></a></li>
```
For images, call them by their file name. Add your own images to the image folder and change the code to reflect your image. For example:
```
<span class="image fit primary"><img src="images/pic01.jpg" alt="" /></span>
change to
<span class="image fit primary"><img src="images/mypicture.jpg" alt="" /></span>
```
### CSS
CSS controls the style of the website, including colors, sizes, fonts, etc. Editing a large style guide on a template can be tricky, but trial and error will help you determine what to change and edit to achieve the desired results. Use F3 or Ctrl+F to search for the setting you want to change, and make changes slowly while being prepared to revert them using undo (Ctrl+Z).

Important things to change include color. The first line below shows an example of a color in `hex value`, while the second line shows an example of `rgba`:
```
color: #464e50;
background: rgba(144, 144, 144, 0.15);
```
### Javascript
Javascript is mainly used for adding interactive elements to your website, such as scrolling buttons, scripts, and configurations that format your website for different devices. You shouldn't have to touch this unless you plan to add scripts or advanced functions to your website. All Javascript is called to your website in your HTML code using the following command:

To include a JavaScript file `myjavascript.js` located in the `assets/js/` directory, use the following code in your HTML:
```
<script src="assets/js/myjavascript.js"></script>
```
# AWS S3 Static Website
Hosting your website as a static website on AWS S3 is a cost-effective and efficient option. You don't need to worry about servers or IP addresses, and you can even have your website accessible without configuring a custom domain name.

First, create a bucket. You can keep all the default settings. Once the bucket is created, upload all your website files. If you have multiple files and/or multiple pages that your website redirects to, it is advisable to keep the `index.html` and other pages in the top layer of the bucket, and place assets (scripts) and images in separate folders. The file structure should be as follows:
```
yourbucket
	index.html
	page1.html
	about.html
	assets
		(css/js files here)
	images
		(images go here)
```
After that, go to Properties and enable static web hosting to obtain a public URL for your bucket, which will look like `http://mybucketname.s3-website-us-east-1.amazonaws.com/`. Also, go to the Permissions tab and enable public access.

To make your website accessible to the public, scroll down on the Permissions tab and edit the Bucket Policy. Copy and paste the following code into the bucket policy, ensuring that you replace "Bucket-Name" in `"arn:aws:s3:::Bucket-Name/*"` with your bucket's name:
```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "PublicReadGetObject",
            "Effect": "Allow",
            "Principal": "*",
            "Action": [
                "s3:GetObject"
            ],
            "Resource": [
                "arn:aws:s3:::Bucket-Name/*"
            ]
        }
    ]
}
```
Your website should be available via the link you saw in the static website settings (e.g., `http://mybucketname.s3-website-us-east-1.amazonaws.com/`). You can upload files with the same filename to replace them in the bucket, so if you update your `index.html`, you can just upload it to update your website.
# Web Services
Web services involve a more complex setup with various AWS services. While not mandatory, these steps can make your website more secure, feature-rich, and aligned with industry standards.

In this guide, we'll focus on two relevant services: CloudFront (HTTPS) and Route53 (DNS). Both offer numerous options and cater to different use cases. For detailed step-by-step instructions, refer to the documentation of CloudFront and Route53.

⚠️ **Please note that pricing for these services may apply beyond the free tier. It's essential to be cautious when setting up these services. Set up billing alerts to monitor charges and ensure you understand the pricing details to avoid unexpected costs.** ⚠️

## CloudFront
CloudFront is a Content Delivery Network (CDN) that helps distribute and deliver your website's content globally. It offers features like Web Application Firewall (WAF) for protection and caching to improve content delivery speed.

The key aspects to know about CloudFront are origins, protocols, and cache.
### Origins
In your case, the origin would be your S3 bucket. You want CloudFront to distribute the contents of your S3 bucket, so that will be your origin. This would also be the link you used previously to access your bucket.
### Protocol
One of the main purposes of using CloudFront might be to enable HTTPS on your website. By default, you will be accessing your website via HTTP. You need to enable the option to redirect viewers from HTTP to HTTPS when they visit your website. This ensures users have a secure, encrypted connection to your website.
### Cache
Your data will be cached in edge locations closest to the users. Depending on your deployment, you might have caches worldwide. While this reduces latency, it also means that if you make changes to your website, you'll need to invalidate the cache to ensure users receive the most recent version of your website. To do this, create a **Cache Invalidation**.
## Route53
You can perform this via the console or by using the following CLI command (requires CLI configuration):
Route53 is the DNS service that allows you to register and purchase a domain name and route traffic from your CloudFront distribution to your domain name. The cost of buying a domain typically ranges around $10, depending on the top-level domain you choose (.com, .net, .cloud, .xyz).
## AWS Certificate Manager
After buying your domain, you'll use AWS Certificate Manager (ACM) to enable SSL on your domain. SSL provides security and displays a padlock icon in the address bar. Request a certificate from ACM and provide your domain information. Note that certificate validation may take a few days.
## Route53 Records
Once you have your certificate, you can set up records in Route53 to route traffic to your website. Route53 allows you to use Alias records instead of CNAMES. You will need one A record for IPv4 and one AAAA record for IPv6.

Route the traffic to CloudFront, not your bucket this time. The final records will look something like the following:
```
Record Name: yournewdomain.com  Type: A  Value/Route Traffic to: d412h4tgzywinc.cloudfront.net.
Record Name: yournewdomain.com  Type: AAAA  Value/Route Traffic to: d412h4tgzywinc.cloudfront.net.
```
# Conclusion
These steps should help you set up a basic website using front-end technologies like HTML, CSS, and JavaScript. By following the AWS S3 static website and web services configuration, you can host your website and make it accessible to the public. Remember to refer to the official documentation for more detailed instructions on each step.
