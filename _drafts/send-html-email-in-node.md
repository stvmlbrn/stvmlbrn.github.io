---
layout: post
title: Send HTML Email in Node.js
comments: true
tags: [node.js]
icon: node.png
---

Let's take a look at 2 packages we can use together to send HTML emails in Node.js:
* `nodemailer` - [https://nodemailer.com/](https://nodemailer.com/) - This will define our email transport method.
* `email-templates` - [https://www.npmjs.com/package/email-templates](https://www.npmjs.com/package/email-templates) - Used for inlining CSS and generating HTML suitable for email clients.

#### Defining A Transport Method
The transport configuration defines how we are going to connect to a server or service to send the email. The configuration object passed to `nodemailer.createTransport()` is going to vary based on what you're using to send email. Maybe you have a local mail server like Microsoft Exchange, or maybe you're using a service such as Amazon SES or Mailgun. Whatever it is that handles the actual sending of your email is going to dictate what your configuration object looks like. For development purposes I really like to use [Mailtrap.io](https://mailtrap.io), so that's what I'll be using for this example.

<pre class="prettyprint">
  <code class="language-javascript">
    const nodemailer = require('nodemailer');
    const transporter = nodemailer.createTransport({
      host: 'smtp.mailtrap.io',
      port: 465,
      secure: true,
      auth: {
        user: // my mailtrap.io username
        pass: // my mailtrap.io password
      }
    });
  </code>
</pre>

Our email transport is now ready to go.

#### Generating Email HTML And Send The Message
Now that the email transport is configured we can move on to generating the email content. Unfortunately, email clients have not kept pace with web browsers regarding how to render HTML content. This makes designing HTML emails a lot like designing web pages like a caveman - inline CSS, limited layout options, lack of consistency among clients, etc.

Fortunately, we can use tools like `email-templates` to allow us to still use a more modern development approach to designing HTML email templates by pre-processing the templates before sending. This pre-processing generates HTML suitable for email clients by automatically inlining CSS as well as other useful things like injecting local variables and generating text-only content for clients that do not support HTML.

Let's go ahead and import `email-templates` and create a new email class:

<pre class="prettyprint">
  <code class="language-javascript">
    const Email = require('email-templates');

    const email = new Email({
      transport: transporter, // this is the transporter we defined in the previous step
      send: true,
      preview: false,
    });
  </code>
</pre>

Put all together the code looks like this:

<pre class="prettyprint">
  <code class="language-javascript">
    const nodemailer = require('nodemailer');
    const Email = require('email-templates');

    const transporter = nodemailer.createTransport({
      host: 'smtp.mailtrap.io',
      port: 465,
      secure: true,
      auth: {
        user: // my mailtrap.io username
        pass: // my mailtrap.io password
      }
    });

    const email = new Email({
      transport: transporter,
      send: true,
      preview: false,
    });

    email.send({
      template: 'HelloWorld',
      message: {
        from: 'Steve Milburn <no-reply@company.com>',
        subject: 'Send Email Demo',
        to: 'john@snow.com',
      },
      locals: {
        fname: 'John',
        lname: 'Snow',
      }
    }).then(() => console.log('email has been sent!'));
  </code>
</pre>

