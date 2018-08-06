---
layout: post
title:  "How GraphQL Saved My Project"
date:   2018-08-03 21:32:00
categories: [development]
tags: [graphql, web application development, enterprise applications, rest]
---

# How GraphQL Saved My Project
The title of this article could just as easily be *“Why I never want to work on another REST API ever again”*, or perhaps more mildly *“Why GraphQL and Why Not REST?”*  

I am writing this on the heels of a second read-through of [“REST vs GraphQL: A Critical Review”](https://blog.goodapi.co/rest-vs-graphql-a-critical-review-5f77392658e7) (found here: https://blog.goodapi.co/rest-vs-graphql-a-critical-review-5f77392658e7) and felt that Z’s article is minimizing the benefits of GraphQL in a way that makes REST look like it can still be a plausible solution to enterprise business needs.

It cannot.  

Expressed with more verbosity, REST is not a pattern you should use to build new APIs unless that API is both built and maintained by the same developer forever and you never expect to grow/change the team or the code base.  And even then...

Let’s talk about my experiences working with REST and why, if you can help it, you should chose GraphQL instead.

## Who am I?

I am what some might call a senior enterprise web application developer.  Having worked in agencies for the last 15 years of my life, I know what it takes to drive a project from start to finish.  I was contracting my web development expertise to friends and family at 12 years old.  I’ve scoped projects, had to work on projects scoped by others, and I have seen many web applications rise and fall for the same reasons.

## Why REST Causes Web Application Projects to Fail

Businesses are all about solving their problems quickly, with as little money as possible, and in a way that provides lasting relief.  Thus, web application development projects are both time-constrained and resource-constrained in most cases.  

Good developers which deliver quality solutions quickly are both hard to find and expensive to retain.  To ship a quality web app before the money runs out, you need (among other things) an API that does not require an army of talented engineers to build, maintain, and consume.

A REST API requires developers to go down one of a couple paths: expose and consume resources across multiple endpoints (‘optionally’ preserving that information client-side while navigating an SPA), or consume different endpoints with giant JSON responses for each page.  

The first path results in lots of network overhead and lots of skilled developer time exposing, documenting, and optimally consuming the different resource endpoints; some of the best developers I know who have since gone on to work for Google, Amazon, etc. still get this wrong.  The second path results in lots of network overhead and more developer time spent maintaining different endpoints for each page which often return vastly similar data structures.  

Both paths result in versioning requirements if you want to re-architect your API and support existing clients at the same time.  None of this even talks about the different client-side tools for consuming REST APIs / managing state, and the multiple headaches associated with them (Redux ~~sagas/thunks/actions/reducers~~ boilerplate, Angular ~~1 2 4 5 6~~ Dart, jQuery?, etc…)

At the end of the day, the documentation is still out of sync, the project is still behind schedule, and the app still needs 5 more endpoints.  Atleast.  

## How GraphQL APIs Deliver

For this, I would like to share a story with you -- a brief story based on real-life, and to which you can likely relate.

Having just inherited a legacy codebase (read: lack of tests, lack of documentation, etc), I was tasked on “getting the thing to work.”  Basically, a core component of the web application (admittedly unrelated to REST) had unfortunately under-delivered and failed to meet the business needs of the company.  So, here I was, the sole-replacement for a team of 10+.  I became project manager, product owner, lead developer, and designer.

In spite of this, over the next few months I did manage to get the thing to work.  However, a new challenge lurked just over the horizon.  With great success comes new responsibility, and now we were looking at a target market with very high-stakes security concerns.  Given the legacy nature of the existing codebase, the three different files for password reset functionality (all of which were insecure and only one of which was in use) and a myriad of other side-effects of REST-based development, I knew we were in for a full rewrite to meet this goal.

##### Enter GraphQL.

Having done some research after a friend pointed me towards what-was-then-called-PostgraphQL-now-called-Postgraphile, I knew that I could quickly spin-up an interactively self-documenting API if all I did was build a robust Postgres schema complete with row-level security and role-based access control.  I also knew that Relay Modern offered declarative data dependencies, that declarative data dependencies would cut my development time in half or more, and that I needed them in my life and in my project were I to be successful.

If GraphQL is the raw ingredients, Postgraphile was the oven and Relay Modern was the delicious meal that was served on my golden web development platter.  I now had a way to create pristine data layer complete with JWT-based authentication from my database and sprinkle some NodeJS schema extensions here or there like little candy sprinkles that make the thing sell.

### Stop Talking About Food!  How Did GraphQL Save The Project?

Alright, thanks for hanging in there for that.  The existing web app was based on Angular 1 and MongoDB.  Having succeeded in making the existing thing work, I had the leverage necessary to sell a full re-write to the client (they wanted a re-design, several features that required extensive refactoring, and this all on top of the up-coming security needs.)  With the re-write green-lit, I knew for reasons I hope are obvious (i.e. stability, maintainability, etc) that I was not going to do it in Angular and Mongo.  

I went with Relay Modern and Postgraphile.

Combined with the Sqitch migration tool, some migration conventions, and some NodeJS schema extensions for things databases don’t do well, Postgraphile has been more maintainable than any REST API I have worked with.  I was able to re-implement the application as an MVP, preserving 80% of the existing functionality, over three months.  

Luckily, I was no longer alone.  My original success getting the thing to work opened the door for investment money and I got a full-time frontend developer added to my team.  This developer had originally been a mobile developer, having worked with both native and React Native applications.  

He may not have been an experienced website developer, but he was certainly willing to learn and able enough to move mountains.  Relay Modern enabled this developer to re-implement our login / signup flows, video room creation flow, and even the video room itself in under three months while I supported him by not only building out our API but also building out the frontend data layer including authentication and routing.

Two developers did a better job in three months than 8 developers did in a year.  The project has since grown to a total of 7 (including myself), more investors have been added, and real-life enterprise clients are using (and paying for) the site as I type this to you.  

And about security?   Multiple (read: three so far) external auditors gave us very positive reports.    

*That is how GraphQL saved my project.*

### Arguments Against GraphQL, Counter-Arguments, and Concessions

##### Against: GraphQL is best-suited for when you have one client that you control
**Counter**: GraphQL is used by multiple entities to expose external APIs that are easily consumed by clients.  Yelp, Shopify, Github, and more: https://github.com/APIs-guru/graphql-apis/blob/master/README.md

##### Against: GraphQL is not performant
**Counter**: Postgraphile handles 500-1500 (depending on query complexity) requests a second.  Shopify uses Ruby-on-Rails and serves 80,000 request per second.  At the end of the day, the conversation about scaling comes down to cost.  How much do the EC2 instances in the autoscaling group, the RDS read-only replications etc. cost compared to the cost savings in developer resources?  I’d say 1500 requests per second for simple queries and 500 requests per second for more complex queries (with a single server) is more than adequate to help a start-up meet their business goals.

If you are concerned about network traffic associated with a GraphQL request, or about server-load in having to parse strings associated with GraphQL requests, check out persistent queries, reverse proxy caches, and API key request limits.


##### Against: GraphQL is not cacheable
**Concession**: I assume you are talking about HATEOAS and using hyper-links to resources then letting the browser make extra requests to those resources and cache the response.  The browser then uses the cached response on every subsequent request.

Yes, having one GraphQL endpoint that returns a different response based on the query does toss out this caching mechanism.  Yes, the server throwing up a proxy cache is not the same thing (though, technically, is caching).

**Counter**: That said, Relay Modern provides a very beautiful client-side state management system complete with a caching layer.  It does not have to request the same query when you navigate between pages of your SPA because it already has all the data it needs in the client-side cache.

##### Against: GraphQL does not scale / neglects the problems of the distributed system
**Counter**: A GraphQL API can serve as a consistent gateway to microservices.  Alternatively, everytime you change the microservice API then all of your clients have to be updated.  See here for more discussion: https://stackoverflow.com/questions/38071714/graphql-and-microservice-architecture#38079681 

Also, if you are talking about requests per second, see the response above to the notion that GraphQL is not performant (i.e. upwards of 1500 RPS on a single server is not too shabby)

##### Against: GraphQL does not support Code-on-Demand
**Counter**: A GraphQL endpoint can return anything you ask it to.  It can return XML, JSON, or binary.  You can hook the express response object and overwrite it completely, returning early from execution and spit out a new mime-type if you want to.  However, the best way is likely to just return JSON in response to a query and, in that JSON, link to where the application can download the code-on-demand. 


##### Against: GraphQL does not provide a Uniform Interface
Ignoring for a second that most REST APIs do not provide a uniform interface, the GraphQL fields and mutation names can be uniform within an API by enforcing naming conventions.  This is not the same as having “.documents/10 “, admittedly,  However, the interactive GraphiQL documentation that is easily searchable and maintainable more than offsets this lack of a “Uniform Interface”.  Now, that said, most REST APIs do not have a Uniform Interface model for reasons layed out in “Why REST Causes Web Application Projects to Fail”


##### Against: GraphQL docs are an arcane gql schema file
Please take a moment to play with the [GraphiQL Explorer of GitHub](https://developer.github.com/v4/explorer/) (here: https://developer.github.com/v4/explorer/) or of any of the [other APIs listed on APIs-guru github](https://github.com/APIs-guru/graphql-apis/blob/master/README.md) (here: https://github.com/APIs-guru/graphql-apis/blob/master/README.md)

Now that you’ve done that, note that this tool is automagical in that the entire documentation tab (over in the right corner there) is auto-generated from field names and descriptions you put in your code.  

Okay, now go take a look at your Apiary documentation and tell me you don’t want GraphiQL instead...

##### Against: GraphQL requires bikeshedding (content negotiation, network errors, caching), etc. etc.
**Concession**: So, I’ll give you this one. How’s that?  You can build a REST API following the rigorous standards of REST APIs, make sure that you use HATEOAS properly to fully take advantage of HTTP caching, use HTTP Error Codes in all of your responses, and live life happily ever after.  

**Counter**: Or, you could document a set of conventions and enforce them during code review.  For caching you can rely on Relay Modern client-side and optionally even throw up a reverse proxy server.  Then you can have GraphiQL autogenerate your docs and more (because REST has lots of bike-shedding associated with documentation tools).  

You can pick.  I think you know what I would pick given the same choice :)

##### Against: GraphQL is not mature
**Counter**: See my rebuttal below to the criticism that “GraphQL is not Enterprise-ready”.  GraphQL has matured nicely since 2015.

##### Against: GraphQL does not have any printed books
Really?  You put this in a picture of a table as a comparison point?  In June of 2018, there were certainly books published on GraphQL if you cared to look.  A quick google search for “GraphQL books” straight up disproves this.  

##### Against: GraphQL is not enterprise-ready
Tell that to Yelp, Shopify, Facebook, and many many more (https://medium.com/@Akryum/oh-no-another-bullet-points-under-argumented-article-with-misleading-comparison-charts-d0dca01d2c04).  Note, also, that GraphQL continues to grow in the public-sphere and that Facebook as since relinquished its patents on the tech to the community at large.  

I’ve also seen Enterprises using PHP 4 when PHP 7 just came out, using CakePHP 1 when the newest version was 3+, and interfacing with it all in jQuery.  The effects of an unmaintainable REST API can be felt many years in the future.

Enterprises are surprisingly robust organizations that really only care about solving their business goals with the most cost-effective (secure, efficient, effective) method.  I believe that the most cost effective method is to use GraphQL to empower developers to iterate on and deliver maintainable code faster.  

##### Against: GraphQL couples the client to the server at development time
*AKA “application state is not driven by the server”*

**Counter**: No matter what, the client relies on the server to spit out data in a consistent fashion.  Assuming again you are referencing HATEOAS then note that HATEOAS does not require the API to implement it in a specific way -- the API could elect to change the way HATEOAS is implemented. 

Moreover, the power of Relay Modern and declarative data dependencies makes it empowering and beneficial to control application state client-side.

##### Against: GraphQL query optimization is hard
**Counter**: Premature optimization is a death knell for software projects.

GraphQL is easy to setup, maintain, and consume.  In the long run, you should only optimize when you have bottlenecks.  And once you have performance bottlenecks then you know where your time is best spent in optimizations.  The time you save in constructing your client and server is in no way lost on late-stage optimizations.  

Late-stage optimizations are par-for-the-course in software development, and optimizing when you have a problem is much better than tossing out a way to speed up business solutions and possibly never solving the problem as a result.




