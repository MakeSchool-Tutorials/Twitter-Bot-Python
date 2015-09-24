---
title: Hash Table from Scratch
slug: hash-table-from-scratch
---

A Quick & Dirty Hash Table
==

In Python, one of the collection types is the dictionary type, which allows you to map keys to values. This is a common and essential data structure with a wide range of purposes.

For example, you might use a dictionary to store phone numbers:

	phones = {'Manolo': '555-321-4321', 'Sue': '555-123-1234'}
	phones['Manolo'] # => '555-321-4321'
	phones['Sue']    # => '555-123-1234'

You may have used dictionaries already, but do you know how they work? We're going to take a little detour in this project to explore and learn about the data structure that power's Python's dictionary feature: the *hash table*.

We'll describe the desired behavior with a sample interface. Then, we'll go over some of the constraints and tools we can use. Finally, it will be up to you to implement the hash table yourself.

##Specifications
Our hash table must implement the following features:

- A key can be **set** to a value
- Given a key, can **get** a value
- Can **update** an existing key with a new value
- Can return a list of all **keys**
- Can return a list of all **values**

##Constraints

- Your hash table must only use the list and tuple collection types (no dictionaries or sets)
- Keys must be a string, a numeric type, or a tuple of strings and/or numbers
- Reading from the table (getting a value for a given key) must have an O(log n) lookup time or better

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

> [action]
>
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

<!-- Commented this section out because these will become requirements, not suggestions -->
<!--
Where to Go From Here
==
Finished already? Is your code clean, readable, and well tested? No? Ok, go do that first. Then, optimize and experiment!

- Did you use a linked list data structure? If not, write an alternate implementation that uses linked lists and compare its performance to your original implementation.
- Expand the interface for your hash table to match that of [Python's built in dictionary](https://docs.python.org/3/library/stdtypes.html#mapping-types-dict). Your hash table should support all of the same features of a regular dictionary.
-->

A Good Hash Table
==

Ok, what have we done so far? Well, we've built an alternative to Python's built-in dictionary structure. But quite frankly, it's not very good. It's too basic and not nearly performant enough to be called a _real_ hash table.

In this next stage, we're going to **refactor** our hash table (change the implementation, not the interface) so that it functions and performs more like a real, production-grade hash table.

## Using Buckets to Store Values

The first critical feature of a hash table is how elements are assigned to "buckets". If your original hash table implementation didn't use a limited number of buckets in your table and the `hash()` function to generate a hashed value from the key, and then find the remainder from dividing the hashed key by the number of buckets, and use _that_ number to pick which bucket to put the value in, then _it's not a real hash table_.

That was a mouthful. Let's use a real example to walk through it in more detail. Say we want to store the value `22` at key `"age"` in our hash table called `student`. This hash table has 6 buckets (in Python, these can be items in a list object), which we can represent visually like this:

   0   1   2   3   4   5
	[ ] [ ] [ ] [ ] [ ] [ ]

If we use Python's built-in `hash()` function to create a hash of the key (i.e. the string value `"age"`), it will give us back a number, like this:

	hash("age")
	# => 1453079729191098346

Now that we have a numeric representation of the string `"age"`, we pick a bucket to put it in by finding the remainder of this number divided by the number of buckets:

	1453079729191098346 % 6
	# => 4

This tells us to place our value in bucket `4`:

	 0   1   2   3   4      5
	[ ] [ ] [ ] [ ] [ 22 ] [ ]

Because a [hash function](https://en.wikipedia.org/wiki/Hash_function) will always return the same, unique number for a given input (caveat: many hash functions use a random seed, so it will only return the same number when called within the same process), we can use this to quickly look up values later. If we want to find the value stored at key `"age"`, all we need to do is hash the key and run our modulo calculation and voilá! We know which bucket to retrieve it from.

Compare this to searching for an item in a list or array, which requires that you walk through each item before returning the item.

> [action]
>
Does your hash table already use `hash()` and `hash_num % num_buckets`? If so, great! If not, make sure that it does!

## Handling Collisions

There is one glaring problem with that previous example of a hash table. You may have already noticed it: what happens when the remainder of the hash key divided by the number of buckets is the same for two different keys? How do we store both values and ensure that they are still associated with the correct key?

This is called a **collision** and any hash table worth its salt needs to handle them gracefully and efficiently.

Let's illustrate the problem a bit more. Remember our example `student` hash looks something like this, with the value `22` at bucket `4`, which we access by hashing the key `"age"`:

   0   1   2   3   4  	5
	[ ] [ ] [ ] [ ] [22] [ ]

Say we wanted to add the value `"Amy"` for the key "name", but the remainder of `hash("name") % 6` is _also_ `4`. What do we do? Make a list of both values, like this?:

   0   1   2   3   4     				 5
	[ ] [ ] [ ] [ ] [[22, "Amy"]] [ ]

No, that won't do. How do we know which value belongs to `"name"` and which to `"age"`?

There are many ways to solve this problem, but in this tutorial we're going to tackle it by using a [linked list](https://en.wikipedia.org/wiki/Linked_list) to handle collision chaining. Each bucket of our hash table will be a linked list containing nodes that hold both the key and the value.

This way, looking up a value for a key will follow these steps:

1. Find the correct bucket for key using hash algorithm
2. Walk through nodes in linked list until node with same key is found
3. Return value of node

When inserting a value, our hash table will follow these steps:

1. Find the correct bucket for key using hash algorithm
2. Create a new linked list node with both the key and value to be stored
3. Append a new node to the linked list in the bucket

So, it looks like we'll need to make a linked list data structure.

## Make a Linked List

Each bucket in our hash table should be a linked list so that we can gracefully handle collisions and still keep the performance benefits of storing values in buckets.

So, what is a linked list? In its simplest form, a linked list is a group of nodes where each node contains both some data (the value to store) and a reference (or link) to the next node in the group.

Data is added to a linked list by creating a new node and referencing it from the last node in the list. Removing a node is as simple as changing the reference from the previous node in the list so that it points to the next node in the list.

We'll describe the desired behavior using a sample interface. Then it will be up to you to implement the linked list yourself.

##Specifications
Our linked list must implement the following features:

- Linked list stores the first node as its **head**
- Linked list will retrieve the last node as its **tail**
- A linked list can **append** data
- Can **find** a value from linked list
- Can **delete** a value from linked list

Here is an example interface:

	mylist = LinkedList()

	mylist.append('a')
	mylist.append('b')
	mylist.append('c')

	mylist.head.data # => 'a'
	mylist.tail().data # => 'c'
	mylist.find(lambda data: data > 'b') # => 'c'
	mylist.delete('a')
	mylist.head.data  # => 'b'

> [action]
>
Not too bad, right? Ok, go build a linked list and use it to make your hash table super awesome!

Help! All this talk of links and nodes has got me feeling woozy.
==

<!-- TODO explain LL in more detail -->
