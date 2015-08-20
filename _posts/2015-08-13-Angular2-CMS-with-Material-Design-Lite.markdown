---
layout: post
title:  "Angular2 CMS with Material Design Lite and ES6"
date:   2015-08-09 18:50:15
categories: [programming]
tags: [angular.js, angular2, javascript, javascript frameworks, es6, ecma2015, cms, material design, material design lite, typescript, traceur]
published: false
---
Building a system to manage content using latest web technology is what it's all about, right?  Angular2 and Material Design Lite bring new life to this routine task.  Let's explore!

### Installing dependencies
I wrote about [installing Angular2]({% post_url 2015-08-09-npm-install-angular2 %}).  We compiled traceur.js from source and setup a header for an Angular2 app using ES6.  Turns out, most people seem to be using Typescript.  This is simply javascript with typing, and lets us get at ES6 features pretty nicely.

Might as well enter into the strictly typed land, yeah?  Then we'll be able to stick with the [Angular2 5-min quickstart](https://angular.io/docs/js/latest/quickstart.html) this time around without much hassel.

Let's install [typescript's package manager](https://www.npmjs.com/package/tsd) and other dependencies.

-----
{% highlight console %}
$ npm install -g tsd # typescript dependency manager
$ npm install -g typescript # typescript compiler
$ mkdir cms-frontend
$ cd cms-frontend
$ tsd init
$ tsd install angular2 es6-promise rx rx-lite --save # from the quickstart
$ npm init
$ npm install --save angular2
$ npm install --save systemjs # for es6 module loading
$ npm install --save material-design-lite
$ bower init
$ bower install --save traceur-runtime # runs our es6 code in the browser
{% endhighlight %}
-----

### Touching the index
To start, we want just index.html to include all of our dependencies.

-----
{% highlight javascript %}
<!-- cms-frontend/index.html -->
<html>
<head>
  <title>My first Angular2 CMS!</title>
  <link rel="stylesheet" href="/node_modules/material-design-lite/material.css"></script>
  <link rel="stylesheet" href="/style.css"></script>

  <!-- add material.js et al. after styles so as not to impeed page rendering -->
  <script src="/node_modules/material-design-lite/material.js"></script>
  <script src="/bower_components/traceur-runtime/traceur-runtime.js"></script>
  <script src="/node_modules/systemjs/dist/system.js"></script>
  <script src="/node_modules/angular2/angular2.js"></script>
</head>
<body>
  <cms></cms>
  <script>System.import('cms-app');</script>
</body>
</html>
{% endhighlight %}
-----

After we set the title, we include Material Design Lite css.  We're not worried about bringing in the sass files here.

We also load our own style.css before any javascript to get the page to render correctly while the js loads in the bg.

Loading material.js first is good imho because there's a weird bug (currently?) where the [page jumps to the top](https://github.com/google/material-design-lite/issues/1224) and this allows the material.js layout rendering to happen fastest.

Traceur is going to let our ES6 magic happen.  Even though Typescript is compiling it, apparently we still need traceur.  This, I gathered from the quickstart.  If I'm wrong, please correct me.

### Angular2 -- The Beginning

> System is a third-party open-source library that adds
> ES6 module loading functionality to browsers.

[ES6 modules](http://www.2ality.com/2014/09/es6-modules-final.html) are not [AMD](http://requirejs.org/docs/whyamd.html) or [CommonJS](http://requirejs.org/docs/commonjs.html).  Instead, ES6 has built-in support for modules and as such requires its own loader.  Looks like the quickstart guide picked System.  We did too.

-----
{% highlight javascript %}
<!-- cms-frontend/index.html -->
<body>
  <cms></cms>
  <script>System.import('cms-app');</script>
</body>
{% endhighlight %}
-----

Note that we're loading in a "cms-app" module.  Let's create cms-app.ts -- this is the file we'll compile down into our actual module in a bit.

-----
{% highlight javascript %}
/* cms-frontend/cms-app.ts  */
/// <reference path="typings/angular2/angular2.d.ts" />

import {Component, View, bootstrap} from 'angular2/angular2';

// Annotation section
@Component({
    selector: 'cms-app'
})
@View({
    template: '<h1>This is {{ name }}</h1>'
})

// Component controller
class CmsAppComponent {
    name: string;

    constructor() {
      this.name = 'Angular2: CMS';
    }
}

bootstrap(CmsAppComponent);
{% endhighlight %}
-----

Here we start by including the typescript definitions for angular2, allowing us to import the Component, View, and bootstrap functionality.

Decorators are an Angular2 thing which describe how the whole component is placed into the page.  We've got the @Component decorator defining our tag name, and we have the @View decorator describing what goes into the tag.

An ES6 class.  I've aligned it with the tag name and the filename.  Consistency is king, at the very least.

Our class can have a property and a constructor.  ECMA2015 for the win!  Our constructor just asigns a string to the name property.  And that's our CMS... for now.  So we bootstrap() the component by classname and we're off to the races.

### Typescript compiling
Let's start typescript compiler watching our new code for changes.

-----
{% highlight console %}
tsc --watch -m commonjs -t es5 --emitDecoratorMetadata cms-app.ts
{% endhighlight %}
----

### The first viewing
Quickly installing a local http server and booting it up...

-----
{% highlight console %}
$ npm install -g http-server
$ http-server
{% endhighlight %}
-----

Navigate to http://localhost:8080 and

