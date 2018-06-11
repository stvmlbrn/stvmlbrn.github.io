---
layout: post
title: How To Upload Files With React To A Node/Express Server
comments: true
tags: [react, node.js, express]
---

Handling file uploads in a React/Node/Express application is done a little different on both the server-side and client-side than what you may be used to doing when handling regular form submissions. In this post we'll take a look at code on the server and client for uploading a file from a React application and saving the file on the server using Node.js and Express.

<h2 class="heading">The Server</h2>
Let's start by looking at the server. The code below should look familiar as a simple Express server that handles a single POST operation using the popular body parsing middleware `body-parser`.

<pre class="prettyprint">
  <code class="lang-js">
    const port = process.env.PORT || 3000;
    const express = require('express');
    const bodyParser = require('body-parser');

    const app = express();

    app.use(bodyParser.json());
    app.use(bodyParser.urlencoded({ extended: true }));

    app.post('/', (req, res) => {
      /*
      Handle the form submission here. By using the body-parser
      middleware the submitted data is available in req.body.
      */
      res.send();
    });

    app.listen(port, () => console.log(`Server listening on port ${port}`));
  </code>
</pre>

Pretty much every Node.js / Express tutorial on how to handle POST requests does something along the lines of the code above using the `body-parser` middleware. Unfortunately, and what may cause some confusion when learning how to handle file uploads, `body-parser` does not handle _multipart/form-data_ bodies which are used when uploading files. So as useful as the `body-parser` middleware is, it will not help us handle the file upload. For that part we have to turn to something else - I like to use `multer`.

`multer` is middleware designed specifically to handle _multipart/form-data_ bodies and works similar to `body-parser`. In fact, just like `body-parser`, `multer` add a _body_ object to the request for data from text fields, select boxes, etc. But, unlike `body-parser`, it also adds a _file_ or _files_ object that contains the files uploaded from the client.

There are 2 options available in `multer` to determine how we want to save the files. We can save directly to disk, or keep the files in memory (useful for things such as streaming to AWS S3, for example). In this tutorial we'll use disk storage.

First, let's make sure we have `multer` installed. Also, it's a good practice to rename files after upload for security purposes. A random-generated ID works well for this, so let's install the `uuid` package too:

<pre class="prettyprint">
  <code class="lang-bsh">
    $ npm i multer uuid
  </code>
</pre>

Now add these new modules to the Express configuration, along with `path` which will be used for some file manipulation tasks.

<pre class="prettyprint">
  <code class="lang-js">
    const multer = require('multer');
    const uuidv4 = require('uuid/v4');
    const path = require('path');
  </code>
</pre>

Since we are using disk storage we need to define how we want that to work by specifying the _destination()_ and _filename()_ functions in the _multer.diskStorage_ configuration. Both of these functions take the request object, file object, and a callback function as arguments. By default, `multer` does not supply a file extension when saving the file, so we need to make sure we do that ourselves in the _filename()_ function we define.

<pre class="prettyprint">
  <code class="lang-js">
    // configure storage
    const storage = multer.diskStorage({
      destination: (req, file, cb) => {
        /*
          Files will be saved in the 'uploads' directory. Make
          sure this directory already exists!
        */
        cb(null, './uploads');
      },
      filename: (req, file, cb) => {
        /*
          uuidv4() will generate a random ID that we'll use for the
          new filename. We use path.extname() to get
          the extension from the original file name and add that to the new
          generated ID. These combined will create the file name used
          to save the file on the server and will be available as
          req.file.pathname in the router handler.
        */
        const newFilename = `${uuidv4()}${path.extname(file.originalname)}`;
        cb(null, newFilename);
      },
    });
    // create the multer instance that will be used to upload/save the file
    const upload = multer({ storage });
  </code>
</pre>

The final step is to actually process the uploaded file. We need to modify the POST route to include the `multer` middleware instance we created in the previous step.

<pre class="prettyprint">
  <code class="lang-js">
    app.post('/', upload.single('selectedFile'), (req, res) => {
      /*
        We now have a new req.file object here. At this point the file has been saved
        and the req.file.filename value will be the name returned by the
        filename() function defined in the diskStorage configuration. Other form fields
        are available here in req.body.
      */
      res.send();
    });
  </code>
</pre>

The 'selectedFile' argument passed to the _upload.single()_ function should match the name of the file field being sent as part of the form submission. Any other form data, such as text inputs, checkbox values, etc., is available in _req.body_ just like when using `body-parser`.

Putting it all together, the completed server code looks like this:

<pre class="prettyprint">
  <code class="lang-js">
    const port = process.env.PORT || 3000;
    const express = require('express');
    const bodyParser = require('body-parser');
    const multer = require('multer');
    const uuidv4 = require('uuid/v4');
    const path = require('path');

    // configure storage
    const storage = multer.diskStorage({
      destination: (req, file, cb) => {
        /*
          Files will be saved in the 'uploads' directory. Make
          sure this directory already exists!
        */
        cb(null, './uploads');
      },
      filename: (req, file, cb) => {
        /*
          uuidv4() will generate a random ID that we'll use for the
          new filename. We use path.extname() to get
          the extension from the original file name and add that to the new
          generated ID. These combined will create the file name used
          to save the file on the server and will be available as
          req.file.pathname in the router handler.
        */
        const newFilename = `${uuidv4()}${path.extname(file.originalname)}`;
        cb(null, newFilename);
      },
    });
    // create the multer instance that will be used to upload/save the file
    const upload = multer({ storage });

    const app = express();

    app.use(bodyParser.json());
    app.use(bodyParser.urlencoded({ extended: true }));

    app.post('/', upload.single('selectedFile'), (req, res) => {
      /*
        We now have a new req.file object here. At this point the file has been saved
        and the req.file.filename value will be the name returned by the
        filename() function defined in the diskStorage configuration. Other form fields
        are available here in req.body.
      */
      res.send();
    });

    app.listen(port, () => console.log(`Server listening on port ${port}`));
  </code>
</pre>

There is one last thing worth noting before we move on the client side. Notice that `body-parser` is still included, even though we are not using it. I left it there to illustrate that `body-parser` and `multer` can exist side-by-side in the same application, each serving a different purpose. If your application only needs to handle _multipart/form-data_ form submissions, you can completely remove `body-parser`. However, most applications will need to handle different types of forms and you may need both packages. Just know that `body-parser` and `multer` can co-exist, and each has their own purpose.

<h2 class="heading">The Client</h2>

If you are not familiar with how to process and submit form data in React you should probably take the time to read [my post on this topic]({{site.url}}/2017/04/07/submitting-form-data-with-react.html) before going any farther. This covers the basics of how to store field values in the state and use `axios` to submit the data to the server. I am going to take the same general component used in that post (an example of a user registration form) and modify it to allow the user to select a file and add a file description. We'll need to make a few changes to how we get form values into the component state, and how to format the form data prior to sending it to the server.

Before we get into handling input changes and submission, here is the basic React component we'll be working with:

<pre class="prettyprint">
  <code class="lang-js">
    import React, { Component } from 'react';
    import axios from 'axios';

    class UserForm extends Component {
      constructor() {
        super();
        this.state = {
          description: '',
          selectedFile: '',
        };
      }

      onChange = (e) =&gt; {
        // event to update state when form inputs change
      }

      onSubmit = (e) =&gt; {
        e.preventDefault();
        // event to submit the data to the server
      }

      render() {
        const { description, selectedFile } = this.state;
        return (
          &lt;form onSubmit={this.onSubmit}&gt;
            &lt;input
              type="text"
              name="description"
              value={description}
              onChange={this.onChange}
            /&gt;
            &lt;input
              type="file"
              name="selectedFile"
              onChange={this.onChange}
            /&gt;
            &lt;button type="submit"&gt;Submit&lt;/button&gt;
          &lt;/form&gt;
        );
      }
    }
  </code>
</pre>

So with that in place, let's get to work on the _onChange()_ handler. Values in file fields have some different properties than other form inputs such as text, select, checkbox, etc. For that reason, we can't just assign the value of the file field to the state variable like we do with other inputs (again, if this doesn't make sense please look at my other post). So we need to check if the input being changed is the file input or something else and act accordingly.

<pre class="prettyprint">
  <code class="language-javascript">
    onChange = (e) => {

      switch (e.target.name) {
        case 'selectedFile':
          this.setState({ selectedFile: e.target.files[0] });
          break;
        default:
          this.setState({ [e.target.name]: e.target.value });
      }
    }
  </code>
</pre>

Finally, let's finish up with the _onSubmit()_ function. As we talked about in the server section the request needs to be sent as _multipart/form-data_, for that we can create a new _FormData()_ object and append our field values.

<pre class="prettyprint">
  <code class="lang-js">
    onSubmit = (e) => {
      e.preventDefault();
      const { description, selectedFile } = this.state;
      let formData = new FormData();

      formData.append('description', description);
      formData.append('selectedFile', selectedFile);

      axios.post('/', formData)
        .then((result) => {
          // access results...
        });
    }
  </code>
</pre>

Putting it all together, the complete client-side code now looks like this:

<pre class="prettyprint">
  <code class="lang-js">
    import React, { Component } from 'react';
    import axios from 'axios';

    class UserForm extends Component {
      constructor() {
        super();
        this.state = {
          description: '',
          selectedFile: '',
        };
      }

      onChange = (e) =&gt; {
        const state = this.state;

        switch (e.target.name) {
          case 'selectedFile':
            this.setState({ selectedFile: e.target.files[0] });
            break;
          default:
            this.setState({ [e.target.name]: e.target.value });
        }
      }

      onSubmit = (e) =&gt; {
        e.preventDefault();
        const { description, selectedFile } = this.state;
        let formData = new FormData();

        formData.append('description', description);
        formData.append('selectedFile', selectedFile);

        axios.post('/', formData)
          .then((result) => {
            // access results...
          });
      }

      render() {
        const { description, selectedFile } = this.state;
        return (
          &lt;form onSubmit={this.onSubmit}&gt;
            &lt;input
              type="text"
              name="description"
              value={description}
              onChange={this.onChange}
            /&gt;
            &lt;input
              type="file"
              name="selectedFile"
              onChange={this.onChange}
            /&gt;
            &lt;button type="submit"&gt;Submit&lt;/button&gt;
          &lt;/form&gt;
        );
      }
    }
  </code>
</pre>



