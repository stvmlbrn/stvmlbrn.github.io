---
layout: post
title: Display A 'Loading' Indicator In React During Ajax Requests
comments: true
---

It's good UX to provide visual feedback to the user of web applications
when an asynchronous operation is taking place. We've all seen these 'loading'
indicators in the form of spinning icons, overlays, modals, etc. as
we wait for view data to load. Here is a
quick look at how we can implement a 'loading' indicator when using React.

To get started, lets mock up a simple React component that fetches some data
when the user presses a button and displays that data after it's received from the
server.

<pre class="prettyprint">
  <code class="language-javascript">
    import React, { Component } from 'react';
    import { render } from 'react-dom';
    import Axios from 'axios';

    import ResultsTable from './resultsTable'; // component that will display the data

    class LoadingDemo extends Component {
      constructor(props) {
        super(props);
        this.state = {
          data: [], // will hold the results from our ajax call
        }
      }

      onClick = () => {
        Axios.get('/endpoint')
          .then(result => this.setState({ data: result.data }));
      }

      render() {
        const { data } = this.state;

        return (
          &lt;div&gt;
            &lt;button onClick={this.onClick}&gt;
              Load Data
            &lt;/button&gt;

            &lt;ResultsTable results={data} /&gt;
          &lt;/div&gt;
        );
      }
    }

    render(
      &lt;LoadingDemo /&gt;, document.getElementById('app')
    )
  </code>
</pre>

If you're at all familiar with React this should look pretty straighforward to you. We begin by importing basic React modules we need as well as `axios`, my preferred package for
making ajax requests.

Also imported is a `ResultsTable` component. For the sake of this demo this can by any component we've created to display our data such as a table, list, menu tree, whatever. The inner-workings of this component are not important right now, so let's
just assume this is a component that will display the data returned from the ajax request in a table.

Inside the component we defined a state variable `data` to hold the results of the ajax request, as well as an `onClick()` method that will be called when the button is clicked. The `onClick()` method uses Axios to make our ajax request and stores the result in the component state variable `data`. Finally, the `render()` method outputs a simple button with the `onClick()`
method bound to the _onClick_ event, and the `ResultsTable` component (passing in `data` from the state).

So with this current setup any time we click the button our app will fetch data from _/endpoint_, save the results
in the component state, and update the page to reflect the changes in the `ResultsTable` component. But we will not
see any changes in our browser window until this entire process completes. If the ajax request takes a few seconds it
can give the illusion to the user that our app is unresponsive, causing the user to click the button multiple times or
just have a bad overall impression of our app. To combat that we need to make the user aware stuff is happening in the background. This is where our 'loading' indicator will come into play.

Now that we have all that setup, let's create our `LoadingSpinner` component. We'll put it in a separate component
so we can reuse it elsewhere in our app, and because it doesn't need any internal state or react
lifecycle methods we'll us a stateless functional component.

<pre class="prettyprint">
  <code class="language-javascript">
    import React from 'react';

    const LoadingSpinner = () => (
      &lt;div&gt;
        &lt;i className="fa fa-spinner fa-spin" /&gt; Loading...
      &lt;/div&gt;
    );

    export default LoadingSpinner;
  </code>
</pre>

We're using some bootstrap classes `fa fa-spinner fa-spin` to create an animated spinning icon that, when rendered, will look something like this:

<div class="well well-sm">
  <i class="fa fa-spin fa-spinner"></i> Loading...
</div>

Now let's begin to integrate this component into the parent component that we created earlier. There are a few things we'll do to be able to display the loading indicator to the user when an ajax request is running:

  * import the LoadingSpinner component
  * maintain a state-level variable to indicate when an ajax request is running
  * conditionally render the LoadingSpinner component based on the value of the variable in the previous step

Let's put all this together:

<pre class="prettyprint">
  <code class="language-javascript">
    import React, { Component } from 'react';
    import { render } from 'react-dom';
    import Axios from 'axios';

    import ResultsTable from './resultsTable'; // component that will display the data
    import LoadingSpinner from './loadingSpinner';

    class LoadingDemo extends Component {
      constructor(props) {
        super(props);
        this.state = {
          data: [], // will hold the results from our ajax call
          loading: false, // will be true when ajax request is running
        }
      }

      onClick = () => {
        /*
          Begin by setting loading = true, and use the callback function
          of setState() to make the ajax request. Set loading = false after
          the request completes.
        */
        this.setState({ loading: true }, () => {
          Axios.get('/endpoint')
            .then(result => this.setState({
              loading: false,
              data: result.data,
            }));
        });
      }

      render() {
        const { data, loading } = this.state;

        return (
          &lt;div&gt;
            &lt;button onClick={this.onClick}&gt;
              Load Data
            &lt;/button&gt;

            {/*
              Check the status of the 'loading' variable. If true, then display
              the loading spinner. Otherwise, display the results.
            */}

            {loading &&
              &lt;LoadingSpinner /&gt;
            }

            {!loading &&
              &lt;ResultsTable results={data} /&gt;
            }
          &lt;/div&gt;
        );
      }
    }

    render(
      &lt;LoadingDemo /&gt;, document.getElementById('app')
    )
  </code>
</pre>

Done! Now when the user clicks the button to load data, the `loading` variable will be set to 'true', causing the `<LoadingSpinner />` component to be rendered. After the ajax request completes, `loading` is set to false causing the `<ResultsTable />` component to render.

Worth noting is how we use the callback function of `setState()` in the `onClick()` method. The `setState()` method is asynchronous, so if we want to be absolutely sure our `loading` variable gets set _before_ the ajax requests runs we need to call it from the `setState()` callback.
You can read more about the behavior of setState() [here](https://reactjs.org/docs/react-component.html#setstate).
