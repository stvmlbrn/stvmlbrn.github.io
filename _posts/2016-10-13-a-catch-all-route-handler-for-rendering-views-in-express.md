---
layout: post
title: A Dynamic Catch-All Route Handler For Rendering Views in Express.js
comments: true
tags: [node.js, express]
---

I was recently working on a demo project and found myself constantly creating and deleting views. Rather than having to keep updating my routes I wondered if I could create a dynamic route handler that would match a view with a requested route.

Fortunately, it's quite easy to do. We need to add our dynamic catch-all route handler *after* all other routes in our application have been defined, such as API endpoints and views that require specific data injected into them, but *before* our 404 handler. Let's look at an example:

<pre class="prettyprint">
  <code class="lang-js">
    var express = require('express');
    var app = express();
    var fs = require('fs');
    var path = require('path');
    var port = process.env.PORT || 3000;

    var viewFolder = path.join(__dirname, 'views');

    app.set('views', viewFolder);
    app.set('view engine', 'pug');

    /*
    All routes that should not be handled by the catch-all go here.
    */

    //now define the catch-all view render route
    app.use((req, res, next) => {
      let requestedView = path.parse(req.path);
      let filePath = path.join(viewFolder, requestedView.dir, requestedView.name) + '.pug';

      if (fs.existsSync(filePath)) {
        let viewPath;
        if (requestedView.dir === '/') { //view is in the root of the views directory
          viewPath = requestedView.name;
        } else { //need to include the subfolder(s) in the path to the requested view
          //using slice() to remove the leading '/' from the directory path
          viewPath = `${requestedView.dir.slice(1)}/${requestedView.name}`;
        }
        //should now have the correct path to the view, go ahead and render() it
        res.render(viewPath);
      } else {
        next(); //important in order to display the 404 handler if the view was not found
      }
    });

    //the 404 route handler should always be last!
    app.use((req, res, next) => {
      res.render('404');
    });

    app.listen(port, () => console.log(`App running on port ${port}`));
  </code>
</pre>

The overall process here is pretty simple. First we parse the path that was requested by the browser `req.path` and use that to build a path to where the file should exist in our file system, `filePath`. If that file exists, render it. If not proceed to the 404 handler.

This code is good enough for our demo, but there are a couple of things we should probably add to this if we ever wanted to use something like this in production. First, if your file system is case-sensitive you may want to take steps to ensure the requested view matches your file system (keep all files/folders in lower case and run `.toLowerCase()` on `req.path` should do the trick). Second, we don't really want to check the file system every time for views that we should already know exist. It may be a good idea to keep an array of views that were already found and check the contents of that array for matches before checking the file system (or something similar to keep known routes in memory).


