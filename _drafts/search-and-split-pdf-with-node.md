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


