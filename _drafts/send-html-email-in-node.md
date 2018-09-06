---
layout: post
title: How To Send HTML Email in Node.js
comments: true
tags: [node.js]
icon: node.png
excerpt: If you reading this to learn how to send the beautiful HTML email you just finished designing, you've already done the hard part. While designing HTML email is a pain, sending HTML email may required an additional step or two than what you're used to but it's still super easy.
---

[<i class="fa fa-github"></i> Get the code](https://github.com/stvmlbrn/node-html-email-demo)

If you reading this to learn how to send the beautiful HTML email you just finished designing, you've already done the hard part. While designing HTML email is a pain, sending HTML email may required an additional step or two than what you're used to but it's still super easy.

To be clear, this tutorial does _not_ cover creating the HTML email template. You can find plenty of free templates online to use (and explanations why styling HTML email is difficult) and the github repo for this tutorial includes an example template. This tutorial is focused on the steps required to turn the HTML email template into content that can be rendered by email clients and sending it.




So let's get started. Here are 2 packages we can use together to send HTML emails in Node.js:
* `nodemailer` - [https://nodemailer.com/](https://nodemailer.com/) - This will define our email transport method.
* `email-templates` - [https://www.npmjs.com/package/email-templates](https://www.npmjs.com/package/email-templates) - Used for generating email content from pre-styled HTML templates suitable for email clients.

#### Define A Transport Method
The transport configuration defines how we are going to connect to a server or service to send the email. The configuration object passed to the _createTransport()_ method is going to vary based on what you're using to send email. Maybe you have a local mail server like Microsoft Exchange, or maybe you're using a service such as Amazon SES or Mailgun. Whatever it is that handles the actual sending of your email is going to dictate what your configuration object looks like. For development purposes I really like to use [Mailtrap.io](https://mailtrap.io) so that's what I'll be using for this example. I can't recommend Mailtrap enough.

<pre class="prettyprint">
  <code class="language-javascript">
    const nodemailer = require('nodemailer');
    const transporter = nodemailer.createTransport({
      host: 'smtp.mailtrap.io',
      port: 465,
      secure: false,
      auth: {
        user: // mailtrap.io username
        pass: // mailtrap.io password
      }
    });
  </code>
</pre>

Our email transport is now ready to go.

#### Generate The Email Content And Send The Message
Now that the email transport is configured we can move on to generating the email content. Unfortunately, email clients have not kept pace with web browsers regarding how to render HTML content. This makes designing HTML emails a lot like designing web pages like a caveman - inline CSS, limited layout options, lack of consistency among clients, etc.

We can use tools like `email-templates` to allow us to still use a more modern development approach to designing HTML email templates by pre-processing the templates before sending. This pre-processing generates HTML suitable for email clients and provides other useful things by automatically inlining CSS (allowing developers to keep markup and styles separate), injecting local variables, and sending text-only content for clients that do not support HTML.

We'll start by pulling in the `email-templates` package and creating a new email object. As part of the email object, we'll assign the transport property to the transport we defined in the previous step. By default, `email-templates` will only actually send email in production environments, though it does provide a preview option that opens a new browser tab to allow you to see the email message. However, since we are using Mailtrap we can go ahead an allow sending and disable the preview since it's not needed.

<pre class="prettyprint">
  <code class="language-javascript">
    const Email = require('email-templates');

    const email = new Email({
      transport: transporter,
      send: true,
      preview: false,
    });
  </code>
</pre>

Now we are ready to get into the nitty-gritty. This next step requires a little bit more explanation. By default, `email-templates` will look for a certain directory structure to find what template to use for the message. Basically, it looks for an _emails_ folder off the root of the project with sub-folders that match the name of each template. In this example, the name of the template we'll be using is 'hello'.

<pre class="prettyprint">
  <code class="language-bsh">
    |- app.js
    |- emails
      |- hello
        |- html.pug
        |- subject.pug
        |- text.pug
  </code>
</pre>

The 3 file that can be included under each template folder are pretty self explanatory (the content of these files used in this demo can be found in the github repo):

* html.pug - The HTML markup to be used as the content of the message.
* subject.pug - The subject of the email.
* text.pug - A text version of the message for clients that do not support HTML.

As you probably already noticed, `email-templates` uses the Pug template engine. If you prefer to use something else like EJS, you can. And if you want to keep your templates in another location, you can do that as well. Both of these settings can be defined by modifying the email object we created in the previous step (though we'll be sticking with the defaults for the rest of this tutorial):

<pre class="prettyprint">
  <code class="language-javascript">
    const Email = require('email-templates');

    const email = new Email({
      transport: transporter,
      send: true,
      preview: false,
      views: {
        options: {
          extension: 'ejs',
        },
        root: 'path/to/email/templates',
      },
    });
  </code>
</pre>

We are finally ready to send the email. As part of the _send()_ method we need to indicate the name of the template, some message properties such the sender and recipient information, and any local variables we want to inject into the email content or subject.

<pre class="prettyprint">
  <code class="language-javascript">
    email.send({
      template: 'hello',
      message: {
        from: 'Steve Milburn <no-reply@blog.com>',
        to: 'john@snow.com',
      },
      locals: {
        fname: 'John',
        lname: 'Snow',
      },
    }).then(() => console.log('email has been send!'));
  </code>
</pre>

When we preview the message in Mailtrap, the HTML content looks like this:

![HTML email output](/assets/images/posts/email-html-preview.png)

and the text-only content looks like this:

![Text email output](/assets/images/posts/email-text-preview.png)

Putting all the code together looks like this:

<pre class="prettyprint">
  <code class="language-javascript">
    const nodemailer = require('nodemailer');
    const Email = require('email-templates');

    const transporter = nodemailer.createTransport({
      host: 'smtp.mailtrap.io',
      port: 465,
      secure: false,
      auth: {
        user: // mailtrap.io username
        pass: // mailtrap.io password
      }
    });

    const email = new Email({
      transport: transporter,
      send: true,
      preview: false,
    });

    email.send({
      template: 'hello',
      message: {
        from: 'Steve Milburn <no-reply@blog.com>',
        to: 'john@snow.com',
      },
      locals: {
        fname: 'John',
        lname: 'Snow',
      }
    }).then(() => console.log('email has been sent!'));
  </code>
</pre>

This tutorial just scratches the surface of what can be done with these packages. There are a myriad of ways you can define the email properties, send attachments, reference external CSS files, localization, and so on. What we've done in this tutorial is enough to get started. I encourage you to continue to look at the documentation for both packages to utilize them to their full potential.

Cheers!
