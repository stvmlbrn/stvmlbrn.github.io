---
layout: post
title: Conditionally Apply Redux Middleware
comments: true
tags: [react, redux]
icon: react.png
---

There may be situations when you only want to apply certain redux middlewares. For example, I typically use `redux-thunk` and `redux-logger` but I don't necessarily want to include the logger in my production build. One way to accomplish this is to add any middleware you want to use into an array, which allows you to apply your conditional logic to determine what should be added, and then use the spread operator (...) to apply that middleware.

Here is an example:

<pre class="prettyprint">
  <code class="language-javascript">
    import { createStore, applyMiddleware } from 'redux';
    import { logger } from 'redux-logger';
    import thunk from 'redux-thunk';

    /*
    Create an array to hold any middleware we want to use.
    Since I will also want to include the thunk middleware I can
    go ahead and add that now.
    */
    const middlewares = [thunk];

    /*
    Since I don't want to include the logger middleware in
    production I can check on my environment and only add it
    if I'm in development.
    */
    if (process.env.NODE_ENV === 'development') {
      middlewares.push(logger);
    }

    const store = createStore(reducer, initialState, applyMiddlware(...middlewares));
  </code>
</pre>

Cheers!

<hr/>

<strong>Note:</strong> I didn't realize it at the time, but there is an example for this exact situation included in the redux documentaion [here](https://redux.js.org/api/applymiddleware) that is (no surprise) a better solution as they take into account build size and don't import the packages unless necessary. Be sure to check it out.
