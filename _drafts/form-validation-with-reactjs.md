---
layout: post
title: An Example of Form Validation With React.js
comments: true
---

As I first started to work with React and familiarize myself with it's one-way data bindings, I found
that I must re-think some of the ways I've solved seemingly trivial problems before. One such issue
is dealing with client-side form validation. There are no shortages of ways and opinions on how to do this,
and numerous packages exists to solve this very problem (with, IMHO varying degrees of success). However,
as I head down the path of learning something new I don't mind reinventing the wheel, so to speak, as
it often helps the learning process. As such, this is a process I came up with for form validation
that I still use today.

While some of this is may be obvious, there are a few objectives I have when performing client-side validation
that not all existing packages allow (though some do):
* Apply multiple validation checks against an input
* Be able to customize the error message based on the validation criteria that failed
* Easily clear all error messages and applied CSS to form controls

So let's take a look at an example sign-up form that asks for an email address, a password, and to
confirm the password. Here is how we might generate such a form using React:

<pre class="prettyprint">
  <code class="language-javascript">
    import React from 'react';
  </code>
</pre>


