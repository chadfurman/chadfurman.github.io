---
layout: post
title:  "Test Driven Development of HTML Tags in Ionic"
date:   2015-08-31 01:10:15
categories: [javascript]
tags: [angular.js, javascript, directives, yeoman, generator m, tdd, ionic]
---

Directives are a feature of Angular, and thus Ionic, allowing us to make tags powered by JavaScript.

Last time, we talked about using Generator M to [quickly build up a mobile app]({& post_url 2015-08-29-ionic-isnt-it &}).

We will now use Test Driven Development and add a `directive` to our app.

### What we need

 1. [Scaffold an app with Ionic using Generator M](https://github.com/mwaylabs/generator-m.git)
 2. Some knowledge on [testing angular directives](http://newtriks.com/2013/04/26/how-to-test-an-angularjs-directive/)

### Setting up

Make sure you've installed the testing pre-reqs:

-----
{% highlight console %}
$ sudo npm install -g generator-karma karma jasmine-node karma-jasmine
$ cd project
$ bower install --save-dev angular-mocks
$ yo karma
{% endhighlight %}
-----

Karma generator sets up test/karma.conf.js -- in here we'll want to make the following changes:

-----
{% highlight javascript %}
config.set({
  // ...
  basePath: '../app',
  // ...
  files: [
      'bower_components/angular/angular.js',
      'bower_components/angular-mocks/angular-mocks.js',
      'bower_components/angular-animate/angular-animate.js',
      'bower_components/angular-sanitize/angular-sanitize.js',
      'bower_components/angular-ui-router/release/angular-ui-router.js',
      'bower_components/ionic/js/angular/main.js',
      'bower_components/ngCordova/dist/ng-cordova.js',
      '*.js',
      'main/*.js',
      'main/**/*.js',
      'main/**/*.html',
      '../test/karma/*.js'
  ],
  // ...
})
{% endhighlight %}
-----

Now Karma can find everything it's looking for

Run the tests.

`karma start test/karma.conf.js`

Without any tests, you should see a notice about `0 out of 0` tests passing.  Good.

### Writing our first test

-----
This in a file at `project/test/karma/sound-button.js`:
{% highlight javascript %}
'use strict';

describe('Directive: soundButton', function() {
  var element,
    scope;

  var audioMock = {
    play: function () { return true; },
    pause: function () { return true; }
  };

  beforeEach(module('main'));
  beforeEach(inject(function($rootScope, $compile) {
    window.Audio = function(src) {
      audioMock.src = src;
      return audioMock;
    };

    element = angular.element('<div class="well span6">' +
      '<h3>Sounds:</h3>' +
      '<sound-button sound="{{sound}}">' +
      '</sound-button></div>');

    scope = $rootScope;
    scope.sound = 'nobell.mp3';

    $compile(element)(scope);
    scope.$digest();
  }));

  it("should play and pause on click", function() {
    spyOn(audioMock, 'play');
    spyOn(audioMock, 'pause');

    angular.forEach(element.find('sound-button'), function(e) {
      expect(audioMock.src).toBe(e.getAttribute('sound'));

      var ngElement = angular.element(e);
      ngElement.triggerHandler('click');
      expect(audioMock.play).toHaveBeenCalled();
      ngElement.triggerHandler('click');
      expect(audioMock.pause).toHaveBeenCalled();
      expect(audioMock.currentTime).toBe(0);
    });
  });
});
{% endhighlight %}
-----

### But what does it do?

The test that we've just written should be failing.  You can check by re-running the `karma start test/karma.conf.js` command.

Taking a look inside, we see a mock object:

----
{% highlight javascript %}
  var audioMock = {
    play: function () { return true; },
    pause: function () { return true; }
  };
{% endhighlight %}
----

The audioMock object is our gateway into the directive.  It allows us to make sure methods are being called.  In this case, we want to be certain the audio is started and stopped.

The `beforeEach()` commands run, well, before each test.

----
{% highlight javascript %}
  beforeEach(module('main'));
  beforeEach(inject(function($rootScope, $compile) {
    window.Audio = function(src) {
      audioMock.src = src;
      return audioMock;
    };

    element = angular.element('<div class="well span6">' +
      '<h3>Sounds:</h3>' +
      '<sound-button sound="{{sound}}">' +
      '</sound-button></div>');

    scope = $rootScope;
    scope.sound = 'nobell.mp3';

    $compile(element)(scope);
    scope.$digest();
  }));
{% endhighlight %}
----

In this case, `element` and `scope` get compiled together to render our `sound-button` directive.

we're using the module() and inject() helpers that comes with Angular mocks.  Additionally, we're defining the Audio object to create our mocks -- primarily because Jasmine doesn't know what the Audio tag is.

Note that our constructor for window.Audio binds SRC onto the audio mock.  This is less than ideal -- we can only have one active mock at a time, unless we set up an array which we aren't going to do.

Then we get to our actual test:

----
{% highlight javascript %}
  it("should play and pause on click", function() {
    spyOn(audioMock, 'play');
    spyOn(audioMock, 'pause');

    angular.forEach(element.find('sound-button'), function(e) {
      expect(audioMock.src).toBe(e.getAttribute('sound'));

      var ngElement = angular.element(e);
      ngElement.triggerHandler('click');
      expect(audioMock.play).toHaveBeenCalled();
      ngElement.triggerHandler('click');
      expect(audioMock.pause).toHaveBeenCalled();
      expect(audioMock.currentTime).toBe(0);
    });
  });
{% endhighlight %}

----

This sets up our spies on the mock audio object, and then iterates over each sound-button element.  During each iteration, we check to make sure that clicking the element triggers play and pause events respecitvely, and that the second event is actually a stop command (i.e. sets time to 0).

### Passing the test

We actually kinda want to write a directive, right?  That was the whole idea.

This directive below should make the test pass.

----
This file belongs in `app/main/directives/sound-button.js`
{% highlight javascript %}
'use strict';

angular.module('main')
.directive('soundButton', function () {
  return {
    template: '<div class="sound-button button button-positive"><div class="button-text">Play</div></div>',
    restrict: 'E',
    link: function postLink (scope, element, attrs) {
      var sound = new Audio(attrs.hasOwnProperty('sound') ? attrs.sound : '');
      var buttonTextElement = element[0].firstChild.firstChild;

      sound.loop = attrs.hasOwnProperty('loop');

      var hideStop = function() {
        buttonTextElement.innerHTML = '';
        element.off('click', stopHandler);
      };

      var showStop = function() {
        buttonTextElement.innerHTML = 'Stop';
        element.on('click', stopHandler);
      };

      var hidePlay = function() {
        buttonTextElement.innerHtml = '';
        element.off('click', playHandler);
      };

      var showPlay = function() {
        buttonTextElement.innerHTML = 'Play';
        element.on('click', playHandler);
      };

      var stopHandler = function() {
        sound.pause();
        sound.currentTime = 0;
        hideStop();
        showPlay();
      };

      var playHandler = function() {
        sound.play();
        hidePlay();
        showStop();
      };
      element.on('click', playHandler);
    }
  };
});
{% endhighlight %}
----

Basically, we define a template inside the directive, restrict the directive to element bindings (i.e. `<sound-button>`), setup the audio event, and attach click handlers.

The click handlers play/pause as well as update the UI.  We could have added CSS animations, but that's a-whole-nother 1am blog post.

Running the tests again should give you an all-green.

### Conclusion
Karma is powerful.  It wasn't too hard to attach Karma to our Ionic application (once you get the paths right).

The power to write tests for directives, complete with click handling, is yours!
