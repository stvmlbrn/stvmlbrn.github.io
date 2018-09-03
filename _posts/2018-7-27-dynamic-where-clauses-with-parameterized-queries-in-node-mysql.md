---
layout: post
title: Dynamic 'Where' Clauses With Parameterized Queries in Node.js and MySQL
comments: true
tags: [node.js, mysql, database]
icon: database.png
---

Occassionally when writing SQL queries the number of fields that we need to filter on may change depending on user selection. Let's take a look at how we can build dynamic 'where' clauses and the corresponding parameter array to handle this scenario.

For this example, imagine an application that allows users to filter car types based on certain criteria such as make, model, and color. Our search function may look something like this:

<pre class="prettyprint">
  <code class="lang-js">
    const search = (make, model, color) => {
      const sql = `select * from cars
        where make = ? and model = ? and color = ?`;
      connection.query(sql, [make, model, color], (error, results, fields) => {
          // handle results here...
        }
      );
    };
  </code>
</pre>

If you are familiar with the `mysql` package ([https://www.npmjs.com/package/mysql](https://www.npmjs.com/package/mysql)) for Node this should look familiar to you. If not, you may want to familiarize yourself in order to understand what is going on here.

So, now imagine that our user has the option to select 'all' for any of the options. For example, they may want to see any Ford F150 trucks, but don't care what color it is. The select box on the front-end may have an 'All Colors' selection that, rather than passing a particular color to the search API, it will simply pass that value 'all'. The same thing can happen for 'make' and 'model' selections too.

In order to accommodate this in the search API, we'll need to do 2 things - (1) detect which values we want to query the database on and build the SQL accordingly, and (2) make sure we correctly pass the values the parameters array to match the placeholders in the SQL statement.

One trick I like to use in these types of situations is to start the SQL statement with a 'where' clause that will match all records. The reason for this is simple - when it's determined that we need to append a 'where' clause because the user make a specific selection, we do not have to detect whether or not a 'where' clause already exists, which makes building the SQL string much easier. For example:

<pre class="prettyprint">
  <code class="lang-js">
    const sql = 'select * from cars where 1 = 1';
  </code>
</pre>

That may seem a little weird at first, but it's handy. Since `1 = 1` will alway be true, no records are acually excluded from the database. This statement is functionally the same as

<pre class="prettyprint">
  <code class="lang-js">
    const sql = 'select * from cars';
  </code>
</pre>

But now when we need to add a filter based on 'color', for example, we do not have to check to see if a 'where' has already been added, and can simply start appending additional clauses with 'and' statements. At the same time we do this, we'll also want to add the value of the filter to a paramaters array - this will ensure our parameters array only includes values we want to filter on, and appear in the same order as their matching placeholders in the SQL string.

Let put it all together:

<pre class="prettyprint">
  <code class="lang-js">
    const search = (make, model, color) => {
      const params = [];
      let sql = 'select * from cars where 1 = 1';

      if (make !== 'all') {
        sql += ' and make = ?';
        params.push(make);
      }
      if (model !== 'all') {
        sql += ' and model = ?';
        params.push(model);
      }
      if (color !== 'all') {
        sql += ' and color = ?';
        params.push(color);
      }

      connection.query(sql, params, (error, results, fields) => {
          // handle results here...
        }
      );
    };
  </code>
</pre>
