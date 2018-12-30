---
layout: post
title: Allow Public Access To Private Files In An AWS S3 Bucket
comments: true
tags: [aws, node.js]
icon: s3amazon.png
---

For security purposes you may not want objects stored in your AWS S3 buckets to be publicly accessible (and by default, all objects in S3 are private). However, you likely still need to be able to serve them to properly authenticated users. This can be done by utilizing pre-signed URLs.

A pre-signed URL is created using the security credentials of the object owner and can be used to allow access to download the object for a specified period of time. I prefer to keep the URL expiration relatively low, somewhere around 20 seconds or less. This means that typically when a user clicks on a link to download a certain object (file, image, document, etc.) in my application, the client needs to make an ajax request to my API to get the pre-signed URL. When the URL is received from the API the client can then fetch the object from S3. If you are ok with allowing a longer expiration this ajax request is unnecessary - you can create the pre-signed URL(s) when the page is initially requested and embed the pre-signed URL(s) on the rendered page.

Amazon provides SDKs for the most popular programming languages. In this example I'll be using the JavaScript SDK in Node.js, so make sure the `aws-sdk` is installed:

<pre>
  <code class="lang-bash">
    npm i aws-sdk
  </code>
</pre>

As I already mentioned, I generate pre-signed URLs at an enpoint hit by an ajax request, so my code is typically found inside an `express` route. The client sends the ID (as stored in a database) of the object record as part of the request. In order to generate the pre-signed URL we need the name of the object, which I also have stored in the db. So the first step for me generally is to query the database for the name of the file using the ID passed in the request URL. Your method may vary.

<pre>
  <code class="lang-js">
    const aws = require('aws-sdk');
    aws.config.update({
      accessKeyId: // your AWS access key
      secretAccessKey: // your AWS secret key
    });
    const s3 = new aws.s3();

    router.get('/:key/downloadurl', (req, res, next) => {
      // start by querying the database for object details
      Document.getDetails(req.params.id)
        .then((result) => {
          const url = s3.getSignedUrl('getObject', {
            Bucket: bucketName, // the name of your bucket
            Key: result[0].filename, // name of object in S3
            Expires: 20 // how long the URL is good, in seconds
          });

          res.json({ url });
        });
    });
  </code>
</pre>

The `Document.getDetails()` function is a custom functon on my Documents model. Your code and method of retreiving the object filename will likely be different. I only include it in this example to provide a general idea of the steps that may be required.

That's it. The client will receive the pre-signed URL which it can then use to access the object in S3. After 20 seconds (or whatever length of time you specify) the link will no longer work.

The complete JavaScript SDK documentation, including how to create pre-signed URLs can be found here: [https://docs.aws.amazon.com/AWSJavaScriptSDK/latest/AWS/S3.html](https://docs.aws.amazon.com/AWSJavaScriptSDK/latest/AWS/S3.html)

Happy Coding!



