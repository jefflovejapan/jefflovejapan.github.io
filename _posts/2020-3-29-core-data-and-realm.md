---
layout: post
title: Getting to Know Core Data and Realm
tags: [tech book a month]
---

Core Data has always been a weak area for me, having never worked with it professionally[^1], and I felt like I could use a refresher on Realm, too. So instead of following the plan and reading one book a month, I read three over the course of February and March:

1. [Core Data](https://www.objc.io/books/core-data/) by Florian Kugler and Daniel Eggert
2. [Core Data by Tutorials](https://store.raywenderlich.com/products/core-data-by-tutorials) by Aaron Douglas, Matthew Morey, and Pietro Rea
3. [Realm: Building Modern Swift Apps with Realm Database](https://store.raywenderlich.com/products/realm-building-modern-swift-apps-with-realm-database) by Marin Todorov

## How were the books?

I learned a lot from all three, but I wish I'd read the Ray Wenderlich book on Core Data before reading Objc.io's. I think the Wenderlich book gives a much better introduction for people that have never touched Core Data. The Objc.io's introductory chapters are rougher, but it still provides a lot of handy functions and best practices. I'm a fan of Florian's coding style from the other books and videos and found plenty to like in it.

Also, unlike the Wenderlich book, the Objc.io one is really not project-based. It's more difficult to follow along with the sample code, and sometimes the explanations are worded in a way that makes me wonder whether it's a mistake or if I'm misunderstanding something they'd said earlier.

The Realm book is also from the team at Ray Wenderlich and feels very comparable to their Core Data one in terms of scope, building up the sample projects, etc. As someone who's done a decent amount of work with Realm I still learned some useful things, and wouldn't hesitate to recommend it to someone wanting to jump into Realm for the first time.

## So should I use Core Data or Realm for my project?

This is obviously going to depend a lot on your use case, but I'd sum up how I think about the two like this:

- Core Data is like a framework for building object graph management solutions. You can control every aspect of how data moves to and from disk and how your multithreaded code behaves.
- Realm has batteries included. There's generally one right way to do something, and there's much less that you need to understand to use it correctly.

I would reach for Core Data if I was trying to minimize my dependency on third-party libraries, or if I had a need to work with large graphs of managed objects and wanted to optimize reads and writes very tightly. To give you a sense of what I mean, executing a fetch request always involves a round-trip to disk, so you want to limit how frequently you're performing them. The data that's included in those fetch results typically contain faults -- references to other managed objects that haven't been populated yet. You determine when you want to pay for the faults to be filled. Changes that you make in the "scratchpad" (context) have to be explicitly saved to disk. You can use "subentities" if you want multiple types of managed objects to be stored together in the same database table, but these don't behave like subclasses. The list goes on.

I would go for Realm if I was just trying to get something up and running. Specifying a schema is as simple as inheriting from `Realm.Object` and async code is easy to understand. For example, any time you mutate a managed Realm object it needs to be in a write transaction and other parts of your code that are listening for changes are notified. In general, it seems like it's less powerful and gives you less control, but there are fewer opportunities to shoot yourself in the foot.

## Random Learnings

### Core Data

- You can't pass Core Data contexts or objects across threads. For access to an object on a separate thread you'll want its `NSManagedObject.objectID`, which is threadsafe.
- Core Data's persistent store coordinator will keep all contexts backed by the same persistent container up to date. This means that you can save changes to your object on `context1` and `context2` will see them if it looks for them. You can keep the two in sync pretty easily by subscribing to the relevant notifications.
- A lot of the complaints I've heard from people who work with Core Data revolve around reasoning about child contexts. The way they're presented in the Wenderlich book seems to be entirely as in-memory scratchpads whose changes you can either commit or throw away. Calling `save()` on a child context only saves those changes to the parent context, not to the store.
- The Objc.io book comes out pretty strongly against nested contexts, with the exception of the single parent-child case.

### Realm

- Realm can be made very performant when you need to do a ton of writes, but it requires some screwing around to get a run loop installed on a background thread.
- Realm allows reads and writes from any thread, publishing notifications on the thread they were created from. The realm and its objects can't be passed across threads.
- Calling `Realm(configuration:)` to initialize a new realm is usually a lightweight operation because the framework will return an existing instance for this thread if one's available. In general, you shouldn't hold onto realm instances and should hold onto the configuration and initialize on the fly instead. (There's an exception when you're writing on a single dedicated _thread.)_
- By default, adding an object to a realm where another object of the same type shares its primary key will error instead of applying the changes.
- It's very simple to set up Realm Cloud, at least with their PaaS (not sure about self hosting.)

### Differences and similarities

- Unlike Core Data, Realm doesn't allow for cascading deletes out of the box, but the Wenderlich book shows you how to build a simple implementation.
- Migrations in Core Data and Realm seem more or less similar. The same kinds of automatic migrations can be performed for you, and you perform more complex migrations in a similar way. Core Data provides a graphical editor for its data models, giving you a way to map old to new by specifying stuff in a GUI ("custom mapping model") as a middle ground between a fully automatic migration and a fully custom one.

## Conclusion?

The abstractions in Core Data are a lot cooler than I thought they were, and it seems like a lot of people's complaints about it are probably more related to complex configurations than poor design decisions by the framework's authors. That said, I like that Realm makes easy things easy, and for a lot of projects, the sacrifice in performance and predictability will be justified by the reduced engineering effort to keep things working.

[^1]: Except for that time that I used it (incorrectly) in a take-home project for a job application. I got the job, which I like to think supports my idea that people think Core Data is an important thing to know, whether or not they actually want to use it in their projects.
