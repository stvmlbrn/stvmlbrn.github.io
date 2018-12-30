---
layout: post
title: Create CSV Files With Node.js
comments: true
tags: [node.js]
icon: node.png
---

More often than I care for I find myself needing to write data to a csv file. This certainly falls under the "less than interesting" category of things I work on, but you have to take the good with the bad, I guess.

There are a number of pre-existing packages that can be used to generate CSV files that may be a perfect fit for you rather than the technique described in this post. If you have JSON data that you need to write out to a CSV, check out `json2csv` [(https://www.npmjs.com/package/json2csv)](https://www.npmjs.com/package/json2csv) as it may make life easier. Unfortunately for me, while my data source is almost always JSON (most often fetched from a database), there is  always some conditional logic that I need to perform on the fields before they get written to the file. I could perform this logic directly on the JSON data and still use a package such as `json2csv`, but in these situations I tend to prefer building the CSV manually.

The method I use is to have 2 arrays, `row[]` and `output[]`. Each column of data in a particular row will be pushed to `row[]`. After all data for the current row has been added it's joined with a comma and pushed to `output[]` array. The process is repeated until all rows have been added to `output[]`. Then join `output[]` with an end-of-line character to split it into multiple lines, and write it to the CSV file.

<pre>
  <code class="lang-js">
    const fs = require('fs');
    const path = require('path');
    const os = require('os');

    // output file in the same folder
    const filename = path.join(__dirname, 'output.csv');
    const output = []; // holds all rows of data

    // example JSON data
    const data = [
      {
        state: 'Maryland',
        capital: 'Annapolis',
        population: 38394
      },
      {
        state: 'New York',
        capital: 'Albany',
        population: 97856
      },
      {
        state: 'New Mexico',
        capital: 'Santa Fe',
        population: 75764
      },
    ];

    data.forEach((d) => {
      const row = []; // a new array for each row of data
      row.push(d.state);
      row.push(d.capital);
      row.push(d.population);

      output.push(row.join()); // by default, join() uses a ','
    });

    fs.writeFileSync(filename, output.join(os.EOL));
  </code>
</pre>

So this is obviously a bit of a contrived example, and I've spared you from the details about including conditional processing or deeply nested JSON values that sometimes complicate this, but I think this is enough to give you an idea if you looking for ways to generate a csv file.

Also worth mentioning here is the possibility of having comma characters inside your data values, which throws off the columns in the resulting csv file. As such, it's normally good practice to wrap your data values inside quotes. This is sometimes referred to as a <em>comma-quoted</em> csv file. To implement this, our .forEach() loop would look like this:

<pre>
  <code class="lang-js">
    data.forEach((d) => {
      const row = []; // a new array for each row of data
      row.push(`"${d.state}"`);
      row.push(`"${d.capital}"`);
      row.push(`"${d.population}"`);

      output.push(row.join()); // by default, join() uses a ','
    });
  </code>
</pre>

One last tip - if you are using Visual Studio Code there is a nice plugin available for view csv files called [Rainbow CSV](https://marketplace.visualstudio.com/items?itemName=mechatroner.rainbow-csv) that is worth checking out.





