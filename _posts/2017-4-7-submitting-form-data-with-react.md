---
layout: post
title: Submitting Form Data With React
comments: true
---

I was reading through a thread on Reddit recently
with some users discussing React. One particular user seemed a little frustrated with his/her
understanding of React and had a few specific complaints about form data and submissions. I decided
to put together an example addressing each of the issues (s)he is having.

#### "Getting a value from a form or user input seems insanely complicated"

This is an example of where the adage "thinking in React" really applies. It's best not to think
in terms of "getting data from a form or user input". You get your data from your "state". So during
development the task you should be thinking about is "how do I put the value of the user input into
my state". To do so, use the `onChange()` event handler on the input fields. Here
is a simple example:

<pre class="prettyprint">
  <code class="language-javascript">
    import React, { Component } from 'react';

    class UserForm extends Component {
      constructor() {
        super();
        this.state = {
          fname: '',
          lname: '',
          email: '',
        };
      }

      onChange = (e) =&gt; {
        // Because we named the inputs to match their corresponding values in state, it's
        // super easy to update the state
        const state = this.state
        state[e.target.name] = e.target.value;
        this.setState(state);
      }

      render() {
        const { fname, lname, email } = this.state;
        return (
          &lt;form&gt;
            &lt;input type="text" name="fname" value={fname} onChange={this.onChange} /&gt;
            &lt;input type="text" name="lname" value={lname} onChange={this.onChange} /&gt;
            &lt;input type="text" name="email" value={email} onChange={this.onChange} /&gt;
          &lt;/form&gt;
        );
      }
    }
  </code>
</pre>

So when I said earlier that "you get your data from your state" it should be understood that _all data_
comes from your state. The form inputs can be thought of as not actually having data, they are merely
reflecting data that is stored in your state. Any changes to the input is handled by the `onChange()`
handler, which updates the corresponding values in the state, which is in turn reflected in the input value.

This may require a few more lines of code than what some developers may be used to if they are using
other libraries like jQuery to gather and submit form data. However, as applications grow and become more complex, having your data centralized in your state becomes really handy.

#### "No tutorials seem to go over anything like submitting form data"

After gaining an understanding of how and why we keep our form data in state. I think it will
make perfect sense how we want to handle the form submission. In reality, it's not that much different than
what your probably used to doing.

Working from our example above, let's create an `onSubmit()` function and attach that to the form. I like
to use [Axios](https://www.npmjs.com/package/axios) but there are any number of tools that are suitable for this, such as SuperAgent, Fetch, and of couse, jQuery.

<pre class="prettyprint">
  <code class="language-javascript">
    import React, { Component } from 'react';
    import axios from 'axios';

    class UserForm extends Component {
      constructor() {
        super();
        this.state = {
          fname: '',
          lname: '',
          email: '',
        };
      }

      onChange = (e) =&gt; {
        // Because we named the inputs to match their corresponding values in state, it's
        // super easy to update the state
        const state = this.state
        state[e.target.name] = e.target.value;
        this.setState(state);
      }

      onSubmit = (e) =&gt; {
        e.preventDefault();
        // get our form data out of state
        const { fname, lname, email } = this.state;

        axios.post('/', { fname, lname, email })
          .then((result) => {
            //access the results here....
          });
      }

      render() {
        const { fname, lname, email } = this.state;
        return (
          &lt;form onSubmit={this.onSubmit}&gt;
            &lt;input type="text" name="fname" value={fname} onChange={this.onChange} /&gt;
            &lt;input type="text" name="lname" value={lname} onChange={this.onChange} /&gt;
            &lt;input type="text" name="email" value={email} onChange={this.onChange} /&gt;
            &lt;button type="submit"&gt;Submit&lt;/button&gt;
          &lt;/form&gt;
        );
      }
    }
  </code>
</pre>

And that's pretty much it. Again, the only difference here is that we are getting the
data to use in the form submission from the state, not the form fields.


