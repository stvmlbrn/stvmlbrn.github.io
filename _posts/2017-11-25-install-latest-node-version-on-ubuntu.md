---
layout: post
title: How To Install The Latest Version of Node.js on Ubuntu
comments: true
---

Here is a quick and easy way to install the latest version of Node.js on a Ubuntu system.

With many things in the JavaScript world, there is more than one way to do this, but I prefer using [n](https://www.npm).

First, make sure you have `npm` installed:

<pre class="prettyprint lang-bsh">
$ sudo apt-get update
$ sudo apt-get install npm
</pre>

Then it is just a simple matter of globally installing `n`:

<pre class="prettyprint lang-bsh">
$ sudo npm i n -g
</pre>

Once `n` is installed you can install the latest version of Node.js with the following command:

<pre class="prettyprint lang-bsh">
$ sudo n latest
</pre>

Or, if you prefer the stable version use this:

<pre class="prettyprint lang-bsh">
$ sudo n stable
</pre>

Super simple. You can read more about `n` [here](https://github.com/tj/n)
