---
layout: post
title:  "Ionic, isn't it?"
date:   2015-08-29 21:57:00
categories: [programming]
tags: [angular.js, ionic, html5, hybrid apps, yeoman generators, cordova, javascript, javascript frameworks]
---

Making mobile apps is pretty fun if all you have to do is write css and javascript.  [Angular](https://angularjs.org/) makes it more fun, and [Generator M](https://github.com/mwaylabs/generator-m) gets us off the ground [pdq](http://www.merriam-webster.com/dictionary/pdq).

*Note* I assume you know Angular and aren't afraid of yeoman generators.

### Setting up

1. Install [Generator M](https://github.com/mwaylabs/generator-m)
2. Make a new directory and generate app scaffolding within that.  I like 'tabs' personally.
3. Tada! *note* we aren't stopping here...

Just run `gulp watch` and it will pop open a local browser for you to see your new app in development (resize your browser to phone-size for best experience :)

### Change things

I'll give you a brief overview of where stuff is and you can change it a little before building your app.

`app/*` and the main module `app/main/*` are where all the cool stuff is.  Assets, controllers, templates, styles, etc.

The main index (i.e. app/index.html) loads in our javascript dependencies, application module, and lays out the header and tab based navigation.

Tab templates are in `app/main/templates` -- you start out with list, list details, debug, and tabs.

List is the homepage -- this and other routing is specififed by the angular ui config in main.js.  Note the 'otherwise' route.

More details about [where stuff is](https://github.com/mwaylabs/generator-m#file-structure) can be found in the Generator M docs.

### More Options

When making changes, note that there is a [grid system](http://ionicframework.com/docs/components/#grid) (and many other cool [components](http://ionicframework.com/docs/components/)) provided by Ionic.  You can also just use [Flexbox](http://www.smashingmagazine.com/2015/03/harnessing-flexbox-for-todays-web-apps/) to lay out your elements -- who really needs a grid system anymore?

Also, I highly encourage exploring the [testing integration](https://github.com/mwaylabs/generator-m#testing) and adding in [e2e tests with protractor](http://angular.github.io/protractor/#/) tests..

### Compiling and Installing

Add the platform of your choice to ionic, build for it, emulate it, and install it on your phone.  We're done :)

Here's some helpful commands:

----
{% highlight console %}
gulp build
ionic platform add android
ionic build android
ionic emulate android
{% endhighlight %}
----

Note: the www/ folder is built by gulp, and then ionic builds the apk to `platforms/android/build/outputs/apk/android-debug.apk`

## Summary

AngularJS can power a mobile app, as well as a web page.  Ionic integrates Angular and Cordova quite nicely.  Yeoman generators are what you want for speed of setup.

Testing is your friend.
