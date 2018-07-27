---
layout: post
title: Test JWT-Authenticated Express Routes with Jest And SuperTest
comments: true
tags: [node.js, express]
icon: javascript.png
---

So you have decided to use JSON Web Tokens (JWT) to secure your API, or you are already using JWT and need to add some unit tests for your routes, and you are a little unsure how to acquire, store, and pass the required token to complete the test. Let's take a look at how to do this using [Jest](https://facebook.github.io/jest/) and [SuperTest](https://www.npmjs.com/package/supertest).

The first thing we need to do is get a token from the API. To do so we can leverage Jest's `beforeAll()` function to get a token before any tests are run, and we'll use `supertest` to make a call to our authentication endpoint. For the sake of this example we'll be using `/login` for the endpoint and passing a username and password payload. Your actual authentication process may be different, so be sure to adjust what you see here accordingly. Once we receive a token back from the authentication endpoint we can store it in a variable that all tests will have access to.

<pre class="prettyprint">
  <code class="lang-js">
    const request = require('supertest');
    const app = require('../app'); // the express server

    // declare the token variable in a scope accessible by the entire test suite
    let token;

    beforeAll((done) => {
      request(app)
        .post('/login')
        .send({
          username: user,
          password: pw,
        })
        .end((err, response) => {
          token = response.body.token; // save the token!
          done();
        });
    });
  </code>
</pre>

Depending on how your authentication is configured you may want to give some thought about how to return a token when testing. For example, in the preceding code snippet we are passing a username and password, but where should be keep that information? We probably do not want to hardcode those values in the tests. One option may be to utilize environment variables to keep this information secure. My post on how to [Maintain Multiple Environment Configurations and Secrets in Node.js Apps](https://blog.stvmlbrn.com/2018/01/13/maintain-multiple-configurations-and-secrets-in-node-apps.html) may be useful for this. Another option may be to detect your runtime environment in the backend code and simply return a token without requiring credentials if the test environment is detected.

So now that we are authenticated and have a token it's just a matter of passing that token to any API we want to test, or we can test that a particular route requires authentication by *not* passing the token. Here is a quick example:

<pre class="prettyprint">
  <code class="lang-js">
    const request = require('supertest');
    const app = require('../app'); // the express server

    // declare the token variable in a scope accessible by the entire test suite
    let token;

    beforeAll((done) => {
      request(app)
        .post('/login')
        .send({
          username: user,
          password: pw,
        })
        .end((err, response) => {
          token = response.body.token; // save the token!
          done();
        });
    });

    describe('GET /', () => {
      // token not being sent - should respond with a 401
      test('It should require authorization', () => {
        return request(app)
          .get('/')
          .then((response) => {
            expect(response.statusCode).toBe(401);
          });
      });
      test('It responds with JSON', () => {
        return request(app)
          .get('/')
          .set('Authorization', `Bearer ${token}`) // send the token!!
          .then((response) => {
            expect(response.statusCode).toBe(200);
            expect(response.type).toBe('application/json');
          });
      });
    });
  </code>
</pre>


