---
layout: post
title: Considerations When Choosing An NPM Package
comments: true
tags: [npm]
icon: javascript.png
---

NPM, the package manager for the JavaScript programming language, has become the largest
package registry in the world with more than double the number of packages of the next
largest registry. This doesn't happen by accident. JavaScript is an incredibly popular
programming language and NPM makes it easy for any JavaScript developer to publish
his/her own packages. With such a large number of packages there is a high probability
that we can find an existing solution to almost anything we need as developers. And in
many cases we can find multiple packages to solve the same problem. When this happens
here are some things I look at to help make a selection on what package to use.

This list is not in any particular order and by no means should be considered exhaustive
of all things that we should consider when deciding what to include in our
programs. However, these are the things I look at most often to make a decision on what
to use, or maybe even write something from scratch.

* **Recent Download Totals** -
I know I said this list was not in a particular order but I am including this item at the
top of the list because, for me, this is the single biggest factor in choosing one package over
another. NPM will show the total number of downloads in the past day, week,
and month which provides a good indication of a package's popularity. The more popular a package,
the more people depend on it. A higher vested interest in a package by the community
as a whole means that it is more likely to be actively maintained, documented, and
battle tested.

* **Last Updated** -
NPM will show you the last time a package was updated, and you can also view the Github
repository and view the commit history. This will give you an indication of whether or not
a package is being actively maintained or it has perhaps been abandoned. Worth noting here
is that smaller packages that provide minimal functionality may have not been updated
in a long time. This does not necessarily mean it's not maintained, or that it wouldn't
be maintained if bugs are found. It may simply be a case that the package does what it's
supposed to do and no maintenance/development is needed.

* **Contributors** -
If you've been around the JavaScript ecosystem for a while you're undoubtedly familiar
with some of the leaders in the community, both individuals and companies. If you're
relatively new, you'll soon begin to recognize these leaders and will often see them
as publishers of packages. Knowing that a package is developed/maintained by a community
leader or simply a prolific contributor invokes a level of confidence in the quality
of the code.

* **Follows Semver** -
Semver, or [semantic versioning](http://semver.org/), is a standard way of defining
software releases and is heavily relied upon by NPM. It's always nice to see some
acknowlegement of semver in the documentation, or at least evidence of semver in the package
release history. A package that fails to follow semver may cause unexpected breaking
changes in your project at some point in the future.

* **Active Bug List** -
Are there any active bug/issue reports? Is there any activity in addressing reported issues? Does
there seem to be any community involvement in helping address issues? If there are active issues
that have not been addressed or at least commented on this should raise a red flag.

* **Documentation** -
Last but not least, how is the documentation? We've all struggled to use libraries/APIs/packages
that are poorly documented and it sucks. A well maintained and active package should contain
good documentation on installation, implementation, best practices, edge case scenarios, etc.


