---
layout: post
title: Maintain Multiple Environment Configurations and Secrets in Node.js Apps
comments: true
tags: [node.js]
---

During the software development process your application will likely need to adapt to multiple runtime environments. At a minimum you'll hopefully have at least 2 runtime environments: development and production. Additional runtime environments such as testing and staging are also common. Each of these runtime environments will likely require different configurations for things such as database connection information, log settings, port, and so on. In addition to these configurations you may also have settings that may be unique to each developer on the team such as usernames/passwords and API keys. These "secrets" shouldn't be included in the codebase or repository and are best used as environment variables, but we still need a way to maintain them. Let's look at 2 packages that when used together create a nice solution to this problem.

* `config` - [https://www.npmjs.com/package/config](https://www.npmjs.com/package/config) - Use for managing configuration settings across multiple runtime environments.
* `dotenv-safe` - [https://www.npmjs.com/package/dotenv-safe](https://www.npmjs.com/package/dotenv-safe) - Used for defining environment variables for keeping secrets such as passwords, api keys, etc., out of a codebase and repository.

Let's begin by installing both packages packages:

<pre class="prettyprint">
  <code class="language-bsh">
    $ npm i config dotenv-safe
  </code>
</pre>

By default, `config` will look for a _config_ folder at the root of your project for the configuration files, so let's go ahead and create the folder with a default configuration file:

<pre class="prettyprint">
  <code class="language-bsh">
    $ mkdir config && touch ./config/default.json
  </code>
</pre>

As you can tell by the name of the file we just created, `config` settings will be saved as a JSON object. Our _default.json_ file that contains settings for a database connection may look something like this:

<pre class="prettyprint">
  <code class="language-javascript">
    {
      "Database": {
        "host": "dbserver_dev.example.com",
        "db_name": "sample_db",
        "username": "user",
        "password": "secret_password"
      }
    }
  </code>
</pre>

Now, anywhere in the project where we need access to this information, we can required the `config` package:

<pre class="prettyprint">
  <code class="lanaguage-javascript">
    const config = require('config');
  </code>
</pre>

and then access the settings:

<pre class="prettyprint">
  <code class="language-javascript">
    config.get('Database.host'); // = dbserver.example.com
  </code>
</pre>

Pretty cool, right? The ability to keep these types of settings together is really handy. But the whole point of `config` is to help us manage these configurations across multiple runtime environments. To do this,we need to create a new configuration file for each environment. For example, to add our configuration for the _production_ environment we need to create a _production.json_ in the _config_ folder. The _production.json_ file may look something like this:

<pre class="prettyprint">
  <code class="language-javascript">
    {
      "Database": {
        "host": "dbserver_production.example.com"
      }
    }
  </code>
</pre>

Notice we don't have to add the entire configuration to the new file. Anything that may stay the same as the default configuration, such as the database name, username, and password in our example, do not need to be in the _production.json_ file. When the application is run in the production environment, `config.get('Database.host')` will be pulled from the _production.json_ file and revert to the _default.json_ configuration for everything else. To support any other runtime environment that requires different configuration we just need to create a new file  - _staging.json, qa.json,_ etc. and add in the new configuration settings.

__NOTE:__ In case your not sure how to set your runtime environment, on linux you would run `export NODE_ENV=production` prior to starting your application to set your environment to production. On Windows, run `set NODE_ENV=production'.

But we have a glaring problem which you may have already noticed. Our database password is in the configuration. We generally want to keep that out of the codebase and repository for security reasons, so we don't want to include it in `config`. For that, we'll use `dotenv-safe`.

The `dotenv-safe` package allows us to define any environment variables we want to keep private. It's worth noting that we are now talking about the environment used by the Node process, not the runtime environment such as development, production, staging, etc. that we refer to when discussing configuration settings with `config`. By default `dotenv-safe` will pull values from a _.env_ file located at the root of the project:

<pre class="prettyprint">
  <code class="language-bsh">
    $ touch .env
  </code>
</pre>

In our example, we want to keep our database password out of the codebase, so the _.env_ file will look something like this:

<pre class="prettyprint">
  <code class="language-bsh">
    DB_PASSWORD=secret_password
  </code>
</pre>

Then, as early as possible in our project code, we need load these environment variables:

<pre class="prettyprint">
  <code class="language-javascript">
    require('dotenv-safe').load(); // only needs done once in the project
  </code>
</pre>

Now any variable we define in the _.env_ file will be available in our code as _process.env.VARIABLE_NAME_.

<pre class="prettyprint">
  <code class="language-javascript">
    db_password = process.env.DB_PASSWORD // = secret_password
  </code>
</pre>

Since we want to keep the information in this file private and out of the code repository, be sure to add _.env_ to your _.gitignore_ file.

The nice thing about `dotenv-safe` over the package it's built upon, `dotenv`, is that we can define an example file without values that should to be added to the repository so it's easy to keep track of what environment variables are expected without having to go through the code.

<pre class="prettyprint">
  <code class="language-bsh">
    $ touch .env.example
  </code>
</pre>

<pre class="prettyprint">
  <code class="language-bsh">
    DB_PASSWORD=
  </code>
</pre>

Not only can developers use this file to be aware of any environment variables they need to define in their respective _.env_ file, but by default `dotenv-safe` will halt the execution of your program if any variable defined in _.env.example_ is not found in _.env_.

With this current setup we can access any runtime environment setting using `config.get('whatever.setting')` and any private environment variable via `process.env.VARIABLE_NAME`. This works, but we can go a step farther so the only methods we need to use in our code are those offered by the `config` package, as well as take advantage of the hierarchical nature of `config`. We do this by telling `config` what environment variables to expect, and these values will override anything we have defined in the _.json_ configuration files. To do this, we make a new file in the _config_ folder called _custom-environment_variables.json_ which may look something like this:

<pre class="prettyprint">
  <code class="language-javascript">
    {
      "Database: {
        "password": "DB_PASSWORD"
      }
    }
  </code>
</pre>

Now, when we call `config.get('Database.password')`, `config` will look for an environment variable called "DB_PASSWORD". If it's not found it will use the value found in the _.json_ file that matches our current runtime environment, and if it's not found there it will load from _default.json_.

__NOTE:__ Since we are now keeping the DB_PASSWORD value in _.env_ file, be sure to remove it from the original _default.json_ file we defined at the beginning of this tutorial.

And there you have it - `config` and `dotenv-safe` make for a nice setup to manage runtime environment configurations and environment variables!



