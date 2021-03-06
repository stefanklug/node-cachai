LRU Cache for Node.js
====================

This is a fork of Monsur Hossain's jscache for browsers, which is loosely based on ASP.NET's Cache, and includes many caching options such as absolute expiration, sliding expiration, cache priority, and a callback function on purge.

How It Works
------------

        var Cache = require('cachai');

        // Create a new cache item
        // The constructor accepts an optional integer
        // parameter which places a limit on how many
        // items the cache holds
        var cache = new Cache();

        // add an item to the cache
        // parameters: key - the key to refer to the object
        //             value - the object to cache
        //             options - an optional parameter described below
        // the last parameter accepts an object which controls various caching options:
        //      expirationAbsolute: the datetime when the item should expire
        //      expirationSliding: an integer representing the seconds since
        //                         the last cache access after which the item
        //                         should expire
        //      priority: How important it is to leave this item in the cache.
        //                You can use the values Cache.Priority.LOW, .NORMAL, or
        //                .HIGH, or you can just use an integer.  Note that
        //                placing a priority on an item does not guarantee
        //                it will remain in cache.  It can still be purged if
        //                an expiration is hit, or if the cache is full.
        //      onPurge: A function that gets called when the item is purged
        //               from cache.  The key and value of the removed item
        //               are passed as parameters to the callback function.
        cache.setItem("A", "1", {expirationAbsolute: null,
                                 expirationSliding: 60,
                                 priority: Cache.Priority.HIGH,
                                 onPurge: function(k, v) { console.log('removed ' + k); }
                                });


        // You can also generate an item with node style callbacks. 
        // This prevents double generation, if multiple requests try to fetch the same cache item.
        // parameters: key - The key to refer to the item.
        //             generator - The generator called when the cache item doesn't exist. 
        //                         The context (this) passed to the generator must be called like callback, with the result, or error.
        //                         You can also return the data from generator, but not both.
        //             callback - The callback called when the item was generated. Signature: callback(err, data)
        //             options - See setItem for info
       cache.getOrGenerateItem('someKey', function() {
           var data = generate some data ...
           if(some error) {
               this(error);
           } else {
               //either
               this(null, data);
               //or
               return data;
           }
       }, function(err, data) {
           //handle the generated data
       });

        // retrieve an item from the cache
        // takes one parameter, the key to retreive
        // returns the cached item
        cache.getItem("A");

        // Remove and return an item from the cache.
        // If the item doesn't exist it returns null.
        cache.removeItem("A");

        // Returns the number of items in the cache.
        cache.size();

        // Return stats about the cache, like {"hits": 1, "misses": 4}
        cache.stats();

        // clears all items from the cache
        cache.clear();


History
-------
* 2/6/2013: Stefan Klug added node style generator support
* 2/22/2012: Forked from Monsur Hossain repo and ported to Node.js.
* 11/29/2011: Thanks to Andrew Carman for tests, pluggable backends, localStorage persistance, and bug fixes.
* 1/8/2011: Migrated project to GitHub.
* 1/20/2010: Thanks to Andrej Arn for some syntax updates.
* 5/30/2008: First version.
