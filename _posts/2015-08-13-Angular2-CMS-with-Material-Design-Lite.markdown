---
layout: post
title:  "Angular2 CMS with Material Design Lite"
date:   2015-08-09 18:50:15
categories: [programming]
tags: [angular.js, angular2, javascript, javascript frameworks, es6, ecma2015, cms, material design, material design lite]
---
Building a system to manage content using latest web technology is what it's all about, right?  Angular2 and Material Design Lite bring new life to this routine task.  Let's explore!

### Installing dependencies

#### Angular2 (ES6 Style)
I wrote about [installing Angular2]({% post_url 2015-08-09-npm-install-angular2 %}).  We compiled traveur.js from source and setup a header for an Angular2 app using ES6.  We're just going to use [es6-shim](https://www.npmjs.com/package/es6-shim) this time around.

-----
{% highlight console %}
$ mkdir cms-frontend
$ cd cms-frontend
$ npm init
$ npm install angular2 --save
$ npm install es6-shim --save
{% endhighlight %}
-----

We'll want to include this in our header, of course.  Below is what your HTML should look like, roughly:

-----
{% highlight javascript %}
<!-- cms-frontend/index.html -->
<html>
<head>
  <title>My first Angular2 CMS!</title>
  <link rel="stylesheet" href="style.css"></script>
  <!-- es6-shim before angular2, javascript after styles -->
  <script src="node_modules/es6-shim/es6-shim.js"></script>
  <script src="node_modules/angular2/angular2.js"></script>
</head>
<body>
  <!-- we're gonna put some cool stuff in here -->
  <script src="start.js"></script>
</body>
</html>
{% endhighlight %}
-----

#### Material Design Lite
We will be styling our CMS with Google's MDL because it's pretty and new.  Let's install it with NPM because that gives us control of the SASS variables.  If we want.

-------
{% highlight console %}
$ npm install material-design-lite --save
{% endhighlight %}
-------

For now, let's just use the pre-compiled stylesheet.  I'm not going to setup Gulp etc. in this post.

-----
{% highlight javascript %}
<head>
  <title>My first Angular2 CMS!</title>
  <!-- add material.css before style -->
  <link rel="stylesheet" href="node_modules/material-design-lite/material.css"></script>
  <link rel="stylesheet" href="style.css"></script>

  <!-- add material.js et al. after styles so as not to impeed page rendering -->
  <script src="node_modules/material-design-lite/material.js"></script>
  <script src="node_modules/es6-shim/es6-shim.js"></script>
  <script src="node_modules/angular2/angular2.js"></script>
</head>
{% endhighlight %}
-----

#### YMMV
When done, your package.json should look like the following:

------
{% highlight javascript %}
{
  "name": "cms-frontend",
  "version": "1.0.0",
  "description": "An Angular2 CMS Frontend",
  "main": "index.html",
  "scripts": {
    "test": "I love you"
  },
  "keywords": [
    "Angular2",
    "love"
  ],
  "author": "The universal self",
  "license": "ISC",
  "dependencies": {
    "angular2": "^2.0.0-alpha.34",
    "es6-shim": "^0.33.0",
    "material-design-lite": "^1.0.3"
  }
}

{% endhighlight %}
------

### Scaffolding the Angular2 App
I'm not shy to say I don't even know how to make a [module](https://docs.angularjs.org/guide/module) in Angular2.  Are there modules in Angular2?  Our [guide](http://www.sitepoint.com/writing-angularjs-apps-using-es6/) from the [previous post]({% post_url 2015-08-09-npm-install-angular2 %}) is definitely for Angular1.  The guide is dead.  Long live the... wait... we need a new guide!


