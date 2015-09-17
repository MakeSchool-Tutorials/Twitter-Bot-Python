---
title: Hash Table from Scratch
slug: hash-table-from-scratch
---

In Python, one of the collection types is the dictionary type, which allows you to map keys to values. This is a common and essential data structure with a wide range of purposes.

For example, you might use a dictionary to store phone numbers:

	phones = {'Manolo': '555-321-4321', 'Sue': '555-123-1234'}
	phones['Manolo'] # => '555-321-4321'
	phones['Sue']    # => '555-123-1234'

You may have used dictionaries already, but do you know how they work? We're going to take a little detour in this project to explore and learn about the data structure that power's Python's dictionary feature: the *hash table*.

We'll describe the desired behavior with a sample interface. Then, we'll go over some of the constraints and tools we can use. Finally, it will be up to you to implement the hash table yourself.

##Specifications
Our hash table must implement the following features:

* A key can be **set** to a value
* Given a key, can **get** a value
* Can **update** an existing key with a new value
* Can return a list of all **keys**
* Can return a list of all **values**

##Constraints

* Your hash table must only use the list and tuple collection types (no dictionaries or sets)
* Keys must be a string, a numeric type, or a tuple of strings and/or numbers
* Reading from the table (getting a value for a given key) must have an O(log n) lookup time or better

Here is an example `HashTable` object that implements the specifications listed above. It stores the decimal digit that corresponds to its roman numeral.

	roman = HashTable()
	roman.set('I', 1)
	roman.get('I')          # => 1
	roman.set('V', 5)
	roman.set('X', 9)
	roman.update('X', 10)   # Oops, let's fix that.
	roman.get('X')          # => 10
	roman.keys()            # => ['I', 'V', 'X']
	roman.values()          # => [1, 5, 10]

Notice that this implementation uses an object-oriented approach by defining a `HashTable` class. You are welcome to follow either an object-oriented approach or to simply use functions that take a hash table as an argument, for example:

	table_set('I', 1, hash_table)
	table_get('I', hash_table) # => 1

Where `hash_table` is a variable pointing to your hash table data structure.

##Tools
An essential tool for this problem is Python's built-in `hash()` function. Read up on the documentation and the Wikipedia entry for hash functions to understand how `hash()` works and why it is useful. Then play around with `hash()` in a REPL or with some experimental scripts.

Got it? Ok, go forth and build your hash tables!

Help! How do I even begin to think about this?
==
No problem. Let's backtrack to the purpose of a hash table.

What makes hash tables so useful is that they allow us to store *values* (strings, numbers, collections, or any other object) by a *key* of our choosing, which decouples the order of the elements from our ability to insert or retrieve them.

That's a lot of language. Let's look at an example, comparing a basic list to a hash table. Take a look at this list:

	roman_list = [('I', 1), ('V', 5), ('X', 10)]

It contains the same *data* as the example hash table above, but in order to access and use this data we need to know a lot more about *how* it is structured.

For example, if I wanted to find the value of the Roman numeral V, I would have to go through a series of steps to get there:

1. Iterate over each item in the list
2. For each item (a tuple), check the value of the first element (index `0`) in the tuple
3. If the first element is equal to the value provided (`'V'` in this case), then...
	1. Return the value of the item at index `1` in the tuple
5. If we reach the end of the list, then that key must not exist, so...
	1. Return `None` (or throw an error? There are multiple ways to solve this case)

Compare that with an equivalent hash table lookup:

	roman_hash_table.get('V') # => 5

The only thing I need to know about this hash table is that it has a `get()` method which takes my *key* and returns a *value*. That's it. No knowledge of the internal structure is required.

So, a major benefit of hash tables is this key-value mapping interface, also called an *associative array*. They are used all over the place in modern programming.

Under the hood, however, all hash tables still have to use the fundamental building blocks that computers provide. Lists (also called arrays) are a lower-level data structure for storing ordered collections of elements in "buckets" that can then be retrieved by their index, an integer indicating their position within the 1-dimensional array of buckets.

Hash tables use these lower-level arrays in creative ways to allow programmers to use a simpler interface of key-value storage without having to worry about the underlying array structure.

The most basic abstraction step you can take towards building a real hash table would be to simply store the keys and values in a list:

	basic_table = ['I', 1, 'V', 5, 'X', 10]

With that, you could write a `get()` function that takes a key and this list, and then returns the item in the position immediately following the key:

> [solution]
>
	def get(key, assoc_array):
	    value = None
	    for index, item in enumerate(assoc_array):
	        if item == key:
	            value = assoc_array[index + 1]
	            break
	    return value
>
	get('V', basic_table) # => 5

This is the simplest possible way to structure this data, and it's not very elegant or efficient.

There are lots of other ways to structure it, and to truly implement a hash table you'll need to make use of a *hash function* to make indices from keys. Read up on hash tables to learn more about how this works.

<!-- DH - We tell them how to make a terrible hash table, but not a good one -->
<!-- DH - We don't even talk about (hash % numbBuckets) as index into an array yet
<!-- DH - Insert instructions on how to improve the hash table - those will involve a whole new section on how to create a linked list -->
<!-- DH - Students must use the linked list they make to resolve collusions via chaining.

<!-- Commented this out - these will be requirements, not suggestions -->
<!--
Where to Go From Here
==
Finished already? Is your code clean, readable, and well tested? No? Ok, go do that first. Then, optimize and experiment!

* Did you use a linked list data structure? If not, write an alternate implementation that uses linked lists and compare its performance to your original implementation.
* Expand the interface for your hash table to match that of [Python's built in dictionary](https://docs.python.org/3/library/stdtypes.html#mapping-types-dict). Your hash table should support all of the same features of a regular dictionary.
-->
