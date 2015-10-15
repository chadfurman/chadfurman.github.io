---
layout: post
title:  "Features with Value: Behavior Driven Development"
date:   2015-10-14 23:22:10
categories: [programming]
tags: [bdd, cucumber.js, gherkin, project planning]
---

Delivering a fully end-to-end tested product which verifiably produces value might sound like a bit of a dream.  Behavior driven development ensures that each feature is formally defined (think automated testing) and directly related to a business goal.

The steps are as follows:
1) Start with a goal
2) Identify actors and what they do that relates them to the goal
3) Identify the behaviors which the actors must perform
5) Identify features which enable the behavior and assess the value/complexity of each
6) Write feature specifications with examples to remove ambiguity

Interesting to point out that we won't actually talk about programming directly in this article.  Also interesting: these [slides on BDD](https://prezi.com/_apkxraabzzz/bdd/)

---------------

## S.M.A.R.T. Goals
    Specific, measurable, attainable, relevant, and time-bound.

    Consider the following:

    `100 purple widget sales through the website by the end of December 2015`
    vs
    `Sell a good number of widgets through the site`

    The first goal makes it so you feel good about selling 101 purple widgets in November, where the second goal is a little less certain.

## Selecting your Actors
    Actors are people with behaviors that affect the goal.  

    Sticking with our `purple widgets` example, we have: 
    + `buyers` of the widgets
    + site `admins`
    + `warehouse` distribution center
    + `finance` department

    Buyers, admins, warehouse, and finance.

## Valuable Behaviors
    What is the behavior of the buyers that relate them to the goal?  Well, they buy things.  In order to buy things, they must see things and be able to click on them.
    We might describe the behaviors of our actors as follows:
    Buyers:
    + See three purple widgets featured on homepage
    + Browse featured widgets
    + Navigate between widget categories
    + See widget thumbnails on category view
    + See widget details

    Admins:
    + Login on a private page
    + add widgets
    + remove widgets
    + update widgets
    + moderate comments

    Warehouse:
    + receive shipment notification

    Finance:
    + receive payment information
    + confirm payment information
    + process payment
    + approve order

## Features
    This is similar to behaviors, except the behavior is what the actor does and the feature is what the application does.

    A `buyer` needs to see three purple widgets on their homepage.

    Possible features:
    + featured widget box on homepage
    + display three featured widgets on homepage
    + featured widget box stands out with a highlight
    + featured widget box is at the top of the main content area

    For each of these features, I would estiamte the value to be medium or high and the complexity to be relatively low.  These are low-hanging fruit which can be safely reserved for later in the project if need be.

    A more difficult behavior might be for the `warehouse` actor.  The `warehouse` needs to receive shipment notifications, which is a bit involved:
    + user order must be approved 
        + high value, high complexity
    + approved orders must be parsed into ASCII flat file format 
        + high value, medium complexity
    + flat file must be FTPd to `warehouse` automatically 
        + medium value, medium complexity
    + FTP credentials are not in the repo 
        + high value, low complexity

    
    The goal here is to start to communicate what the behavior means to the developer.  Communication goes both ways, and this gives a framework for prioritizing development tasks as well as describing the tasks that must be performed.

### Feature specification language: Gherkin
    Feature specifications are the formal binding of the process.  Starting with features like `featured widget box on the homepage` and `user order must be approved`, we write `Scenarios` and `steps` which explicitly define each feature.

    By writing our feature specifications in [Gherkin](https://cucumber.io/docs/reference), we effectively bridge the gap between business language and programmer logic in a well-defined manner.  This is accomplished through example.

    [More on Gherkin](http://docs.behat.org/en/v2.5/guides/1.gherkin.html) can be found in many places.  An effective summary might be:

    + `Feature` 
        + a sentence describing the feature, and then three sentences explaining why
        + `In order to <achieve value>`
        + `As <an actor>`
        + `I want to gain <outcome that supports the goal>`
    + `Scenario`
        + A one-sentence use-case of the feature
        + `First-time site user buys 2 purple widgets`
    + Steps: `Given`, `When`, `Then`, `And`, `But`
        + These are conventions; all steps may be treated the same by your BDD framework
        + Givens setup pre-conditions
        + Whens perform actions
        + Thens check for outcomes
        + Ands continue (i.e. given... and...)
        + Buts are for exclusion (then this but not this)
    + `Background`
        + A set of steps that applies to a group of scenarios
    + `Scenario Outline`
        + A template for generating scenarios based on example data
    + `Examples
        + tabular data used for populating the scenario outline templates

    This might look like the following:

```
Feature: Multiple site support
  In order to generate traffic
  Admins should be able to
  Post to internal blogs

  Background:
    Given a global administrator named "Greg"
      And a blog named "Greg's anti-tax rants"
      And a customer named "Wilson"
      And a blog named "Expensive Therapy" owned by "Wilson"

  Scenario: Wilson posts to his own blog
    Given I am logged in as Wilson
     When I try to post to "Expensive Therapy"
     Then I should see "Your article was published."

  Scenario: Greg posts to a client's blog
    Given I am logged in as Greg
     When I try to post to "Expensive Therapy"
     Then I should see "Your article was published."

  Scenario Outline: Eating while blogging
    Given there are <start> cucumbers
     When I eat <eat> cucumbers
     Then I should have <left> cucumbers
  
    Examples:
      | start | eat | left |
      |  12   |  5  |  7   |
      |  20   |  5  |  15  |
```

#### Steps
    We're only going to talk in detail about steps.  Properly written step definitions translate directly into automated end-to-end tests.

```
Given I am on the homepage
 When I click on ".featured .purple-widget[data-id='1'] .buy"
 Then I should see a ".confirmation-dialog" element
  And I should see a "button.confirm" element
  And I should see a "button.cancel" element
```

    The words I have chosen match up against the [Cucumber-Mink Step Dictionary](cucumber-mink.js.org/steps) -- this saves us a lot of time.

#### Step definitions
    Cucumber-mink step dictionary saves us from having to write the javascript which validates that UI elements are present.  If you've never done testing before and that sounds tedious, rejoice!  With over 350 unique steps, I only had to manually define maybe 20, and these were around database interaction and uncommon UI.

    If you were to write your own step definition in Cucumber.js, it might look as follows:
```
this.Then('I should see "$title" as the page title', function (title, callback) {
  // the above string is converted to the following Regexp by Cucumber:
  // /^I should see "([^"]*)" as the page title$/

  var pageTitle = this.browser.text('title');
  if (title === pageTitle) {
    callback();
  } else {
    callback(new Error("Expected to be on page with title " + title));
  }
});
```

## Summary
1) Behavior Driven Development is a process that starts with a goal and ends with Gherkin.
2) All features added to a project are tied to business goals through desired user behavior
3) All features are formally specified
4) The formal specifications become automated end-to-end test

Use BDD on your project to end up with a fully end-to-end tested deliverable where every feature adds value.

Good luck, best wishes, happy hacking :)
