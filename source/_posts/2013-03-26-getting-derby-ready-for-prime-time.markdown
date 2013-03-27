---
layout: post
title: "Getting Derby ready for prime time"
date: 2013-03-26 12:41
comments: true
categories: 
---

At Lever, we believe that all web apps should be realtime. When you are looking at a page and it no longer reflects what someone else is seeing, that is a bug. In addition, we believe that web apps shouldn't feel inferior to their desktop or mobile counterparts&mdash;they should load super quickly, respond to interaction instantly, and work offline without interruption.

We are creating Derby and Racer, intending to build the best technology to power the next generation of innovative web applications. We also aim to make their development accessible for individual developers, startups, and large teams.

## Welcome, Joseph!

We'll get back to our plan for achieving these ambitious goals, but first want to introduce [Jospeh Gentle](http://josephg.com), who recently joined the Lever team. Joseph has already built [ShareJS](http://sharejs.org/), perhaps the easiest open source library for adding Operational Transformation (OT) to a web app. OT is a method for enabling collaborative text editing and other forms of collaborative editing. Similar algorithms power Google Docs, Google Wave, Etherpad, and a handful of other advanced applications. Joseph was also previously a member of the Google Wave team, and is leading development of the next generation of our realtime infrastructure.

## In case you haven't been following along yet

Looking at the landscape of web frameworks and libraries a couple of years ago, we realized that while more and more applications were integrating realtime features, developer options were lacking. 

We started by building Racer, the realtime data synchronization engine at Derby's core. We focused first on innovating in the APIs and capabilities of the system, since there was little precedence for a generic realtime app data engine. There were many fantastic examples of realtime applications, such as chat, Google Docs and Wave, multi-player games, and financial platforms. However, they were by and large created by large organizations with massive resources. In contrast, clear patterns, standards, and frameworks had been established for server-rendered web apps and REST-backed client apps. Such tools were widely available to a variety of developers, including individuals and small companies.

On top of Racer, we built out Derby as a full-stack application framework with the capabilities of both a client-side single page application and a server-rendered Express app. Derby remains one of the very few solutions for taking advantage of full client and server code reuse, and it enables super fast page loads paired with immediate client-side updates.

We are now building a next generation application for companies to better manage their hiring. Our technology enables us to provide features unlike any other application in our market. At the same time, personally developing a full enterprise application helps us to test and improve the platform in a tight iteration loop.

## Establishing a solid foundation

As with most new projects, we couldn't foresee everything that would and wouldn't work well. We wrote a whole lot of features and came up with a many new concepts as we created Racer and Derby. Many of these new ideas were fantastic, and many of them didn't work well or made the implementation too complex. We also didn't write the first version of the code to scale across multiple processes, since our focus was on innovating at the API level.

The next step in our development is simplifying down to the good ideas and making a horizontally scalable system. At the same time, we will be coordinating our work on ShareJS and Racer to better both projects.

The biggest part of the simplification work is moving away from Racer's current fully arbitrary JSON structure object and replacing it with a mirror of Mongo's collections and documents. The model API will stay the same, but we'll be enforcing a collection and document topology consistently. This is already a requirement when using the Mongo adapter, and it is similar to how most other MVC frameworks structure their models.

Each collection will be a map from document ID to document data. Aside from access control filtering, each document will be present in the model or it won't. Instead of trying to select out subsets of documents like a server-side Mongo query does, Racer will simply fetch each entire document that matches a query. We've learned that merging arbitrary document fragments from multiple queries introduces an excessive amount of complexity, and will likely be a big source of bugs without being particularly useful.

Over the next few months, we're rewriting a great deal of Racer and refactoring ShareJS. Joseph is leading this effort and working on it full time. ShareJS will remain an independent project for those who only want to use its OT capabilities alongside their current stack. Integrating OT will help us to simplify some of the gnarlier parts of the model code while maintaining Racer's simple API. What's more, this will bring us much closer to proper offline support by default.

Racer will sit on top of ShareJS. ShareJS will provide the database layer, communication between servers and clients, and OT algorithms, and Racer will provide the model interface. Horizontal scaling support will be added into ShareJS itself, which is going to undergo a huge refactor to make the pieces fit nicely together. The highlights are:

  - Splitting ShareJS into three smaller projects: OT types, a scaling database backend, and a glue project to expose an OT database to a web browser.
  - Moving to a collection/document view instead of a flat set of documents.
  - Support for live database queries.

You can find a much more detailed description of the backend changes to ShareJS on [Joseph's blog](http://josephg.com/post/45724165226/its-time-to-rewrite-sharejs).

These changes will be implemented in a bottom-up manner, so the changes to Racer will land last. In the meantime, we have been fixing bugs as they come up, but much of the current code is going to be removed or completely replaced over the next few months.

## Let's meet!

We'd also like to engage in a more open dialog with our community. We'll be hosting a [meetup next Wednesday, April 3](http://www.meetup.com/lever-open-source/events/111258232/) at our office in San Francisco. Hope to see you there!
