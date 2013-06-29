---
layout: post
title: "Semantic Versioning"
date: 2013-06-29 10:5
comments: true
categories: [Code, Ruby]
---

Version numbering is a field on which you could talk for hours on end and still
not make any sense out of it. Take package managers for instance; you have a
package foo, that depends on package bar - now if it was as simple as that, we'd
have no problems. It's more likely that you'd wind up with version x of foo
depending on version y of bar, which in turn depends on version z of baz. So
far, so good. Now, let's update baz to version z1 - OOPS! There are some changes
to version z1 that break version y of bar, which means we need to revert to
version z.

<!-- more -->

Now, as a human, it is possible to go through the changelog for each version and
try to figure out if the changes in the new version will work. However, this is
time consuming and error prone. A simpler way is to let the computer do this,
but natural language processing is a hard problem. Instead, we depend on a
specific version number scheme that emposes some semantics to ensure
compatibility across releases. Enter [Semantic versioning](http://semver.org).
This is a specification that packages can conforming to and it shifts the
responsibility of the user to the developer to assure compatibility.

As a developer, it does add a little bit of additional work, but the payoff is
great. All you need to do is ensure that any change that affects backward
compatibility is reflected in the major version number. Any changes that add
functionality, but without affecting backward compatibility should show up in
the minor version. And any bug fixes should show up in the patch version.

This is also an excellent project to get my hands dirty on Ruby programming.
Although I have experience with shell programming, Perl and a bit of Python, I
never really thought of myself as a Ruby person, until now.

This results in a gem I call the version manager library or VMLib. It comprises
of a Ruby module with a Version class, that allows the user to compare different
version numbers, and manage a projects version number. As of this writing, the
VMLib package also has a wrapper script that allows you to apply semantic
versioning to any software package. Check out <http://github.com/nirenjan/vmlib>

This project goes through different aspects of the Ruby language, including, but
not limited to:

* Modules
* Classes
* Exceptions
* Regular expressions
* String manipulation
* RDoc documentation

One simple method is to go through the code in the `lib/vmlib` folder, and since
it is documented, it should give some decent examples of usage.
