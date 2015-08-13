---
layout: post
title:  "Sails Javascript REST API and CRUD"
date:   2015-08-12 22:03:15
categories: [programming]
tags: [angular.js, angular2, javascript, javascript frameworks, ES6, ECMA2015]
---
Dynamic content is an important part of most applications.  This content needs to be created, edited, and retrieved for display.  Let's build a REST API in Sails for basic CRUD.

### Getting started
Let's start by following the [Sails.js getting started guide](http://sailsjs.org/get-started).

-----
{% highlight console %}
$ sudo npm -g install sails
{% endhighlight %}
-----

This will install the command-line tool for building Sails.js applications, so let's build one with the new command-line tool!

-----
{% highlight console %}
$ sails new cms
$ cd cms
{% endhighlight %}
-----

Looks like there's some other stuff on the getting started guide that we just don't need -- installing node, etc.  So we're done here.  Moving on.

Now we need to get a basic REST API going.  We're going to manage widget factory content.  This could include many things, but for now let's just say it's staff members that we're managing at the factory.

So we need to build a staff member CMS page, ultimately, and currently need staff endpoints in our CMS.  Anyone who has built a REST API before knows that we're going to need to utilize three things atleast here: a route, a model, and a controller.  Saving you [some](http://sailsjs.org/documentation/concepts/routes) [light](http://sailsjs.org/documentation/concepts/models-and-orm) [reading](http://sailsjs.org/documentation/concepts/controllers), I will tell you that we're going to be using the scaffolding to get off the ground quickly.

### What is a model?
If you want to talk to the computer about people, you have to tell the computer the boundaries that define the people you're talking about.  That's called modeling your data.

A model is an object which "models" the data, providing methods for interacting with databases.

### Generating a Model
Yeah, we're not going to code a model.  Who has time for that?  I mean, sure it's not that hard.  Might take 15 minutes.  But [how about 15 seconds](http://sailsjs.org/documentation/reference/command-line-interface/sails-generate#?sails-generate-model-foo-attribute-1-type-1-attribute-2-type-2)?

I just need to know the fields I want my staffer to have.  Let's just give them a name for now, and then create one in the REPL.

-----
{% highlight console %}
$ sails generate model staff name:string
$ sails console
sails> Staff.create({name:'Staffer One'})
{% endhighlight %}
-----

### What is a route?
A route is the path between the URL and the code that gets executed.  Skip the next paragraph if you intuitively understood what I just said.

When we go to http://localhost/staff, sails will map /staff to a Staff Controller, and since no other information is provided then the index method of the StaffController (StaffController::index()) is what will be executed and control (heh, 'controller') what displays to the user.

### Routes in Sails.js
From the [Sails.js route documentation](http://sailsjs.org/documentation/concepts/routes), we are directed to `config/routes.js` where we can create routes manually like so:

-----
{% highlight javascript %}
// config/routes.js
module.exports.routes = {
  'get /staff': 'StaffController.index'
}
{% endhighlight %}
-----

Normal browser requests to http://localhost/staff will execute the StaffController::index() method.

But like, I don't want to do each route.  I also don't really have time to make controller methods.  Can't we [automate things a little](http://sailsjs.org/documentation/concepts/routes#?automatic-routes)?

### Automatic API with Blueprint
Starting with the [blueprint documentation](http://sailsjs.org/documentation/reference/blueprint-api), note that there are three types of blueprint routes.  We're most interested in the REST routes and should [disable](http://sailsjs.org/documentation/reference/configuration/sails-config-blueprints) atleast the shortcut routes.

#### REST
These are the most magical and provide out-of-the-box JSON API with support for Pagination, filtering, etc. given only a controller and a model with the same name.

wow.  Gotta try that.

### Trying it

-----
{% highlight console %}
$ sails generate controller staff
$ sails lift
{% endhighlight %}
-----

Assuming you didn't delete any data when Sails asked you what to do, then navigating to http://localhost:1337/staff in your browser should give you a staffer in JSON.  If you deleted data, no worries, just close down sails and create another staffer in the repl.

Misson complete.
