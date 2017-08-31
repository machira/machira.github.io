---
layout: post
title:  "Simplest DB: The simplest database."
date:   2017-08-30 16:31:17 -0600
categories: db architecture talks
---

Engineering is a study of trade-offs. When we build things, we often have to compromise many concerns against each other. We sometimes choose a simple solution over a faster or more powerful one; attaining ease of implementation and possibly giving up on extensibility. This is the so called quick and dirty. Other times, we give up accuracy, in return for cost.

Often, perfect solutions are prohibitively expensive in execution. Good engineering then is the ability to discern what is good enough of a compromise in a given situation.

In this spirit, I have been writing a database. [SimpleDB](https://github.com/machira/SimpleDB) that compromises on EVERYTHING but the educational experience. You really are better off writing to /dev/null.

More seriously though, I wanted to write a tiny database in order to understand the architecture of such applications. As I envision it, SimpleDB will be a no-sql database that can [CRUD](https://en.wikipedia.org/wiki/Create,_read,_update_and_delete) json objects.

The biggest compromise so far is that I am not designing for concurrent access. With this out of the way, we should be able dispose of ACI of the ACID concerns. So my task is only really writing some stuff to a file, and defining some access mechanisms. I hope to handle larger than memory files elegantly.


###### Approach
First thing I did was to encode the classic CRUD operations as simplest as I could. At this point in the [commit history](https://github.com/machira/SimpleDB/commit/0e91091a5de988c2503fb3bb7762f70df6112bc3) SimpleDB can accurately perform the simplest db operations on a small dataset. 
I'd like to talk very briefly about what SimpleDB can do now, and why each of these steps are important for the MVP.  


    1. Instantiates itself from a JSON file.    
        This enables me to test on a real JSON file as I add more features and so on. 
    
    2. We can add data once a database is initialised. 
    {% highlight python %}
        db.write({'a': 1})
    {% endhighlight %}
      
    3. We can update and delete items. 
    4. We can query what's in the database.
        The querying in particular, is pretty elegant, if I do say so myself. I struggled for a while trying to define a small DSL that would allow some user queries. Finally, I settled on just accepting truthy lambdas. These are an extensible, well understood solution, and I find them quite beautiful. When we bundle Update/Deletes with querying, we get a fairly beautiful API:
    {% highlight python %}
        # querying example: x is an object with an integer value at key 'a'
        def _even(self, x): return x['a'] % 2 == 0
        all_evens = db.query(_even)
        #updating example:
        def double(x): return {'a': x['a'] * 2}
        def even(x): return x['a'] % 2 == 0
        db.update(even, double)
    {% endhighlight %}
    5. We even have some syntatic sugar, in the form of projections and countBy. A projection allows you to fetch the value of a key from every object in the database; so if you have people, you can say give me all first names. A countBy allows aggregate counts, again over all keys in the db. Ideally, this should be a GroupBy, returning more than just a count, and if I have enough time I will double back and implement it that way.
       
I view 1-4 as the core pieces of SimpleDB. Implementing these simply and having them well tested gives me great confidence to try some optimizations. As it is now, SimpleDB is a fairly rough PoC; most operations read the whole DB into memory. 

[Add some benchmarks on how SimpleDB is doing right now] 


Optimizations I have in mind revolves around adding IDs and using those as an index to reduce query times when IDs are known beforehand, and some sort of data structure that will allow such access, that we can operate on databases much larger than the memory we have.   

[There is no end to a good thing](https://youtu.be/AjP1H6tjbu4?t=32s), but I hope to stop development on SimpleDB with a better understanding of: indexing, file writing, and the compromises that the databases I use make.