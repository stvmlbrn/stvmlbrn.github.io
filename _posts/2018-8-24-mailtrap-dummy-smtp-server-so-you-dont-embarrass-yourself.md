---
layout: post
title: Mailtrap.io - A Dummy SMTP Server So You Don't Embarrass Yourself
comments: true
tags: [utilities]
icon: gears.png
---

If you have a program/application which sends email you should really take a look at [Mailtrap.io](https://mailtrap.io/). Mailtrap provides a fake SMTP server to which you can send email during testing/development and a reasonable pricing model that includes a free plan. I love it.

It includes a host of features like spam analysis and blacklist reports as well as HTML, text, and mobile views of your messages. But to me the most valuable service is the ability to test email procedures to make sure the correct customers, and only the correct customers, are recipients.

For example, imagine writing a procedure that sends a discount code to any customer that has been using your service for longer than 12 months. You write the query to fetch the customers, loop through the results and send an email to each one with a discount code. During your testing you can have your mail transport configured to use Mailtrap so your program functions exactly as it will in production, but the messages are sent to Mailtrap instead of your customers. This not only gives you the opportunity to view the email as it will appear in your customer's inbox, but it gives you the chance to review the recipients before running in production to make sure an mistake hasn't been made to include (or exclude) any qualifying customer.

So, check it out. I think you'll find it a valuable asset to your development process.

By the way, if you use an environment configuration setup [like I discuss in another post](https://blog.stvmlbrn.com//2018/01/13/maintain-multiple-configurations-and-secrets-in-node-apps.html), you're email transport configuration will change seemlessly between your development and production environments so you won't have to change a thing when you're ready to run your email procedure in production.

Cheers!

