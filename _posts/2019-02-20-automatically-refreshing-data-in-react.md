---
layout: post
title: Automatically Refreshing Data In A React Application
comments: true
tags: [react, javascript]
icon: react.png
---

When we need to refresh data without user interaction in a web-based interface we have a few different options to consider. The ideal solution is likely to be implementing a solution based on the [the websocket API](https://developer.mozilla.org/en-US/docs/Web/API/WebSockets_API) as it provides more realtime updates with, perhaps, less overhead. Other solutions include standard JavaScript methods such as `setInterval()` and `setTimeout()`.
These are the methods we'll look at in this post.

At first glance, the `setInterval()` method looks like the proper fit for this scenario as it's designed to run a set of instructions at a specific interval, whereas `setTimeout()` is designed to run a set of instructions only once after a specified delay. Regardless, `setTimeout()` is probably the more common choice to implement in this situation (we'll look at how in a little bit), but there is nothing wrong with using `setInterval()`. With either approach there are extra considerations we need to keep in mind when working in a React application (or any other single-page application, for that matter).

Let's look at a basic example using `setInterval()`. The need for refreshing data is common among dashboard-type applications, so we'll call our component 'Dashboard':

<pre>
  <code class="language-javascript">
    import React, { Component } from 'react';

    class Dashboard extends Component {
      componentDidMount() {
        // need to make the initial call to getData() to populate
        // data right away
        this.getData();

        // Now we need to make it run at a specified interval
        setInterval(this.getData, 5000); // runs every 5 seconds.
      }

      getData = () =&gt; {
        // do something to fetch data from a remote API.
      }

      render() {
        return (
          &lt;div>
            Our fancy dashboard lives here.
          &lt;/div>
        );
      }
    }
  </code>
</pre>

Looks pretty simple, right? Unfortunately there are a few problems here. First, the `getData()` method is going to lose the proper `this` context on subsequent calls (which will cause anything like _setState()_ to fail), so we need to bind it appropriately inside `setInterval()`. Secondly, and here is where we run into an unusual situation due to working with single-page applications, is that `getData()` will be called every 5 seconds even if the user navigates to a different page. The reason for this is because `setInterval()` is never actually removed. And this is an obvious problem since we don't want to needlessly continue to run what is most likely an expensive operation in the background as the user navigates around the application.

Fortunately, `setInterval()` returns a value when called that can be used to identify and clear the interval at a later time using `clearInterval()`. This is exactly what we need to prevent needlessly running background operations.

Here is the updated component:

<pre>
  <code class="language-javascript">
    import React, { Component } from 'react';

    class Dashboard extends Component {
      /*
        declare a member variable to hold the interval ID
        that we can reference later.
      */
      intervalID;

      componentDidMount() {
        /*
          need to make the initial call to getData() to populate
         data right away
        */
        this.getData();

        /*
          Now we need to make it run at a specified interval,
          bind the getData() call to `this`, and keep a reference
          to the invterval so we can clear it later.
        */
        this.intervalID = setInterval(this.getData.bind(this), 5000);
      }

      componentWillUnmount() {
        /*
          stop getData() from continuing to run even
          after unmounting this component
        */
        clearInterval(this.intervalID);
      }

      getData = () =&gt; {
        // do something to fetch data from a remote API.
      }

      render() {
        return (
          &lt;div>
            Our fancy dashboard lives here.
          &lt;/div>
        );
      }
    }
  </code>
</pre>

It can be argued that `setTimeout()` provides us with a cleaner solution since we can encapsulate subsequent calls to `getData()` inside the `getData()` function itself. However, we still need to make sure we clear the timeout when we unmount the component. I built out a more real-life example of the `getData()` function and added a state variable to better illustrate what we're trying to do here.

<pre>
  <code class="language-javascript">
    import React, { Component } from 'react';

    class Dashboard extends Component {
      intervalID;

      state = {
        data: [],
      }

      componentDidMount() {
        this.getData();
      }

      componentWillUnmount() {
        /*
          stop getData() from continuing to run even
          after unmounting this component. Notice we are calling
          'clearTimeout()` here rather than `clearInterval()` as
          in the previous example.
        */
        clearTimeout(this.intervalID);
      }

      getData = () =&gt; {
        fetch('/endpoint')
          .then(response => response.json())
          .then(data => {
            this.setState({ data: [...data] });
            // call getData() again in 5 seconds
            this.intervalID = setTimeout(this.getData.bind(this), 5000);
          });
      }

      render() {
        return (
          &lt;div>
            Our fancy dashboard lives here.
          &lt;/div>
        );
      }
    }
  </code>
</pre>

Since `setTimeout()` only defines a set of instructions to run one time at some point in the future, we need to use `setTimeout()` at the end of `getData()` to call itself. This sets up a repeating pattern for the execution of `getData()` that is a bit cleaner than using `setInterval()`.

Cheers!
