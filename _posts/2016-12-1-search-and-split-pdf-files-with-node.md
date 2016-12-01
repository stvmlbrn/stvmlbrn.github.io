---
layout: post
title: Searching and Splitting PDF Files With Node.js
comments: true
---

So much of my work is web development related that when something different comes along I can geek out on it pretty hard. It's even better
when it's an opportunity to save our organization money. A recent need for a command line script to search and split a PDF file satisfied both.

Here was the scenario: I had a roughly 2000 page PDF that contained tax information for our employees. Each page of the PDF was specific to a particular employee,
and the text on each page included the employee's social security number. The task was to extract each page from the source PDF, match it with the correct
employee and distribute it.

Oh, and the tax preparer that generated the PDF wanted over $7,000 to accomplish basically the same thing!

I found 2 useful packages to help in the process:

* **[pdf-text-extract](https://www.npmjs.com/package/pdf-text-extract)** for searching the contents of the source PDF
* **[hummus](https://www.npmjs.com/package/hummus)** for splitting the source PDF into individual files.

Here is the entire script:

<pre class="javascript">
    var extract = require('pdf-text-extract');
    var hummus = require('hummus');
    var path = require('path');
    var fs = require('fs');
    var readline = require('readline');

    var sourcePDF = path.join(__dirname, 'source.pdf');
    var outputFolder = path.join(__dirname, '/output');
    var employees = [];

    //delete any files that already exist in the output folder
    fs.readdirSync(outputFolder).filter((file) => {
      fs.unlinkSync(path.join(outputFolder, file));
    });

    /*
    Need to load up employee SSNs pulled out of the payroll system.
    Each line in the file is in the following format: LastName,FirstName,MI,EmployeeID,SSN
    */
    var lineReader = readline.createInterface({
      input: fs.createReadStream('employees.txt')
    });

    lineReader.on('line', (line) => {
      employees.push(line);
    });

    lineReader.on('close', () => {
      /*
      Now that all employees are loaded into the employees[] array, we can begin breaking apart
      the source PDF to make it easier to search. The pdf-text-extract package will
      load each page of the PDF into an array item.
      */
      extract(sourcePDF, (err, pages) => {  //pages will be an array of strings. Each item corresponds to a page in the PDF
        if (err) console.log(err);

        /*
        We can now run through the employees[] array, and for each employee we'll look for a matching
        ssn in the pages[] array created by the extract() function.
        */
        employees.map(emp => {
          let ssn = emp.split(',')[4];

          //use a for-loop to make it easier to break out after a match is found
          for (let i = 0; i < pages.length; i++) {
            if (pages[i].indexOf(ssn) !== -1) { //found it!
              /*
              Now use the hummus package to pull that page out into it's own document. We'll use
              the employee ssn as the document name for now.
              */
              var pdfWriter = hummus.createWriter(path.join(outputFolder, `${ssn}.pdf`));
              pdfWriter.appendPDFPagesFromPDF(sourcePDF, {type:hummus.eRangeTypeSpecific,specificRanges: [ [ i,i ] ]});
              pdfWriter.end();
              break; //no need to keep searching
            }
          }
        });
      });
    });
</pre>


You may have noticed that we are using the index in the ```pages[]``` array to match a corresponding page number when using the ```pdfWriter```. Considering
that arrays are zero-based and pages numbers are one-based, this seems weird, right? Don't worry, that is covered in the [hummus documentation](https://github.com/galkahana/HummusJS/wiki/Embedding-pdf)

Nested loops like we have here is not something I'm crazy about, so it's tempting to try to optimize this a little bit. My first thought was to remove the item in ```pages[]``` after a match was found.
That way, when we move on to the next employee there are fewer pages we need to search through in finding a matching ssn. However, this was a bad idea. Once we start
removing items from ```pages[]```, then the index of that array no longer matches the original page number in the source PDF, causing the wrong page to be extracted. Oh well,
performance here isn't of the upmost importance anyway. And frankly the disk I/O of working with the PDF files is going to be the drag on performance, not the nested loop (especially with only
a few thousand records).

So that's it. We just took a nearly 2000 page PDF document an extracted each page into it's own PDF that corresponds to each employee. It probably
took me longer to write the blog post than to write the actual program, and it saved us $7,000!

