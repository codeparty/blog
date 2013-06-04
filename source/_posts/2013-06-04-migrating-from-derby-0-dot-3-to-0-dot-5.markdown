---
layout: post
title: "Migrating from Derby 0.3 to 0.5"
date: 2013-06-04 9:59
comments: true
categories: 
---

From Derby 0.3 to 0.5, Racer has been completely rewritten. Please see [the documentationn](http://derbyjs.com/) for more detailed information, but this post should help you get started with upgrading your app.

## Migrating data in MongoDB

The first thing you'll need to know is that Racer now requires keeping a journal of all operations, which is stored in Redis. If this journal gets out of sync with the data in MongoDB, it will likely break your app. The journal is the main source of what is the current state, and the database is used to store a cache of the data at a particular snapshot. When fetching documents, the snapshot stored in the DB provides more efficient retrieval and query support. To get started, you'll need to install [Redis 2.6](http://redis.io/download), then initialize the journal with the data stored in Mongo.

To make getting started with existing data a bit easier, we've written a [script to initialize the journal](https://github.com/share/igor) in Redis based on a MongoDB database. There is also a [tool to inspect your data](https://github.com/share/godbox) once it is initialized. For more info on using these tools, check out this video:

{% youtube FoOfNCJkAAA %}

## Conceptual differences

Overall, the core concepts in Racer remain mostly the same. However, we have made a few significant changes based on our experience with the previous version.

* Queries use the native database format. Racer 0.3 worked by defining a named queries on the server in advance. It used a special format that was then mapped to the database's native query language. In Racer 0.5, queries are simply expressed in the native format of the database. Currently, we have implemented a MongoDB adapter, though it is possible to implement adapters for other databases as well.
* The previous concept of the "speculative model" has been removed, since all remotely synced data is updated via operational transformation. The speculative model was a frequent source of frustration, and operational transformation is a superior approach to solving eventual consistency.
* Private paths have been replaced with local and remote collections. Previously, private paths were any path containing a segment that started with an underscore. This allowed you to nest private paths underneath objects that were synced, but this was always buggy and complicated the implementation. In 0.5, every document is either local to a model or remote and synced back to the server and other clients. Local collection names (the very first path segment) start with an underscore (`_`) or dollar sign (`$`). All other collection names are remote. Collections starting with dollar sign are meant for internal use by Racer, Derby, and framework extensions. Your application should define local collections starting with underscore.
* Subscriptions are only available at the document level. To greatly simplify the implementation of subscriptions and fetches, models may only subscribe or fetch an entire document. For now, this is also a limitation of access control, and a given client may only be allowed or prevented from getting an entire document. Eventually, access control will support filtering certain fields from certain users, but this is not completed yet.
* Racer is now much simpler and more modular. It relies on ShareJS to handle remote data syncing, allows use of any connection layer instead of depending on Socket.IO, and has much less abstraction internally.

## Specific API changes

* Derby automatically cleans up data stored underneath `_page` immediately before each full page render on the client. This means that all private paths and refs should generally be moved to be underneath `_page`
* It is no longer possible to store data at a root private path, and it must be stored within a local document, now most likely underneath `_page`
* The MongoDB adapter adds `_v` and `_type` to keep track of internal state
* Documents may have a type other than an object at their root. If a document is an object, its MongoDB document will correspond directly to the document. If it is another type, the Racer document will be nested underneath `_data` in MongoDB. This means that it is now possible to use paths like `hello.message`, which would be stored as a document such as `{_id: 'message', _data: 'Some text', _v: 1, _type: 'http://sharejs.org/types/JSONv0'}` in the `hello` collection
* Document `id` properties are no longer mapped to and from the `_id` in Mongo
* Derby automatically unsubscribes from all queries and documents subscribed on the previous page unless resubscribed in the next route
* `model.subscribe` and `model.fetch` only return an error argument. They no longer return scoped models corresponding to the inputs
* Model events no longer correspond directly to method names. The model events are now `change`, `insert`, `remove`, `move`, `stringRemove`, `stringInsert`, `load`, `unload`, and `all`
* The path wildcard synatx for model events has changed slightly, and there are now both single segment wildcards (`*`) and multiple segment wildcards (`**`) at the end of a path
* Model functions are defined based on names rather than bundled as strings. Similar to view helper functions, model functions are now defined based on a name and initialized via `model.start()`. This means that model functions can use closure scope as expected
* Stores no longer have getter and setter methods. It is now necessary to create a model from a store to perform gets and sets
* The previous afterDb hooks have been removed
* Access control APIs have been changed
* `model.ref` no longer has a third `key` argument
* Derby no longer generates and saves a file to disk for an app script bundle. Instead, the script is cached in memory and made available via middleware
* The server configuration options have changed substantially. See [the examples](https://github.com/codeparty/derby-examples) or [generate a new project](http://derbyjs.com/#create_an_app) for reference

If you have other migration questions or tips, please leave them in the comments to this post.
