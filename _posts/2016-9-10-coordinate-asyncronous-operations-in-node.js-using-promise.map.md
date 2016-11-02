---
layout: post
title: Coordinate Asyncronous Operations in Node.js Using Promise.map()
---

Using promises is my (current) preferred method of managing async operations in Node.js. Recently I got hung-up on how to most effectively leverage promises to navigate through a series steps when one of the steps itself required an indeterminate number of asyncronous operations to be performed before the overall process could advance.

For example, we can use promises to step through a sequence of asyncronous operation 1 -> 2 -> 3 with each step returning a promise to the next step. But what if step 2 required us to perform multiple asyncronous operations (each generating it's own promise) before we can proceed to step 3?

See the example problem below where we fetch a number of records from a database in step 1 and then need to perform another query for each record in step 2 and merge the results of step 1 and step 2 into an file before moving to step 3 where we need to ftp this file to a remote location.

{% highlight javascript linenos %}
var Promise = require('bluebird');
var Sequelize = require('sequelize');
var path = require('path');
var fs = require('fs');

var sequelize = new Sequelize(<database>, <username>, <password>, {
    host: <db_host>,
    logging: false
});
var filename = path.join(__dirname, 'output.csv');
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
        /*
        Step 3 - ftp the file to a remote location, but only after
        all operations in step 2 are done.
        */
    })
    .catch(err => console.log(err));
{% endhighlight %}


[Bluebird](http://bluebirdjs.com/docs/getting-started.html) is a wonderful promise library and provides several methods of handling multiple operations, each with their own advantages and disadvantages. In this particular scenario, `Promise.map()` is a nice tool for the job.

As stated in the [Bluebird documentation](http://bluebirdjs.com/docs/api/promise.map.html), the `.map()` function allows us to:
> iterate over all the values in the Iterable into an array and map the array to another using the given mapper function.

In our example, the Iterable is going to be the array of records returned from step 1, and our mapper function is going to a execute query for each individual record with each query returning it's own promise back to `Promise.map()`, which in turn will return a single promise to step 3 when all mapper functions are complete.

{% highlight javascript linenos %}
var Promise = require('bluebird');
var Sequelize = require('sequelize');
var path = require('path');
var fs = require('fs');

var sequelize = new Sequelize(<database>, <username>, <password>, {
    host: <db_host>,
    logging: false
});
var filename = path.join(__dirname, 'output.csv');
var sql = 'select * from table1';

//Step 1 - fetch the records from the db
sequelize.query(sql, {type: sequelize.QueryTypes.SELECT})
    .then(results => {
      //loop through `results` and return a single promise to step 3
      return Promise.map(results, (result) => {
        let line = []; //will hold our data that needs written to a file
        let sql2 = 'select * from table2 where table2.id = :id'; //query to be performed from each `result`
        //our mapper function needs to return a promise!
        return sequelize.query(sql2, {replacements: {id: result.id}, type: sequelize.query.SELECT})
          .then(result2 => {
            //we now have access to `result` and `result2` and can now stage data that will be written to a file
            line.push(result.field1);
            line.push(result.field2);
            line.push(result2.field1);
            line.push(result2.field2);
            //write the data to the file
            fs.appendFileSync(filename, '\n' + line.join(','));
          });
      });
    })
    .then(() => {
      /*
      At this point all queries are done and results are written to file. We
      can safely perform our ftp upload here. Actual code for
      the ftp transfer is not relevent.
      */
    })
    .catch(err => console.log(err));
{% endhighlight %}

Done! As you can see, line 21 will return a promise for each record in the results from step 1. However, the outer `Promise.map()` function will make sure that all those promises are fulfilled before moving to step 3. Perfect.

Read the [complete documentation](http://bluebirdjs.com/docs/api/promise.map.html) on the Bluebird website.
