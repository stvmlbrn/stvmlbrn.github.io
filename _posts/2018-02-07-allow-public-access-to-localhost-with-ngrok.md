---
layout: post
title: Need To Allow Public Access To Localhost? Check Out Ngrok
comments: true
tags: [node.js, express, utilities]
icon: gears.png
---

During the development process there may be times when you need to allow public access to your local development environment. You may want to demo your progress to a client or test an incoming webhook, for example. With ngrok [(https://ngrok.com)](https://ngrok.com) there is no need to push to production or some other public facing environment in order to accomplish this.

For example, let's say you are developing a application that is running on port 3000. With ngrok installed we can run the `ngrok` command and specify the port we want to map:

<pre>
  <code class="language-bash">
    $ ngrok http 3000
  </code>
</pre>

The output should look something like this:

<img src="/assets/images/posts/ngrok_command.png" />

As you can see in the above image, ngrok maps http and https URLs to localhost. In this case, I can share `https://c8c102d8.ngrok.io` and anyone, from anywhere, can access my application for as long as I keep the ngrok process running (and, of course, my application is running). Once I quit the ngrok process the tunnel is dissolved. Pretty cool, right? And incredibly handy. Best of all, ngrok is completely free! You can signup for a free account to get some additional features, or for a paid account for even more awesomeness such as reserved domains. However, signing up is completely optional. If you choose to, you can simply download the ngrok utility and start using it.

#### Integrating With Node.js Apps

ngrok works regardless of your technology stack. It simply acts as a tunnel from the public internet to your private server. However, with the `ngrok` package on NPM [(https://www.npmjs.com/package/ngrok)](https://www.npmjs.com/package/ngrok) it happens to work extremely well with Node.js. Let's take a look at a basic Express server:

<pre>
  <code class="language-javascript">
    const express = require('express');
    const app = express();
    const port = process.env.PORT || 3000;

    app.listen(port, () => {
      console.log(`Server listening on port ${port}`);
    });
  </code>
</pre>

This is a pretty simple express setup. Assuming this file is saved as `index.js`, we can start it with

<pre>
  <code class="language-bash">
    $ node index.js
  </code>
</pre>

 and expect to see the following output:

<img src="/assets/images/posts/basic_express.png" />

To integrate ngrok we first need to install it from NPM:

<pre>
  <code class="language-bash">
    $ npm i ngrok
  </code>
</pre>

Now let's modify our express server to conditionally open an ngrok tunnel. We could use something like a command-line argument to the node process to indicate whether to open a tunnel or not, but I like to use an environment variable for this. We'll tell our program that if `process.env.ENABLE_TUNNEL` is true to load `ngrok` and start the server with a tunnel.

<pre>
  <code class="language-javascript">
    const express = require('express');
    const app = express();
    const port = process.env.PORT || 3000;
    // only need to require ngrok if process.env.ENABLE_TUNNEL = true
    const ngrok = process.env.ENABLE_TUNNEL ? require('ngrok') : false;

    app.listen(port, () => {
      if (ngrok) { // open a tunnel
        ngrok.connect(port, (err, url) => {
          if (err) {
            return console.log(err);
          }
          console.log(`Server started. Tunnel running at url ${url}`);
        });
      } else { // start normally
        console.log(`Server listening on port ${port}`);
      }
    });
  </code>
</pre>

At this point, if we set ENABLE_TUNNEL=true in our runtime environment (syntax varies based on your OS - on Mac/Linux use `export`, on Windows use `set`) and start the app a tunnel should be created.

<img src="/assets/images/posts/express_with_ngrok.png">

The application is now available publicly using `https://90b9b15e.ngrok.io`.
