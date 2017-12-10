---
layout: post
title: Performing 'like' Queries Using the MySQL Package in Node.js
comments: true
tags: [node.js, mysql]
---

The [MySQL package](https://www.npmjs.com/package/mysql) is a hugely popular library for working with MySQL from Node.js, and with good reason. It's performant, easy to use,
battle tested, and well documented. I was a bit surprised, however, that the documentation doesn't show the syntax for performing parameterized 'like' queries.

And we all use parameterized queries, right? Have we learned our lesson from [Little Bobby Tables](https://xkcd.com/327/)? In all seriousness, if the reasons why we want to use parameterized
queries are unclear, research 'SQL injection attack' for further understanding.

The package documentation show us that in order to parameterize a query we would want to do something like this (updated a bit for ES6 syntax):

<pre class="prettyprint">
  <code class="language-javascript">
    var sql = 'select * from users where lastname = ?';
    connection.query(sql, [lname], ((err, result) => {
      ...
    });
  </code>
</pre>

If our ```lname``` variable is equal to 'smith', this query will return all users in the database who have a last name of 'Smith'. But suppose we also wanted people named 'Smithers', 'Smitherson' and 'Hammersmith' - how do
we adjust our syntax to use a ```like``` query? Have a look:

<pre class="prettyprint">
  <code class="language-javascript">
    var sql = 'select * from users where lastname like ?';
    connection.query(sql, [`%${lname}%`], ((err, result) => {
      ...
    });
  </code>
</pre>

We first changed our sql statement to use `like` instead of `=`. Then we surround our ```lname``` variable with `%` symbols to match any number of characters. Simple - maybe that's why it's not
included in the documentation to begin with 🤔

One additional thing worth noting here is how the use of [ES6 template literals](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Template_literals) makes this so much nicer. Prior to ES6 we'd have to
build a string by concatenating things together like so: ```'%' + lname + '%'```. Yuck. And with a large number of parameters things can get really ugly and hard to read in a hurry.
Thankfully we can now use template literals to make this so much nicer.
