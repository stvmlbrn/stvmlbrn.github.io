---
layout: post
title: How To Install The Latest Version of Node.js on Ubuntu
comments: true
tags: [node.js, ubuntu, npm]
---

Here is a quick and easy way to install the latest version of Node.js on an Ubuntu system. As with many things in the JavaScript world there is more than one way to do this, but for getting up-and-going quickly I like using `n`.

First, make sure you have `npm` installed:

<pre class="prettyprint">
  <code class="lang-bsh">
    $ sudo apt-get update
    $ sudo apt-get install npm
  </code>
</pre>

Then it is just a simple matter of globally installing `n`:

<pre class="prettyprint">
  <code class="lang-bsh">
    $ sudo npm i n -g
  </code>
</pre>

Once `n` is installed you can install the latest version of Node.js with the following command:

<pre class="prettyprint">
  <code class="lang-bsh">
    $ sudo n latest
  </code>
</pre>

Or, if you prefer the stable version use this:

<pre class="prettyprint">
  <code class="lang-bsh">
    $ sudo n stable
  </code>
</pre>

Super simple. You can read more about `n` [here](https://github.com/tj/n)
