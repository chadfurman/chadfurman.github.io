---
layout: post
title:  "npm install angular2"
date:   2015-08-09 18:50:15
categories: [programming]
tags: [angular.js, angular2, javascript, javascript frameworks, ES6, ECMA2015]
---
Sometimes, learning something new is daunting.  Angular2 uses ECMA2015 -- two new technologies at once for me.  Might as well get started!

-------
{% highlight console %}
$ mkdir cms
$ cd cms
$ npm init
$ npm install angular2 --save-dep
{% endhighlight %}
-------

Absolutely not going to worry about proper HTML headings here.  Just going to make a file, load in our angular2, and see if we can get some stuff going.

-------
{% highlight console %}
$ vim index.html
{% endhighlight %}
-------
{% highlight html %}
<!-- cms/index.html -->
<html>
  <head>
    <title>Because title.</title>
    <script type="text/javascript" src="node_modules/angular2/angular2.js"></script>
    <script type="text/javascript" src="start.js"></script>
  </head>
  <body>
    Because bodies are nice.
  </body>
</html>
{% endhighlight %}
-------

ooh shivers.  Holding off on start.js for now.  Alright.  So we should be able to... do something.  Now what are we supposed to do?  Maybe now is a good time to consult [the angular docs](https://angular.io/docs/js/latest/index.html).  Let's [start at the top](https://angular.io/docs/js/latest/quickstart.html).

> The goal of this quickstart is to write a component in TypeScript that prints a string.

Uh.  TypeScript?  Okay, wait.  We're not going to do that.  Let's translate the typescript to Javascript in real-time and "get-what-we-pay-for" so-to-speak.  Should be good.

Oh, looks like the 5-minute quick-start isn't going to be useful, then.  Okay, switching to [the step-by-step guide](https://angular.io/docs/js/latest/guide/setup.html).

> Although we work through the examples in TypeScript, you can also use regular ES5. Click the ES5 link in any code box to see the ES5 JavaScript version.

Winning.  Wait, ES5?  Why?  *sigh*  Let's find an ES6 guide because, well, that's what we're doing right?

How about this ['Writing AngularJS Apps Using ES6'](http://www.sitepoint.com/writing-angularjs-apps-using-es6/) guide?

> As we will be using ES6 to write the front-end part of the application, we need a transpiler to make the ES6 features understandable for all the browsers. We will be using traceur client side library to compile ES6 script on the fly and run it on the browser.

Alright, let's [get traceur setup](https://github.com/google/traceur-compiler/wiki/Getting-Started).  We'll clone it down and compile.

------
{% highlight console %}
$ cd ../
$ git clone git@github.com:google/traceur-compiler.git
$ cd traceur-compiler
$ npm install
$ make
{% endhighlight %}
------

Guessing we'll be including that traceur-compiler/bin/traceur.js file in our app in a sec.

Let's continue the guide.


> In the sample code...

The sample code?  Where?  They're talking about sample.. oh... it's all the way down at the bottom.  [Sample code here](https://github.com/sravikiran/Angular-ES6-BookShelf).

[Looks like](https://github.com/sravikiran/Angular-ES6-BookShelf/blob/master/Index.html#L56) first we include angular2, then angular components we want to use (we don't want to use any, if we can avoid it, to keep this thing simple), then traceur et al.

Sure... let's try it.  Noticing that they have all their stuff in bower_components and, well, we're just not doing that...

--------
{% highlight console %}
$ cp ../traceur-compiler/bin/traceur.js ./
{% endhighlight %}
--------
{% highlight html %}
<!-- cms/index.html -->
...
<script src="node_modules/angular2/angular2.js"></script>
<script src="traceur.js"></script>
<script>
    traceur.options.experimental = true;
    new traceur.WebPageTranscoder(document.location.href).run();
</script>
<script src="start.js"></script>
...
{% endhighlight %}
--------

Now I'm not sure if we should put this in the header or the footer.  Might as well put it in the footer until it causes an issue and then move it.  This way, all elements will be loaded.  We'll hide things by default and show them, as opposed to hiding them with Javascript on load.  It'll be okay.  Don't worry.

Hmmm looks like this thing is called "bookShelf" -- I'd of thought it'd be in title case. Let's drop that snippet in start.js

-----
{% highlight javascript %}
// start.js
import { default as bookShelfModule} from './ES6/bookShelf.main';
angular.bootstrap(document, [bookShelfModule]);
{% endhighlight %}
-----

Okay, what's that do?  bookShelfModule is the AngularJS module which we get into later, fine.  Following along for now, Site Point!  Next time, we gain some control with Controllers!

Feel the power.
