---
layout: post
title: How To Add Sass Support to a Create-React-App Project (after ejecting)
comments: true
tags: [React, Create-React-App, Sass]
---

I spent far too much time when I was first getting into React development trying to maintain my own boilerplate for new projects. Even though I don't use any of that old boilerplate code anymore, it wasn't time completely wasted as I learned a good deal about the React ecosystem, especially webpack. But eventually the process became too tedious and, frankly, I wasn't doing anywhere near as good a job as what others had already done. So now I start every new project with `create-react-app` ([https://github.com/facebookincubator/create-react-app](https://github.com/facebookincubator/create-react-app)). It's an incredible tool but it doesn't include support for Sass, which I often use. So I must add that to the configuration and build process manually.

By default, `create-react-app` abstracts most of the configuration away so you can focus on writing code. But if you would like to customize the build tools and configuration choices, you can 'eject' from the default setup to gain more control (or to just peek under the hood to see how things work). While not usually required I tend to use the 'eject' feature, just because. So it's in this 'ejected' state that I need to add Sass support.

<div class="alert alert-info">
<i class="fa fa-exclamation-circle"></i> This tutorial assumes you have already setup your project using `create-react-app` and have run `npm run eject`.
</div>

This first thing we need to do is install 3 packages, `node-sass`, `sass-loader`, and `resolve-url-loader`.

<pre class="prettyprint lang-bsh">
  <code class="language-bsh">
    $ npm i node-sass sass-loader resolve-url-loader
  </code>
</pre>

With the packages now installed, locate the 2 webpack config files in the `config` folder. They should be named `webpack.config.dev.js` and `webpack.config.prod.js` for the development and production builds, respectively. Open `webpack.config.dev.js` and locate the `module` section. This section has a `rules` array that contains an array named `oneOf`. Inside of this array you should see rules for dealing with images, css, and js files. Add the following object to this array:

<pre class="prettyprint">
  <code class="language-js">
    {
      test: /\.scss$/,
      include: [paths.appSrc, paths.appNodeModules],
      use: [
        {
          loader: require.resolve('style-loader'),
          options: {
            sourceMap: true
          }
        },
        {
          loader: require.resolve('css-loader'),
        },
        require.resolve('resolve-url-loader'),
        {
          loader: require.resolve('sass-loader'),
          options: {
            sourceMap: true
          }
        }
      ]
    },
  </code>
</pre>

Now open `webpack.config.prod.js` and, in the same location, add the following object:

<pre class="prettyprint">
  <code class="language-js">
    {
      test: /\.scss$/,
      include: [paths.appSrc, paths.appNodeModules],
      use: [
        {
          loader: require.resolve('style-loader'),
          options: {
            sourceMap: shouldUseSourceMap
          }
        },
        {
          loader: require.resolve('css-loader'),
        },
        require.resolve('resolve-url-loader'),
        {
          loader: require.resolve('sass-loader'),
          options: {
            sourceMap: shouldUseSourceMap
          }
        }
      ]
    },
  </code>
</pre>

Note the difference between these configurations is the if we are going to generate source maps. In the development configuration, source maps are created by default. In the production configuration the use of source maps is determined by the `shouldUseSourceMap` variable which defaults to 'true' and can be overridden with `process.env.GENERATE_SOURCEMAP`.







