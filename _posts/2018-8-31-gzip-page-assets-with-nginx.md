---
layout: post
title: Gzip Page Assets With Nginx
comments: true
tags: [nginx]
icon: nginx.png
---

This is something I feel like I should have known, but didn't. By default, Nginx only applies gzip compression to `text/html` MIME-types. This means any additional assets like css, js, fonts etc. are _not_ compressed.

The image below shows gzip section in the default `nginx.conf` file. As you can see, gzip is turned on but the _gzip_types_ directive is commented out.
<img src="/assets/images/posts/nginx-gzip.png">

To enable gzip compression for MIME-types other than `text/html` this line should be uncommented and include a list of MIME-types that you want Nginx to compress (or override this setting per-application rather than in the global config). There are already a few types listed, but a more complete list [can be found here](https://github.com/h5bp/server-configs-nginx/blob/master/nginx.conf#L67-L109) to give you some additional possibilities.

<!--
<div class="well well-sm">
  <strong>
    <i class="fa fa-exclamation-triangle text-danger"></i>
  </strong>
  If you are using TLS/SSL, be sure you understand the Breach exploit before enabling HTTP compression on your site/server to make sure gzip is safe for your application. <a href="http://www.breachattack.com/">http://www.breachattack.com/</a>
</div>
-->
Cheers!


