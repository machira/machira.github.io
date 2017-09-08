---
layout: post
title:  "Another Simple Database: DogDB"
date:   2017-09-07 19:15:51 -0500
tags: db architecture lit-review
category: tech
---

As part of my project building a [simple database]({{ site.url }}{% post_url 2017-08-30-simplest-db%}), I have found myself looking at other small (in complexity) databases. In fact, my project was kickstarted after I read the code base to [ Tiny DB ](https://github.com/msiemens/tinydb). 

So today, I wanted to briefly go over what I learned looking at other such projects, and perhaps how they informed my own approaches. 

### TinyDB: 
I don't quite remember where I first came across Tiny DB, but once I started poking around, I was impressed by it's simplicity. This sort of approachability, motivated me to try making my own database. I found myself wondering  
As I mentioned above, this project was motivated by looking at the codebase for TinyDB. There are a ton of good ideas here;
* A json file as the backing store, meaning the data is still human readable. 
* Querying! Meaning you can do this:
* A fairly plugable ecosystem for various backing stores (memory or json file)
     
I copied a lot of these ideas to SimpleDB (sincerest form of flattery.) In fact, at exactly, [this commit](https://github.com/machira/SimpleDB/commit/0e91091a5de988c2503fb3bb7762f70df6112bc3) simple db realises CRUDs by reading and writing the whole file as in TinyDB. 
     
There were a couple of things I would desire to improve on TinyDB;
* Initially, I wanted to deal with JSON files as they are. ie, I didn't want to embed any metadata. It would be really cool if I could download any JSON file from anywhere, and then start querying on any field immediately. We're at this point right now, but because of other features I would like to add, I may have to revisit this.
* I wanted to tackle files larger than memory. 
* Simplify the querying syntax a bit.

I was also left with a bunch of questions after reviewing the codebase. eg. I couldn't figure out why/when the writers of the db had decided to add ids to elements or what these ids added to the design. Still, a really cool project and I learned a lot.  


### DogBed DB

DogBed DB is one of the AOSA 500 lines or less applications. Having embarked on the journey to write yet another database I found myself curious how the problems I was facing had been solved by others, and did some literature review.

DBDB is a key-value store where persistence on disk is implemented as an immutable binary tree. Nodes contain references to their children as offsets from the head of the file. Leaf nodes contain null references. The file contents are serialized before being spat to disk, so the database file is a human unreadable binary. Since it's a binary tree, DBDB is vulnerable to poor performance when keys are monotonically increasing, such as an integer count, or words from an english database; in this cases, we expect O(N) operations as opposed to the normal O(lgN) operations. 
 
 Finding a key then becomes a binary search over the tree, terminating either at a node with a value, or an unqualified leaf hence no hits.
 The node refs confer two benefits: 
 * deletes can be accomplished by a deref; ie without writing the whole tree afresh at the expense of defragmenting actually usable data in the file. Compaction is left as an exercise to the reader!
 * when a node is loaded into memory we load only refs; which maybe a relatively light memory footprint compared to the actual node data.
 
 Using a fairly smart trick, DogBed database supports atomic writes:
 >We know this last write is atomic because we store the disk address on a sector boundary. It's the very first thing in the file, so this is true regardless of sector size, and single-sector disk writes are guaranteed to be atomic by the disk hardware.
   
 >Because the root node address has either the old or new value (never a bit of old and a bit of new), other processes can read from the database without getting a lock. An external process might see the old or the new tree, but never a mix of the two. In this way, commits are atomic.

DogBedBD uses a file lock mechanism; as opposed to an in-process lock, implying multiple process can co-ordinate over the same database file.

Lastly, DogBed DataBase (DBDB) allows [dirty reads](https://en.wikipedia.org/wiki/Isolation_(database_systems)#Dirty_reads); a read call needn't obey the file lock. This enables many simultaneous reads, at the expense of potentially observing stale data. 

I learned a lot from looking at DogbedDb. It's a great example of what smart trade-offs can give you; The write up also include great advice; test interfaces and single responsibility.




#### *Aside*: 
*I strayed a little bit from reading on dog db and read a few of the architecture posts in the [Architecture of Open Source Applications book](aosabook.org) and I was blown away. The AOSA project is simply beautiful in almost every way. The write ups are **thoroughly** well written,  yet approachable, and ooze nuggets of information. I am floored by how nearly every post can give, give and then give some more. Look how gently the problems of embarrassingly parallel tasks are introduced in the  [webcrawler example](https://github.com/aosabook/500lines/blob/master/crawler/crawler.markdown)  and how deftly each is dealt with, culminating in co-routines. I've yet to use co-routines,  but now understanding their motivations, I should be more deft at recognizing where they can get me out of a pickle.   I will certainly be writing more about some of the projects there.*