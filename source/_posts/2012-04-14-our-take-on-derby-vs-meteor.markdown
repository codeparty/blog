---
layout: post
title: "Our take on Derby vs. Meteor"
date: 2012-04-14 12:14
comments: true
categories: 
---

We have received a number of requests for a comparison. I'd like to thank [Nick Retallack](http://nickretallack.com/), who already did a great job summarizing a lot of these points on our [Google Group](http://groups.google.com/group/derbyjs). Many of his observations are included here.

## A bit of the origin story

First of all, I should mention that Brian and I first met the Meteor team last November when we demoed an early version of Derby at the [Keeping it Realtime Conference](http://krtconf.com/) hosted by [&yet](http://andyet.net/). When we met, the [Meteor](http://meteor.com/) team had already started on their framework, and the similarities were obvious. Some time before, I had also met with David Greenspan (who recently joined Meteor) to pick his brain and learn more about his experience writing [Etherpad](http://en.wikipedia.org/wiki/Etherpad). Our two teams like each other, we've been keeping in touch, and we have all learned a lot from each other.

Our teams share a similar vision for a world where all applications are realtime and collaborative by default. We as well as a number of other other developers all had a revelation about a year and a half ago---the way that web apps are currently built makes it painfully difficult to create the best user experience, where data dynamically update everywhere.

Brian and I first discussed this vision with each other a year ago. I was coming from working as a Product Manager on the Google Search team, and he had been working on a number of open source Node.js projects including [Mongoose](http://mongoosejs.com/) and [everyauth](http://everyauth.com/). I was interested in writing a framework, because I believed it was the best way to write an app with the kind of performance that I desired, and Brian felt that the Node.js community needed an easy to use framework for those developers who preferred tools like Rails and PHP.

## The Holy Grail: server and client code sharing

From Google Search, I learned that in order to have a responsive web app, it is critical to both render pages on the server and in the browser. Google can afford to do this, because it has so many engineers and speed is of paramount importance to search. To achieve fast page loads as well as fast page updates, Google implements pretty much the entire Search front-end in C++ as well as JavaScript. They have been working on a number of projects to make this easier over the past couple of years, but needless to say, such a process is too expensive and slow for a startup or a complex app.

Gmail, Twitter, and other sites rendered only in the client were painfully slow to load. Twitter went through the full circle on this---they started as a server-rendered Rails app, rewrote to render [everything in the client](http://engineering.twitter.com/2010/09/tech-behind-new-twittercom.html), and then realized it took their users 4 seconds to be able to read a 140 character message. Now, Twitter renders half the page in Scala on the server, and they render the other half in JavaScript in the client. This both makes it more difficult for them to maintain a code base in multiple languages and means that the least important items on the page pop-in a few seconds after page load. People are very sensitive to movement, so in effect, they are distracting their users with the least important information after every page load.

Thanks to the awesome re-invention of JavaScript performance starting with V8 and the ability to easily create JavaScript servers with Node.js, lots of developers are excited about the notion of using JavaScript for both server and client development. But despite finally using the same language, few Node.js applications to date deliver on the amazing ability to share code. Even with a common language, there are still a lot of issues including very different latency and connection stability, separate ways of dealing with URLs on the server and in the browser, the existence of a DOM in the browser and not on the server, and direct access to a filesystem only on the server.

## Let's get down to business!

So after all that, our teams decided to go in similar directions in some ways, but very different in others. Derby's goal is to make it possible for any developer to write apps that load as fast as a search engine, are as interactive as a document editor, and work offline. And to [quote Geoff](https://groups.google.com/forum/?fromgroups#!topic/derbyjs/pvtbLullSn4), Meteor's goal is to create "a 'mass-market' app platform that could be used for 90% of the sites on the web, and for that 90%, make web development faster and within reach of more people."

### GPL vs. MIT

Currently, Meteor is only available under a [GPL license](http://meteor.com/faq/how-is-meteor-licensed). This means that you must contact them and arrange for a commercial license if you do not wish to release your source under the GPL or another compatible license.

Derby, Racer, and all other components of our framework are released under the permissive MIT license. Thus, you can do pretty much anything you want with it (other than sue us), and we can't stop you or change our minds later.

I'm no lawyer, so don't take any of what I just said as legal advice; I don't have a crystal ball or anything. (Yes, a lawyer once told me to say that.)

### Meteor packages vs. npm

Meteor has created their own package system and means for distributing packages.

Node.js modules are clearly defined by the CommonJS format and the built-in module APIs. However, it does not include a means for distributing modules. In the early days, Node.js had a few competing package managers, but pretty much everyone has now finalized on using [npm](http://npmjs.org/). It is so universal, that npm is now distributed directly with the official Node.js binary distributions.

Frankly, this is the main thing that concerns me about Meteor. It is likely that many people trying Meteor will never have used Node.js before, and they won't appreciate the great benefits of distribution via npm. I and many other developers view npm as one of Node.js's core strengths, and I would be very sad to see it weakened by an incompatible package system.

Like pretty much every other Node.js project out there, we primarily distribute Derby, Racer, and all plugins for these projects as npm modules. We will continue to break out our projects into smaller modules so that they can be better reused by others. Perhaps you don't use Derby, but you need to parse some HTML. We had to write a simple and fast HTML parser for our templates to work, so you'll be able to use just that in any Node.js project. [Connect](http://www.senchalabs.org/connect/) is great example of a project that has a rich ecosystem of middleware designed to work with it that are all distributed via npm, and we hope to follow in the same pattern.

### Compatibility with other libraries

Meteor makes it possible to replace parts of it with other libraries. It is especially flexible when it comes to substituting in client-side libraries, though those libraries must first be wrapped as a Meteor package.

Meteor mostly manages the creation of a server in a custom way. This makes it easy to create and deploy a Meteor app to their hosting service, but it makes it much harder to use Meteor as a module of a more traditional Node.js server.

In contrast, Derby is just a normal npm module that you can add to any Node.js server. If you want to use any of the [8900 packages](http://search.npmjs.org/) in npm (at the moment), simply add them to your package.json file and `npm install` away!

Derby does not provide any sort of hosting solution, though there are lots of great hosting services for Node.js out there. We will provide better instructions for how to get Derby apps up and running quickly on a public server in the future.

[Racer](http://derbyjs.com/#introduction_to_racer) (the realtime engine powering Derby's models) is a separate module, and it can be used independently of Derby. You could hook pretty much any UI layer up to Racer and handle the methods that it emits as the model is updated. Racer is built on top of the very popular [Socket.IO](http://socket.io/) module, which you can use directly if you need to.

Derby also uses popular modules at its core, especially [Express](http://expressjs.com/) for routing and [Browserify](https://github.com/substack/node-browserify), which can bundle up most Node.js modules for the browser automatically.

### MongoDB API vs. Racer methods

Meteor takes the MongoDB API all the way to the browser. The MongoDB API is powerful and easy to use from JavaScript. It has also been around for some time, so there are well established patterns for using it do most things that an application might need to do. A lot of people seem to think this is a fun way to build apps, and it reduces need to understand multiple layers of abstraction. Some have expressed concerns over the security implications, but I think we should reserve judgment until the Meteor team has more time to address what they think is the best approach for authentication and authorization.

Racer has its own API based around a set of mutator methods and paths. This API maps pretty much 1:1 with any document store, including MongoDB. This has some pluses and minuses, but we believe it is the right choice for a few reasons:

#### Conflict resolution

Paths and methods map well to conflict detection techniques that we think will be one of the major benefits of using Racer. For now, our default mode is last-writer-wins, which is equivalent to how Meteor saves data. However, we have preliminary implementations of conflict resolution via Software Transactional Memory and Operational Transformation methods. Such techniques will make it possible to use a Derby app offline and then resync correctly. It will also make it possible to easily add features like Google-Docs-style realtime collaborative text editing in any text box.

#### Datastore portability

Our paths and methods are granular enough to take advantage of the capabilities of most datastores, but it is possible to switch from one datastore to another or to use them with multiple datastores simultaneously. Swapping in Riak, Postgres, CouchDB, or another service should be straightforward without modifying application code.

#### Efficient PubSub

Paths map well to PubSub, which is how we propagate changes in realtime. In contrast, Meteor's LiveMongo implementation simply writes to the database and polls it frequently for the data in use by every connected client. The advantage to polling is that it can support subscriptions to pretty much any kind of Mongo query, but we have implemented most queries and query PubSub without needing to poll the database. We have no real-world evidence yet, but we expect PubSub to scale much better than database polling.

#### API plugins

It will be possible to create plugins that act like datastores, even though they are communicating with a backend service or a 3rd party API. Database adapters are built on top of a system of routes that can be used in a custom manner.

### Server rendering and shared routes

Derby is one of the only frameworks designed to run all rendering and routing code on the server and in the browser. It's even possible to use Derby to render static pages that share templates with dynamic apps.

This means you get crazy fast page loads with no effort. Even for a simple client-rendered app, pages typically take a second or two to load and then be displayed to the user. Simple Derby apps can fire the onload event in less than 200ms. As apps get more complex, server rendering has an even more drastic effect.

Lots of big companies including Google, Amazon, and Facebook dedicate massive resources to optimizing page load time, because it directly translates into better conversion rates and more revenue. I died a little bit inside the day Gmail added a loading progress bar. Don't do that.

In addition, server-rendering is critical for search engine optimization, and it provides accessibility to screen readers and browsers without JavaScript.

Derby exposes [routes](http://derbyjs.com/#routes) as an Express middleware on the server, so you can use it alongside other server-only Express routes for tasks like uploading files, and you can even write multiple apps that handle different sets of routes on the same Express server. For example, you might write a separate admin app or a separate mobile app that doesn't load all of the code for a desktop browser app.

While Derby gives you all of the speed and accessibility advantages of a more traditional multi-page app, it also creates fully optimized single-page applications that can render any template or route in the browser. Client-side routes simply render any links that they can without doing a page refresh; you don't have to call methods on a special browser-side routing API. Just put a normal HTML link on the page, and Derby will render it client-side.

Meteor does not have these features yet, though they recently rewrote their templating engine to use strings so that it is now [possible for them to add](http://meteor.com/faq/can-meteor-serve-static-html).

### Model-view data bindings

Meteor uses a reactive functional programming approach to updating templates that could potentially work with any template engine. As a template is rendered, all of the data that is used to render that template is assumed to be an input to that chunk of the UI. Later, when that data changes, the template is re-rendered, and its output is compared with the DOM. Meteor then patches up the DOM to apply the minimum required changes. Effectively, every single variable and function in a template is bound. The big advantage to this approach is its simplicity and ability to support any template language.

In contrast, Derby has its own template language based on Handlebars. [Bindings](http://derbyjs.com/#bindings) are declared explicitly, and the HTML of the template is parsed in order to figure out which parts of the DOM need to be updated. Unlike Meteor's, Derby's bindings are two way; when form elements are bound, the model is automatically updated as users type in text inputs, check checkboxes, and select items in drop downs. In addition, it is possible to bind everything or to optimize the performance of templates by only binding what is necessary. Outputting non-bound data may also be preferred if a developer wishes to update the view via manual DOM manipulation in special cases.

### DOM event handlers

It is a relatively minor difference, but Meteor uses CSS selectors on specific templates, and Derby uses HTML attributes within templates to declare event handlers. [jQuery](http://api.jquery.com/category/events/) and [Backbone](http://documentcloud.github.com/backbone/#View-delegateEvents) have popularized the CSS selector method of scoping event handlers. [Knockout](http://knockoutjs.com/documentation/event-binding.html) and [Angular](http://docs.angularjs.org/#!/tutorial/step_10) use HTML attributes.

We prefer the [HTML markup approach](http://derbyjs.com/#dom_event_binding), because it is more work to figure out what CSS selector matches the correct elements, and it is reasonably likely that HTML structure will change and silently break CSS selectors. With the markup approach, it is less likely that function names will be changed inadvertently, and an error will be thrown when the function can no longer be found.

Derby also has an innovative approach to event bubbling that is more efficient and intuitive compared to typical event bubbling. Instead of bubbling up the entire DOM to the root documentElement for every event, Derby will stop bubbling once it finds an element that is bound to the event. Like with routes, bubbling can be continued by calling a `next()` method passed to the event handler.

### Fibers

Meteor uses the [Fibers](https://github.com/laverdet/node-fibers) extension for Node.js that makes it possible to write synchronous looking code on the server instead of the more common callback style used by most Node.js projects. Some prefer this way of writing asynchronous code, others strongly disagree with it. There are a number of arguments on both sides.

This is very contentious issue at the moment, and we prefer to stay out of it. A [very small percentage](http://www.mikealrogers.com/posts/a-vocal-minority.html) of modules in npm are written this way, so we have stuck to the more traditional Node.js style of callbacks.

## Wrapping it up

You can achieve many of the same things with both frameworks. Both are powerful tools for implementing features that are very difficult to write in more traditional web frameworks like Rails and PHP.

Derby has been more focused on making sure it can support advanced features like conflict detection, offline clients, and extremely fast rendering. We are also more focused on compatibility with other modules in the existing Node.js ecosystem, and we have a permissive open source license.

Ultimately, we are all trying to create the best tools for developers, and competition can help to breed innovation. We are happy to see other approaches like [Meteor](http://meteor.com/), [SpaceMagic](http://spacemagic.io/), and [Firebase](http://www.firebase.com/), because we will all learn from each other. We will end up with better tools, a better web, and better experiences for users.

If you're interested in what we're working on, follow us on [Twitter](https://twitter.com/#!/derbyjs) and [GitHub](https://github.com/codeparty/derby/).
