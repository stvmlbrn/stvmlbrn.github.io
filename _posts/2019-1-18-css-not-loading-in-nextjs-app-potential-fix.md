---
layout: post
title: CSS Not Loading In Next.js App - A Potential Fix
comments: true
tags: [javascript, next.js]
icon: javascript.png
---

I've been working on a Next.js project for a few weeks and have been experiencing intermitent issues with page CSS not being loaded when the page is refreshed. I tried a few different things but didn't have much success. I sort of hoped the problem would go away when I made a production build but no such luck.

I eventually found [this issue](https://github.com/zeit/next-plugins/issues/177) which pointed me in the right direction. A few differences in my case from the issue described in the link is (a) I'm using `node-sass`, not `next-css` and (b) it happens to me in development as well as production. Still, [the suggested fix](https://github.com/zeit/next-plugins/issues/177#issuecomment-390335190) seems to have worked for me.

In short, I was importing my .scss file in the wrong component. It should be imported in `_app.js`.

<pre>
  <code class="language-javascript">
    import App, { Container } from 'next/app';
    import Page from '../components/Page';

    /**
    This import of the .scss file was originally in the components/Page
    file. Moving it here was the magic fix!
    **/
    import '../static/scss/theme.scss';

    class MyApp extends App {
      render() {
        const { Component } = this.props;

        return (
          &lt;Container>
            &lt;Page>
              &lt;Component />
            </Page>
          </Container>
        );
      }
    }

    export default MyApp;
  </code>
</pre>

Cheers!
