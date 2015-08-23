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
I wrote about [installing Angular2]({% post_url 2015-08-09-npm-install-angular2 %}).  We compiled traceur.js from source and setup a header for an Angular2 app using ES6.

This time through, we'll grab down Typescript et al. and include our libs from CDNs.  Let's install [typescript's package manager](https://www.npmjs.com/package/tsd) and other dependencies.

-----
{% highlight console %}
$ npm install -g tsd # typescript dependency manager
$ npm install -g typescript # typescript compiler
$ mkdir cms-frontend
$ cd cms-frontend
$ tsd init
$ tsd install angular2 es6-promise rx rx-lite --save # from the quickstart
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
  <link rel="stylesheet" href="/style.css">
  <link rel="stylesheet" href="https://storage.googleapis.com/code.getmdl.io/1.0.4/material.indigo-pink.min.css">
  <link rel="stylesheet" href="https://fonts.googleapis.com/icon?family=Material+Icons">

  <script src="https://storage.googleapis.com/code.getmdl.io/1.0.4/material.min.js"></script>
  <script src="https://github.jspm.io/jmcriffey/bower-traceur-runtime@0.0.87/traceur-runtime.js"></script>
  <script src="https://jspm.io/system@0.16.js"></script>
  <script src="https://code.angularjs.org/2.0.0-alpha.32/angular2.dev.js"></script>
</head>
<body>
  <cms-app></cms-app>
  <script>System.import('cms-app');</script>
</body>
</html>
{% endhighlight %}
-----

After we set the title, we include our stylesheets and scripts.  Pretty straight-forward so far.  I'm sure you can handle creating the style.css file :)

If you are brave enough to be trying to install these dependencies via node, note that you'll need to configure paths for System.js

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
  <cms-app></cms-app>
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

Navigate to http://localhost:8080 and... wait there's nothing there!  What?

Just kidding.  You should actually see the text "This is Angular2: CMS" in beautiful Roboto Sans, 56px font size.  Yes?  Okay. No?!  Check for console/network errors and make sure your header paths are correct.

Moving on.

### Skipping Login
This CMS will be wide-open.  Locally, that is.

If you remember, we skipped auth when building our [REST API with Sails]({% post_url 2015-08-12-Sails-Javascript-REST-API-and-CRUD %}), also.  It's easy enough to implement oauth using passport and track auth tokens along with other user information (like, for example, an admin flag).  Maybe I'll do another article on adding oauth to an existing CMS and REST Api...

### Material Design Staffer Admin
We just want some basic MDL classes to give us a nice little starter-form.  We're gonna put them right into our cms-app.ts view.

Our [API]({% post_url 2015-08-12-Sails-Javascript-REST-API-and-CRUD %}) has support for Staffers (aka `localhost:1337/staff`) and even has a convenient way for adding more during development (aka `localhost:1337/staff/create?name='any'&otherprop='more'`).  Note that there is no schema -- a staffer is just a JSON object.

Let's setup a basic form.  Just a [textfield](http://www.getmdl.io/components/#textfields-section) with a label and a [submit button](http://www.getmdl.io/components/#buttons-section).

-----
{% highlight javascript %}
/* cms-frontend/cms-app.ts  */
@View({
    template: `
      <form action="#">
        <div class="mdl-textfield mdl-js-textfield">
          <input class="mdl-textfield__input" type="text" id="name" />
          <label class="mdl-textfield__label" for="sample1">Text...</label>
        </div>
        <button class="mdl-button mdl-js-button mdl-js-ripple-effect">
          Save Staffr
        </button>
      </form>
    `
})
{% endhighlight %}
-----

