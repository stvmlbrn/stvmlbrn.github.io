---
layout: post
title: An Example of Form Validation With React.js
comments: true
---

As I first started to work with React and familiarize myself with it's one-way data bindings, I found
that I must re-think some of the ways I've solved seemingly trivial problems before. One such issue
is dealing with client-side form validation. There are no shortages of ways and opinions on how to do this,
and numerous packages exists to solve this very problem (with, IMHO varying degrees of success). However,
as I head down the path of learning something new I don't mind reinventing the wheel, so to speak, as
it often helps the learning process. As such, this is a process I came up with for form validation
that I still use today.

While some of this is may be obvious, there are a few objectives I have when performing client-side validation
that not all existing packages allow (though some do):
* Apply multiple validation checks against an input
* Be able to customize the error message based on the validation criteria that failed
* Easily clear all error messages and applied CSS to form controls

So let's take a look at an example sign-up form that asks for an email address, a password, and to
confirm the password. Here is how we might generate such a form using React:

<pre class="prettyprint">
  <code class="language-javascript">
    import React, {Component} from 'react';
    import {render} from 'react-dom';

    /*
    This is just some css sprinkled to help with the layout
    */
    import '../css/_bootstrap.scss';
    import '../css/signin.scss';

    class Login extends Component {
      constructor() {
        super();
        this.state = {
          email: '',
          password: '',
          confirmPassword: ''
        };
      }

      onChange = (e) =&gt; {
        var state = this.state;
        state[e.target.name] = e.target.value;

        this.setState(state);
      }

      onSubmit = (e) => {
        e.preventDefault();

        //handle form processing here....
      }

      render() {
        var {email, password, confirmPassword} = this.state;

        return (
          &lt;div className="container"&gt;
            &lt;form className="form-signin" onSubmit={this.onSubmit}&gt;
              &lt;h2 className="form-signin-heading"&gt;Create Account&lt;/h2&gt;

              &lt;div className="form-group"&gt;
                &lt;input type="text" name="email" className="form-control"
                  placeholder="Email address" value={email} onChange={this.onChange} autoFocus /&gt;
                &lt;span className="help-block"&gt;&lt;/span&gt;
              &lt;/div&gt;

              &lt;div className="form-group"&gt;
                &lt;input type="password" name="password" className="form-control"
                  placeholder="Password" value={password} onChange={this.onChange} /&gt;
                &lt;span className="help-block"&gt;&lt;/span&gt;
              &lt;/div&gt;

              &lt;div className="form-group"&gt;
                &lt;input type="password" name="confirmPassword" className="form-control"
                  placeholder="Confirm Password" value={confirmPassword} onChange={this.onChange} /&gt;
                &lt;span className="help-block"&gt;&lt;/span&gt;
              &lt;/div&gt;

              &lt;button className="btn btn-lg btn-primary btn-block" type="submit"&gt;Create Account&lt;/button&gt;
            &lt;/form&gt;
          &lt;/div&gt;
        );
      }
    };

    render(
      &lt;Login/&gt;, document.getElementById('login')
    );
  </code>
</pre>

Which ends up looking something like this in the browser:

![Basic Signup Form]({{site.url}}/assets/images/posts/react-form-validation-1.png)

Pretty basic stuff so far. To begin the validation, we need a place to hold an input's validation state
and the message we want to display to the user. To me, it makes sense to keep this in the component's
state with the input value.

<pre class="prettyprint">
  <code class="language-javascript">
    this.state = {
      email: {value: '', isValid: true, message: ''},
      password: {value:'', isValid: true, message: ''},
      confirmPassword: {value: '', isValid: true, message: ''}
    };
  </code>
</pre>

So now for each input we have a place to store its value, its validation state (defaulted to true), and
a place to store a message we want to show the user. We have to change the value attribute on each
of the form inputs to reflect this change (as well as the onChange() method). Also, let's go ahead an include the error message in the help-text
block. And, while we are at it, lets setup the dynamically assign class to each form-group div that
will apply an error class if the form input is invalid. To dynamically assigned classes, the
[classnames](https://www.npmjs.com/package/classnames) module works perfectly. We need to add an import
statement for the classnames module:

<pre class="prettyprint">
  <code class="language-javascript">
    import classNames from 'classnames';
  </code>
</pre>

Update the onChange() method:

<pre class="prettyprint">
  <code class="language-javascript">
    onChange = (e) => {
      var state = this.state;
      state[e.target.name].value = e.target.value;

      this.setState(state);
    }
  </code>
</pre>

And here is the new render() method:

<pre class="prettyprint">
  <code class="language-javascript">
    render() {
      var {email, password, confirmPassword} = this.state;
      /*
      Each of the group classes below will include the 'form-group' class, and will only
      include the 'has-error' class if the isValid value is false.
      */
      var emailGroupClass = classNames('form-group', {'has-error': !email.isValid});
      var passwordGroupClass = classNames('form-group', {'has-error': !password.isValid});
      var confirmGroupClass = classNames('form-group', {'has-error': !confirmPassword.isValid});

      return (
        &lt;div className="container"&gt;
          &lt;form className="form-signin" onSubmit={this.onSubmit}&gt;
            &lt;h2 className="form-signin-heading"&gt;Create Account&lt;/h2&gt;

            &lt;div className={emailGroupClass}&gt;
              &lt;input type="text" name="email" className="form-control"
                placeholder="Email address" value={email.value} onChange={this.onChange} autoFocus /&gt;
              &lt;span className="help-block"&gt;{email.message}&lt;/span&gt;
            &lt;/div&gt;

            &lt;div className={passwordGroupClass}&gt;
              &lt;input type="password" name="password" className="form-control"
                placeholder="Password" value={password.value} onChange={this.onChange} /&gt;
              &lt;span className="help-block"&gt;{password.message}&lt;/span&gt;
            &lt;/div&gt;

            &lt;div className={confirmGroupClass}&gt;
              &lt;input type="password" name="confirmPassword" className="form-control"
                placeholder="Confirm Password" value={confirmPassword.value} onChange={this.onChange} /&gt;
              &lt;span className="help-block"&gt;{confirmPassword.message}&lt;/span&gt;
            &lt;/div&gt;

            &lt;button className="btn btn-lg btn-primary btn-block" type="submit"&gt;Create Account&lt;/button&gt;
          &lt;/form&gt;
        &lt;/div&gt;
      );
    }
  </code>
</pre>

Perfect. At this point, anytime we write something to any of the <em>.message</em> values it will be displayed
in the help-text block for that input, and anytime we set any of the <em>.isValid</em> values to false the
'has-error' class will automatically be applied to the respective form group.

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
        //form processing here....
      }
    }

    formIsValid = () => {
      var state = this.state;

      if (!validator.isEmail(state.email.value)) {
        state.email.isValid = false; //this will trigger the has-error class
        state.email.message = 'Not a valid email address'; //will be displayed in the help-block for the email input

        this.setState(state);
        return false;
      }

      //additional validation checks here...

      return true;
    }
  </code>
</pre>

When the user now enters an invalid email address and clicks the 'Create Account' button,
the validation will fail and the form will reflect that to the user:

![Basic Signup Form With Validation]({{site.url}}/assets/images/posts/react-form-validation-2.png)

One last thing. Suppose a user fails one of our validation tests, but then fixes it, submits again, and fails
a validation test on a different input. We need to be able to clear any messages or has-error classes on any
of the other inputs so they don't stick around after the user fixes their mistake. The easiest way to do this
is to use a quick function that is called before our validation procedure runs that will reset all <em>.isValid</em>
and <em>.message</em> values.

<pre class="prettyprint">
  <code class="language-javascript">
    onSubmit = (e) => {
      e.preventDefault();
      this.resetValidationStates(); //reset states before the validation procedure is run.
      if (this.formIsValid()) { //run the validation, and if it's good move on.
        //form processing here....
      }
    }

    resetValidationStates = () => {
      var state = this.state;

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

That's it! Putting everything together our completed SignUp component looks like this:

<pre class="prettyprint">
  <code class="language-javascript">
    import React, {Component} from 'react';
    import {render} from 'react-dom';
    import classNames from 'classnames';
    import validator from 'validator';

    /*
    This is just some css sprinkled to help with the layout
    */
    import '../css/_bootstrap.scss';
    import '../css/signin.scss';

    class SignUp extends Component {
      constructor() {
        super();
        this.state = {
          email: {value: '', isValid: true, message: ''},
          password: {value: '', isValid: true, message: ''},
          confirmPassword: {value: '', isValid: true, message: ''}
        };
      }

      onChange = (e) =&gt; {
        var state = this.state;
        state[e.target.name].value = e.target.value;

        this.setState(state);
      }

      onSubmit = (e) =&gt; {
        e.preventDefault();
        this.resetValidationStates(); //reset states before the validation procedure is run.
        if (this.formIsValid()) { //run the validation, and if it's good move on.
          //form processing here....
        }
      }

      formIsValid = () =&gt; {
        var state = this.state;

        if (!validator.isEmail(state.email.value)) {
          state.email.isValid = false;
          state.email.message = 'Not a valid email address';

          this.setState(state);
          return false;
        }

        //additional validation checks here...

        return true;
      }

      resetValidationStates = () =&gt; {
        var state = this.state;

        Object.keys(state).map(key =&gt; {
          if (state[key].hasOwnProperty('isValid')) {
            state[key].isValid = true;
            state[key].message = '';
          }
        });
        this.setState(state);
      }

      render() {
        var {email, password, confirmPassword} = this.state;
        /*
        Each of the group classes below will include the 'form-group' class, and will only
        include the 'has-error' class if the isValid value is false.
        */
        var emailGroupClass = classNames('form-group', {'has-error': !email.isValid});
        var passwordGroupClass = classNames('form-group', {'has-error': !password.isValid});
        var confirmGroupClass = classNames('form-group', {'has-error': !confirmPassword.isValid});

        return (
          &lt;div className="container"&gt;
            &lt;form className="form-signin" onSubmit={this.onSubmit}&gt;
              &lt;h2 className="form-signin-heading"&gt;Create Account&lt;/h2&gt;

              &lt;div className={emailGroupClass}&gt;
                &lt;input type="text" name="email" className="form-control"
                  placeholder="Email address" value={email.value} onChange={this.onChange} autoFocus /&gt;
                &lt;span className="help-block"&gt;{email.message}&lt;/span&gt;
              &lt;/div&gt;

              &lt;div className={passwordGroupClass}&gt;
                &lt;input type="password" name="password" className="form-control"
                  placeholder="Password" value={password.value} onChange={this.onChange} /&gt;
                &lt;span className="help-block"&gt;{password.message}&lt;/span&gt;
              &lt;/div&gt;

              &lt;div className={confirmGroupClass}&gt;
                &lt;input type="password" name="confirmPassword" className="form-control"
                  placeholder="Confirm Password" value={confirmPassword.value} onChange={this.onChange} /&gt;
                &lt;span className="help-block"&gt;{confirmPassword.message}&lt;/span&gt;
              &lt;/div&gt;

              &lt;button className="btn btn-lg btn-primary btn-block" type="submit"&gt;Create Account&lt;/button&gt;
            &lt;/form&gt;
          &lt;/div&gt;
        );
      }
    };

    render(
      &lt;SignUp/&gt;, document.getElementById('signup')
    );
  </code>
</pre>








