---
layout: post
title:  "Simplest DB: The simplest database."
date:   2017-02-13 16:31:17 -0600
categories: db architecture talks
---

Engineering is a study of trade-offs. When we build things, we often have to compromise many concerns against each other. We sometimes choose a simple solution over a faster or more powerful one; attaining ease of implementation and possibly giving up on extensibility. This is the so called quick and dirty. Other times, we give up accuracy, in return for cost.

Good engineering then is the ability to discern what is good enough of a compromise in a given situation.

In this spirit, I have been writing a database. [SimpleDB](https://github.com/machira/SimpleDB) that compromises on EVERYTHING but the educational experience. You really are better off writing to /dev/null.

More seriously though, I wanted to write a tiny database in order to understand the architecture of such applications. As I envision it, SimpleDB will be a no-sql database that can [CRUD](https://en.wikipedia.org/wiki/Create,_read,_update_and_delete) json objects.

The biggest compromise so far is that I am not designing for concurrent access. With this out of the way, we should be able dispose of ACI of the ACID concerns. So my task is only really writing some stuff to a file, and defining some access mechanisms. I hope to handle larger than memory files elegantly.

[There is no end to a good thing](https://www.youtube.com/watch?v=BUIn24caNQs), but I hope to stop development on SimpleDB with a better understanding of: indexing, file writing, and the compromises that the databases I use make.