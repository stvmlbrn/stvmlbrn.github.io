---
layout: post
title: A Quick Summary Of My First Experience With Let's Encrypt
comments: true
---

If you are not familiar with [Let's Encrypt](https://letsencrypt.org), it is "a free, automated, and
open certificate authority (CA), run for the public’s benefit". Free? Automated? Open? What's not
to like about that, right? And as I happened to have some SSL/TLS certificates related to my
[CronAlarm](https://www.cronalarm.com) project coming up for renewal I thought now would be a
good time to give it a try.

The TL;DR version of the story is that Let's Encrypt, along with the wonderful documentation
from Digital Ocean on setting up [Let's Encrypt on Ubuntu 16.04 and Nginx](https://www.digitalocean.com/community/tutorials/how-to-secure-nginx-with-let-s-encrypt-on-ubuntu-16-04)
provided a near-perfect process. I highly recommend it, and please [donate to the Let's Encrypt project](https://www.generosity.com/community-fundraising/make-a-more-secure-web-with-let-s-encrypt)
if you are able.

However, I did experience an error during the authorization process that was neither the fault of Let's Encrypt or Digital
Ocean but I nonetheless thought it was worth mentioning here. In order for
Let's Encrypt to provide a certificate it has to be verify domain/DNS/IP configuration and that you have access
to the server. It does this via the Let's Encrypt Client, a utility that run on the server, writes a file to the filesystem,
and then attempts to access that file from the Internet. If successful, Let's Encrypt will issues your certificate.

It was during this process that I received a `Failed authorization procedure` error as seen here:
![Failed authorization procedure error]({{site.url}}/assets/images/posts/letsencrypt-error.png)

Hmm. The error says to make sure the DNS is setup correctly, but I was certain this wasn't the issue as I already
had the site successfully running on standard HTTP. A few quick google searches seemed to indicate that the Let's Encrypt utility
didn't have the proper access to do what it needs, but this didn't seem right either as I ran the utility with `sudo`.

It's worth noting at this point that I had been following the instructions verbatim. I was doing <em>exactly</em> what Digital
Ocean was telling me to do. The Digital Ocean tutorial assumes that you do not already have a web site configured in Nginx, therefore
the location block for the `/.well-known` directory (where the Let's Encrypt writes it's file use to verify your server)
is added to the default site configuration. But because I already had
a configuration setup for blog.cronalarm.com, it was this configuration file that was being used when the request
was coming in from Let's Encrypt. And, of course, the configuration for the blog.cronalarm.com domain didn't know how to access the
`/.well-known` directory and therefore the request failed.

There are 2 easy and obvious ways to fix this. First, we can add the location block to access the `/.well-known` directory
to the blog.cronalarm.com configuration in Nginx. My solution, however, was to remove the symlink in the Nginx `sites-enabled`
directory, run the utility again (which Nginx now handles with the default configuration file), then add the
symlink back to the Nginx `sites-enabled` directory. (Note: you'll need to start Nginx after removing and add the symlink files).

This really was a minor setback in the overall process. But as it appeared during my google searches that others were having
the same problem and I didn't see this solution anywhere I thought it worth putting here. All told, I had certificates
up and running in about 20 minutes and have since used Let's Encrypt for several other sites as well. I highly
recommend it, and with there being no cost associated there really isn't a reason not
to use HTTPS on everything.
