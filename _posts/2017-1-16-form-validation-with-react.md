---
layout: post
title: Custom Form Validation Example With React and Bootstrap
comments: true
tags: [react, bootstrap]
icon: react.png
---

As I first started to work with React and familiarize myself with it's one-way data bindings I found that I must re-think some of the ways I've solved seemingly trivial problems before. One such issue is dealing with custom client-side form validation. There are no shortages of ways and opinions on how to do this, and numerous packages exists to solve this very problem (with, IMHO varying degrees of success). However, as I head down the path of learning something new I don't mind reinventing the wheel as it often helps the learning process.

<div class="alert alert-info">
  This post was updated in August 2018 from it's original content to avoid directly mutating state.
</div>

While some of this is may be obvious, there are a few objectives I have when performing client-side validation:

* Apply multiple validation checks against an input
* Be able to customize the error message based on the validation criteria that failed
* Easily clear all error messages and applied CSS to form controls

So let's take a look at an example sign-up form that asks for an email address, a password, and to
confirm the password. Here is how we might generate such a form using React with basic Bootstrap styles.

<div class="well well-sm">
Note: Make sure the Bootstrap CSS is available on your page by including it in your bundle, loading from CDN, or some other method.
</div>

<pre class="prettyprint">
  <code class="language-javascript">
    import React, { Component } from 'react';

    class Login extends Component {
      state = {
        email: '',
        password: '',
        confirmPassword: '',
      }

      onChange = (e) => {
        this.setState([e.target.name]: e.target.state);
      }

      onSubmit = (e) => {
        e.preventDefault();

        //handle form processing here....
      }

      render() {
        const { email, password, confirmPassword } = this.state;

        return (
          &lt;div className="container"&gt;
            &lt;form className="form-signin" onSubmit={this.onSubmit}&gt;
              &lt;h2 className="form-signin-heading"&gt;Create Account&lt;/h2&gt;

              &lt;div className="form-group"&gt;
                &lt;input
                  type="text"
                  name="email"
                  className="form-control"
                  placeholder="Email address"
                  value={email}
                  onChange={this.onChange}
                  autoFocus
                /&gt;
                &lt;span className="help-block"&gt;&lt;/span&gt;
              &lt;/div&gt;

              &lt;div className="form-group"&gt;
                &lt;input
                  type="password"
                  name="password"
                  className="form-control"
                  placeholder="Password"
                  value={password}
                  onChange={this.onChange}
                /&gt;
                &lt;span className="help-block"&gt;&lt;/span&gt;
              &lt;/div&gt;

              &lt;div className="form-group"&gt;
                &lt;input
                  type="password"
                  name="confirmPassword"
                  className="form-control"
                  placeholder="Confirm Password"
                  value={confirmPassword}
                  onChange={this.onChange}
                /&gt;
                &lt;span className="help-block"&gt;&lt;/span&gt;
              &lt;/div&gt;

              &lt;button
                className="btn btn-lg btn-primary btn-block"
                type="submit"
              &gt;
                Create Account
              &lt;/button&gt;
            &lt;/form&gt;
          &lt;/div&gt;
        );
      }
    };
  </code>
</pre>

Which ends up looking something like this in the browser:

![Basic Signup Form]({{site.url}}/assets/images/posts/react-form-validation-1.png)

Pretty basic stuff so far. To begin the validation we need a place to hold an input's validation state and the message we want to display to the user. To me, it makes sense to keep this in the component's state with the input value. In order to help facilitate resetting the form back to it's original state, it's helpful to keep the initial form data in a separate variable, and then load that variable into the state.

<pre class="prettyprint">
  <code class="language-javascript">
    formDefaults = {
      email: { value: '', isValid: true, message: '' },
      password: { value:'', isValid: true, message: '' },
      confirmPassword: { value: '', isValid: true, message: '' }
    }

    state = {
      ...this.formDefaults
    };
  </code>
</pre>

So now for each input we have a place to store its value, its validation state (defaulted to true), and a place to store a message we want to show the user. We have to change the value attribute on each of the form inputs to reflect this change. We'll also need to update the onChange() method, and since we are now dealiing with objects we need to be careful to not mutate state directly. Also, let's go ahead an include the error message in the help-text block. And, while we are at it lets setup the dynamically assigned class to each form-group div that will apply an error class if the form input is invalid.

To dynamically assign classes, the `classnames` ([https://www.npmjs.com/package/classnames](https://www.npmjs.com/package/classnames)) package works perfectly.

<pre class="prettyprint">
  <code class="language-javascript">
    import classNames from 'classnames';
  </code>
</pre>

Update the onChange() method, being careful not to directly mutate state:

<pre class="prettyprint">
  <code class="language-javascript">
    onChange = (e) => {
      const state = {
        ...this.state,
        [e.target.name]: {
          ...this.state[e.target.name],
          value: e.target.value,
        }
      };

      this.setState(state);
    }
  </code>
</pre>

And here is the new render() method:

<pre class="prettyprint">
  <code class="language-javascript">
    render() {
      const { email, password, confirmPassword } = this.state;
      /*
      Each of the group classes below will include the 'form-group' class, and will only
      include the 'has-error' class if the isValid value is false.
      */
      const emailGroupClass = classNames('form-group', { 'has-error': !email.isValid });
      const passwordGroupClass = classNames('form-group', { 'has-error': !password.isValid });
      const confirmGroupClass = classNames('form-group', { 'has-error': !confirmPassword.isValid });

      return (
        &lt;div className="container"&gt;
          &lt;form className="form-signin" onSubmit={this.onSubmit}&gt;
            &lt;h2 className="form-signin-heading"&gt;Create Account&lt;/h2&gt;

            &lt;div className={emailGroupClass}&gt;
              &lt;input
                type="text"
                name="email"
                className="form-control"
                placeholder="Email address"
                value={email.value}
                onChange={this.onChange}
                autoFocus
              /&gt;
              &lt;span className="help-block"&gt;{email.message}&lt;/span&gt;
            &lt;/div&gt;

            &lt;div className={passwordGroupClass}&gt;
              &lt;input
                type="password"
                name="password"
                className="form-control"
                placeholder="Password"
                value={password.value}
                onChange={this.onChange}
              /&gt;
              &lt;span className="help-block"&gt;{password.message}&lt;/span&gt;
            &lt;/div&gt;

            &lt;div className={confirmGroupClass}&gt;
              &lt;input
                type="password"
                name="confirmPassword"
                className="form-control"
                placeholder="Confirm Password"
                value={confirmPassword.value}
                onChange={this.onChange}
              /&gt;
              &lt;span className="help-block"&gt;{confirmPassword.message}&lt;/span&gt;
            &lt;/div&gt;

            &lt;button
              className="btn btn-lg btn-primary btn-block"
              type="submit"
            &gt;
              Create Account
            &lt;/button&gt;
          &lt;/form&gt;
        &lt;/div&gt;
      );
    }
  </code>
</pre>

Perfect. At this point, anytime we write something to any of the <em>.message</em> values it will be displayed in the help-text block for that input, and anytime we set any of the <em>.isValid</em> values to false the 'has-error' class will automatically be applied to the respective form group.

Let's now add a simple form validation procedure so we can see it in action. We'll use the
[validator](https://www.npmjs.com/package/validator) module to help us out.

<pre class="prettyprint">
  <code class="language-javascript">
    import validator from 'validator';
  </code>
</pre>

<pre class="prettyprint">
  <code class="language-javascript">
    onSubmit = (e) => {
      e.preventDefault();
      if (this.formIsValid()) {
        // form processing here....
      }
    }

    formIsValid = () => {
      const email = { ...this.state.email };
      const password = { ...this.state.password };
      const confirmPassword = { ...this.state.confirmPassword };
      let isGood = true;

      if (!validator.isEmail(email.value)) {
        email.isValid = false;
        email.message = 'Not a valid email address';
        isGood = false;
      }

      // perform addtion validation on password and confirmPassword here...

      if (!isGood) {
        this.setState({
          email,
          password,
          confirmPassword,
        });
      }

      return isGood;
    }
  </code>
</pre>

When the user now enters an invalid email address and clicks the 'Create Account' button,
the validation will fail and the form will reflect that to the user:

![Basic Signup Form With Validation]({{site.url}}/assets/images/posts/react-form-validation-2.png)

Now, suppose a user fails one of our validation tests, but fixes it, submits again, and fails a validation test on a different input. We need to be able to clear any messages or has-error classes on any of the other inputs so they don't stick around after the user fixes their mistake. The easiest way to do this is to use a quick function that is called before our validation procedure runs that will reset all <em>.isValid</em>
and <em>.message</em> values.

<pre class="prettyprint">
  <code class="language-javascript">
    onSubmit = (e) => {
      e.preventDefault();
      this.resetValidationStates(); // reset states before the validation procedure is run.
      if (this.formIsValid()) { // run the validation, and if it's good move on.
        // form processing here....
      }
    }

    resetValidationStates = () => {
      // make a copy of everything in state
      const state = JSON.parse(JSON.stringify(this.state));

      /*
      loop through each item in state and if it's safe to assume that only
      form values have an 'isValid' property, we can use that to reset their
      validation states and keep their existing value property. This process
      makes it easy to set all validation states on form inputs in case the number
      of fields on our form grows in the future.
      */
      Object.keys(state).map(key => {
        if (state[key].hasOwnProperty('isValid')) {
          state[key].isValid = true;
          state[key].message = '';
        }
      });

      this.setState(state);
    }
  </code>
</pre>

Finally, we may want to add a 'Reset Form' button to the form. This will reset the form back to it's initial state. Since we kept the initial form data in a separate class variable, we can use that to overwrite the existing form values currently in state. Keep in mind, if you have state data other than your form inputs you'll need to be more careful about overwriting your entire state like we are doing here.

<pre class="prettyprint">
  <code class="language-javascript">
    resetForm = () => {
      this.setState(...this.formDefaults);
    }
  </code>
</pre>

That's it! Putting everything together our completed SignUp component looks like this:

<pre class="prettyprint">
  <code class="language-javascript">
    import React, { Component } from 'react';
    import classNames from 'classnames';
    import validator from 'validator';

    class SignUp extends Component {
      formDefaults = {
        email: { value: '', isValid: true, message: '' },
        password: { value: '', isValid: true, message: '' },
        confirmPassword: { value: '', isValid: true, message: '' }
      }

      state = {
        ...this.formDefaults
      };

      onChange = (e) => {
        const state = {
          ...this.state,
          [e.target.name]: {
            ...this.state[e.target.name],
            value: e.target.value,
          }
        };

        this.setState(state);
      }

      onSubmit = (e) => {
        e.preventDefault();
        this.resetValidationStates(); // reset states before the validation procedure is run.
        if (this.formIsValid()) { // run the validation, and if it's good move on.
          // form processing here....
        }
      }

      formIsValid = () => {
        const email = { ...this.state.email };
        const password = { ...this.state.password };
        const confirmPassword = { ...this.state.confirmPassword };
        let isGood = true;

        if (!validator.isEmail(email.value)) {
          email.isValid = false;
          email.message = 'Not a valid email address';
          isGood = false;
        }

        // perform addtion validation on password and confirmPassword here...

        if (!isGood) {
          this.setState({
            email,
            password,
            confirmPassword,
          });
        }

        return isGood;
      }

      resetValidationStates = () =&gt; {
        // make a copy of everything in state
        const state = JSON.parse(JSON.stringify(this.state));

        /*
        loop through each item in state and if it's safe to assume that only
        form values have an 'isValid' property, we can use that to reset their
        validation states and keep their existing value property. This process
        makes it easy to set all validation states on form inputs in case the number
        of fields on our form grows in the future.
        */
        Object.keys(state).map(key => {
          if (state[key].hasOwnProperty('isValid')) {
            state[key].isValid = true;
            state[key].message = '';
          }
        });

        this.setState(state);
      }

      resetForm = () => {
        this.setState(...this.formDefaults);
      }

      render() {
        const { email, password, confirmPassword } = this.state;
        /*
        Each of the group classes below will include the 'form-group' class, and will only
        include the 'has-error' class if the isValid value is false.
        */
        const emailGroupClass = classNames('form-group', { 'has-error': !email.isValid });
        const passwordGroupClass = classNames('form-group', { 'has-error': !password.isValid });
        const confirmGroupClass = classNames('form-group', { 'has-error': !confirmPassword.isValid });

        return (
          &lt;div className="container"&gt;
            &lt;form className="form-signin" onSubmit={this.onSubmit}&gt;
              &lt;h2 className="form-signin-heading"&gt;Create Account&lt;/h2&gt;

              &lt;div className={emailGroupClass}&gt;
                &lt;input
                  type="text"
                  name="email"
                  className="form-control"
                  placeholder="Email address"
                  value={email.value}
                  onChange={this.onChange}
                  autoFocus
                /&gt;
                &lt;span className="help-block"&gt;{email.message}&lt;/span&gt;
              &lt;/div&gt;

              &lt;div className={passwordGroupClass}&gt;
                &lt;input
                  type="password"
                  name="password"
                  className="form-control"
                  placeholder="Password"
                  value={password.value}
                  onChange={this.onChange}
                /&gt;
                &lt;span className="help-block"&gt;{password.message}&lt;/span&gt;
              &lt;/div&gt;

              &lt;div className={confirmGroupClass}&gt;
                &lt;input
                  type="password"
                  name="confirmPassword"
                  className="form-control"
                  placeholder="Confirm Password"
                  value={confirmPassword.value}
                  onChange={this.onChange}
                /&gt;
                &lt;span className="help-block"&gt;{confirmPassword.message}&lt;/span&gt;
              &lt;/div&gt;

              &lt;button
                className="btn btn-lg btn-primary btn-block"
                type="submit"
              &gt;
                Create Account
              &lt;/button&gt;
            &lt;/form&gt;
          &lt;/div&gt;
        );
      }
    };
  </code>
</pre>








