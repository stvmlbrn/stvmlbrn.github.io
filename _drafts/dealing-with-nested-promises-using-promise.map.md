---
layout: post
title: Dealing With Nested Promises Using Promise.map()
---

Using promises is my (current) preferred method of managing async operations in Node.js. Recently I got hung-up on how to manage leverage promises to navigate through a series steps when one of the steps itself required an indeterminate number of asyncronous operations to be performed before the overall process could advance.

For example, we can use promises to step through a sequence of asyncronous operation 1 -> 2 -> 3 with each step returning a promise to the next step. But what if step 2 required us to perform multiple asyncronous operations (each generating it's own promise) before we can proceed to step 3?


{% highlight javascript linenos %}
var Promise = require('bluebird');
var Sequelize = require('sequelize');

var sequelize = new Sequelize(<database>, <username>, <password>, {
    host: <db_host>,
    logging: false
});
var sql = 'select * from table1';

//Step 1 - fetch the records from the db
sequelize.query(sql, {type: sequelize.QueryTypes.SELECT})
    .then(results => {
        /*
        Step 2 - loop through each record and run another query
        on a different table (assume the tables can not be joined in
        the first query). This is where the problem is - how can we
        make sure all the queries are done before we advance to step 3?
        We can't results.map() since each iteration would end up returning
        its own promise, and we'd advance to step 3 once the first one
        was complete. We need to perform all of these operations and 
        only return a single promise.
        */
    })
    .then(() => {
        //Step 3 - write the data to a file
    })
    .catch(err => console.log(err));
{% endhighlight %}

On solution is to use Promise.all(), but 