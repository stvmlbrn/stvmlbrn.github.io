---
layout: post
title: Force File Download From Amazon S3 With Node/Express
comments: true
tags: [node.js, express, aws]
icon: node.png
---

Generally, when content is returned from a web server to a browser it is intended to be displayed _inline_, meaning rendered as a web page or as part of a web page. This is especially true for file types such as images, html documents, PDF documents, etc. that can be rendered natively by web browsers. There are times, however, when we would rather have the user be prompted to download and save the file locally. Here is a quick look at how we can send files from S3 (or other remote locations) to be downloaded and saved locally on the client.

Express already offers the _response.download()_ method that can take care of this for us if the file is local to the web server. But if we need to force a download of file located somewhere else, like an Amazon S3 bucket for example, we need to handle this ourselves.

To force the browser to download the file we need to set  _Content-Disposition_ header in the response to _attachment_ (a default name for the file can also be specified). Then to actually serve the file to the client, we can use the `reqeust` package to make the request to the remote URL and pipe it to the response object.

<pre>
  <code class="language-javascript">
    const request = require('request');

    app.get('/download', (req, res) => {
      res.set(
        'Content-Disposition',
        'attachment; filename=some_file_name.jpg'
      );

      request(url_to_file).pipe(res);
    });
  </code>
</pre>

Notice that I'm setting the default filename to _some_file_name.jpg_. This, of course, assumes that I already know the file is a jpeg. If you are going to be specifying a value for filename you will likely want to do some work here to determine the correct file extension based on what file is being requested.

