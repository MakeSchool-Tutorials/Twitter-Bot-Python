---
title: Data Structure Comparisons and Performance Analysis
slug: data-structure-comparisons-and-performance-analysis
---

Because modern computers are so fast, and the scripts we've been writing work with relatively small chunks of data with low computation requirements, it may seem like all programs execute in a split second. This is a good thing when we're in development mode: we want the rapid feedback that fast execution time provides.

However, in the real world, the world of production code, the amount of data can be orders of magnitude larger, and constraints on computation power and memory access may be more restrictive. The difference between two histogram-generating scripts, one which parses a source of 10,000 words in 0.1 seconds vs. one that parses a source of 10,000 words in 0.01 seconds, may not seem like a big difference now.

But how will each of those scripts perform when given a source of 500,000 words? Will their execution time grow linearly, to 5 seconds and 0.5 seconds? That would mean an O(N) growth rate. Or will it grow quadratically, with an O(N^2) growth rate? That would take a lot longer.

When dealing with questions of performance, there are two ways to identify how much compute time a particular algorithm will need:

1. Do algorithm analysis on the source code and calculate the Big O number for a given algorithmic operation
2. Run the code with sample data and benchmark the execution times

The first option is akin to looking at an engine schematic for a race car and calculating how fast it will be able to go. The second is akin to racing the car in a time trial. They're both useful skills to have, and in this tutorial you're going to exercise them both.

When you completed the word frequency tutorial, you likely picked a single data structure to implement your histogram (mappings of words to their frequency count). How do you know that the data structure you picked is the best one for the job? In this tutorial, you're going to explore that question further.

Here are your tasks for the tutorial:

- Pick 3 data structures from the list below and implement a histogram program for each.
- For each data structure, write a `histogram()` function that takes a list of words and returns a histogram of the corresponding data structure.
- For each data structure, write a `frequency()` function that takes a word and a histogram structure and returns the frequency count for that word.
- Analyze the algorithms for each`frequency()` function and identify their Big-O complexity.
- Benchmark each `frequency()` function to find the execution time of a search operation with two histogram sizes (100 and 10000 unique words)

> [info]
>
Hint: when benchmarking, you may need to run the same function many times to get a big enough sample size for measurement. For instance, you could run a loop that calls `frequency('testword', histogram)` 1000 times.

Here are some data structure options to choose from, in order of complexity/difficulty:

1. List of tuples (flat [associative array](https://en.wikipedia.org/wiki/Associative_array))
2. Dictionary ([hash table](https://en.wikipedia.org/wiki/Hash_table))
3. Sorted, [singly linked list](https://en.wikipedia.org/wiki/Linked_list#Singly_linked_lists)
4. Doubly linked list ([association list](https://en.wikipedia.org/wiki/Association_list))
5. [Binary search tree](https://en.wikipedia.org/wiki/Binary_search_tree)
6. [Radix tree](https://en.wikipedia.org/wiki/Radix_tree)
7. * wild card - choose your own

When you're finished, you should have a better sense of what is meant by algorithmic complexity and how it can affect the performance of your code. Remember, though, that you don't get a free pass for writing sloppy code just because it performs well. Good naming and code structure are all essential, especially when refactoring for performance optimization!

> [action]
>
Ok, go code!

Help! What is algorithmic analysis and how do I do it?
==
No worries if you haven't done algorithmic analysis before. Let's walk through an example implementation, and then analyze and benchmark it together.

For this implementation, we're going to keep it simple and use a list of tuples to store our histogram.

So, let's start out by writing a `histogram()` function that takes a list of words and returns a list of tuples (which will be pairs of words along with their count). Start with some pseudocode:

	def histogram(words):
	    # create a new list called hgram
	    # for each word in the list of words
	    #    check if word has been counted already
	    #    if word is not in histogram
	    #        add a new word-count pair to hgram (count is one)
	    #    if word is already in hgram
	    #        find its current count
	    #        make a new word-count pair with
	    #         the same word and the current count plus one
	    #        replace the word-count pair with the new pair
	    # when all words have been read, return the hgram

This looks like a decent description of the algorithm for creating a histogram. If you haven't yet made a histogram function, try to do it before looking at the solution:

> [solution]
>
	def histogram(words):
	    hgram = []                           # create a new list called hgram
	    for word in words:                   # for each word in the list of words
	        index = find(word, hgram)        # check if word is in hgram already
	        if index == None:                # if word is not in histogram
	            hgram.append((word, 1))      # add a new word-count pair to hgram
	        else:                            # if word is already in hgram
	            count = hgram[index][1]      # find its current count
	            new_pair = (word, count + 1) # make a new word-count pair
	            hgram[index] = new_pair      # replace word-count pair
	    return hgram                         # return the hgram

Notice that in writing this `histogram()` function, we used a function that doesn't yet exist: `find()`. We could have included the steps here, but the implementation details of finding a word are not the responsibility of `histogram()`, so it is better to use a separate function. We already have some explicit expectations about how it should work: it will take a word and the histogram, and then return either the index of the matching word-count pair or `None` if the word does not exist in the histogram.

With those expectations defined, we can write that `find()` function:

> [solution]
>
	def find(item, hgram):
	    for index, pair in enumerate(hgram):
	        if pair[0] == item:
	            return index
	    return None
>
Note the use of the enumerate() built-in function to include an index with the iteration.

To test that these functions work as expected, we need some sample data. Ideally it should be ordered and predictably structured so that we can reason about it easily. In this case, we'll use a subset of the word list at `/usr/share/dict/words` since we know these will yield a set number of unique words.

Here's a utility function that will return the first n words from `/usr/share/dict/words`:

	def list(length):
	    dict_words = '/usr/share/dict/words'
	    words_str  = open(dict_words, 'r').read()
	    all_words  = words_str.split("\n")
	    return all_words[0:length]

With this, we can easily generate lists of unique words with a specific size:

	list(5)  # => ['A', 'a', 'aa', 'aal', 'aalii']
	list(10) # => ['A', 'a', 'aa', 'aal', 'aalii', 'aam', 'Aani', 'aardvark', 'aardwolf', 'Aaron']

Now we can test our `histogram()` and `find()` functions:

	wordlist = list(5)
	hgram = histogram(wordlist) # => [('A', 1), ('a', 1), ('aa', 1), ...]
	find('aal', hgram) == 3     # => True
	find('zoo', hgram) == None  # => True

Got it? If you're unclear, make sure to write this code for yourself and test it.

Now we're ready to write our `frequency()` function, which is what we really want to analyze, since this function will answer the important question of "how many times does word x appear?"

Remember that `frequency()` takes the word to search for as well as the histogram object, and then returns the word frequency count. Here's one way to implement it, making use of the existing `find()` function written already:

	def frequency(word, hgram):
	    index = find(word, hgram)
	    if index:
	        word_count_pair = hgram[index]
	        return word_count_pair[1]
	    else:
	        return 0

To test `frequency()`, we should make sure that our source has at least some duplicate words. Otherwise every word will have a count of 1, and that leaves a lot of room for false positives. Here's some tests to prove that `frequency()` works as expected:

	wordlist = list(5)
	wordlist.append('aal')
	wordlist.append('a')
	wordlist.append('a')
	hgram = histogram(wordlist)     # => [('A', 1), ('a', 3), ('aa', 1), ...]
	frequency('a', hgram) == 3)     # => True
	frequency('aal', hgram) == 2)   # => True
	frequency('aalii', hgram) == 1) # => True
	frequency('zoo', hgram) == 0)   # => True

Ok, now we have something to work with: a functional `frequency()` function that we can analyze and benchmark.

Let's start with the algorithm analysis to find the Big-O complexity of this function. Read this explanation of Big-O notation to get familiar with the concept:

> [info]
>
When doing algorithm analysis, we need to look for are all of the individual computations that make up a particular algorithm and determine which of them are constant time (also known as O(1)) (i.e. they will always take roughly the same amount of time to compute), which are linear time or O(n) (i.e. time to compute will increase by one time-unit with every additional data-unit added), and which are quadratic time or O(n^2) (i.e time to compute will increase quadratically, as the square of the number of data-units being computed). There are other kinds of complexities, but for now we'll just deal with these three.

With that in mind, let's break up the `frequency()` function into its component computations and identify the Big-O complexity of each computation. Here's the function definition, with comments identifying the computations:

	def frequency(word, hgram):
	    index = find(word, hgram)          # 1. call the find() function; 2. assign variable
	    if index:                          # 3. evaluate conditional
	        word_count_pair = hgram[index] # 4. access list element; 5. assign variable
	        return word_count_pair[1]      # 6. access list element
	    else:
	        return 0

Written another way, here's a list of all the component computations in `frequency()`:

1. Call function `find()`
2. Assign variable
3. Evaluate conditional
4. Access list element
5. Assign variable
6. Access list element

We can identify the Big-O complexity of all of these computations, although for `find()` we'll need to look at the function body itself to find its Big-O complexity.

The Big-O complexity of `frequency()` can be written as:

O( [complexity of `find()` function call] + [complexity of variable assignment] + [complexity of remaining steps...] )

> [action]
>
Try to figure out the Big-O complexity for each computation step for yourself, and then keep reading.

<!--- html comment to break boxes -->
> [solution]
>
Ok, so what are the Big-O runtime for each of the computations in `frequency()`?
>
The first step, `find(word, hgram)` is a function call, which is equal to whatever time it takes to execute the actual function. Because `find()` iterates through each element of the `hgram` list, it has O(n) time. If `hgram` has 10 elements, the maximum number of iterations is 10. But for 1000 elements, the maximum number of iterations is 1000. So we see that the computation time (number of iterations) grows in direct proportion to the number (n) of elements in the list, giving it an O(n) runtime.
>
The rest of the steps (assign variable, access a list element, etc.) are all constant time, because the size of the data doesn't affect how long it takes to perform these tasks. Or rather, it doesn't influence the time in a way that is significant to its Big-O complexity.
>
So, we can simplify our Big-O complexity for `frequency()` to this:
>
O( n + 1 + 1 + 1 + 1 + 1 )
>
Because the complexity of `find()` is O(n) and all other computations are O(1). Then we can simplify:
>
O( n + 5 )
>
And because Big-O doesn't care about constants, we can drop those too, leaving us with our final answer: `frequency()` has a runtime complexity of `O(n)`. Success!


Benchmarking your Code
==
The last step is to benchmark this function to see how much time it takes to run. This information will not be particularly useful on its own, but comes in very handy when comparing two different algorithms.

To benchmark our code, we'll use the [timeit module](https://docs.python.org/3/library/timeit.html?highlight=timeit#module-timeit).

There are several steps to benchmarking; it's important to be careful when setting it up or you may contaminate your results! This is like being a laboratory scientist: if your petri dish is dirty, you can't trust the results of your experiment!

The first step is to set up the program state that is not being timed, so that we can isolate only the part of the program we do want to time. In this case, we'll create two histograms: one with 100 words, and another with 10,000:

	hundred_words       = list(100)
	ten_thousand_words  = list(10000)

	hundred_hgram       = histogram(hundred_words)
	ten_thousand_hgram  = histogram(ten_thousand_words)

That gives us two histogram objects to pass to our benchmarked `frequency()` call. Next we'll need a word to search for, and to really push our function we'll search for the last word in the original list:

	hundred_search      = hundred_words[-1]
	ten_thousand_search = ten_thousand_words[-1]

Now we have both of our arguments needed to call `frequency()`, so we can import the `timeit` module, set up a statement to time, and create a new timer:

	stmt  = "frequency('{}', hundred_hgram)".format(hundred_search)
	setup = "from __main__ import frequency, hundred_hgram"
	timer = timeit.Timer(stmt, setup=setup)

Note that the `Timer` doesn't have access to our functions or variables by default, which is why we need to include the `setup` argument to import them.

With the timer set up, we then define a number of executions (how many times `stmt` will be run), run the timer, and print out the result with a friendly message:

	iterations = 10000
	result = timer.timeit(number=iterations)
	print("frequency() time for 100-word histogram: " + str(result))

Running that on my machine, I can see how many seconds it takes!

	$ python histogram_benchmark.py
	frequency() time for 100-word histogram: 0.07999536900024395

Repeat the same steps to test the 10,000 word histogram:

	$ python histogram_benchmark.py
	frequency() time for 100-word histogram: 0.07999536900024395
	frequency() time for 10,000-word histogram: 8.522602347999054

Comparing that benchmark with the complexity analysis before, we see that `frequency()` does indeed grow in linear time, or O(n), because it takes 100 times as long to find the frequency of a word in a 10,000 word histogram as it does in a 100-word histogram.

With that, our benchmarking practice proved our analysis theory! Experiment success!

Now we just have to do the same thing for the other data structures...

Where to Go From here
==
Finished already? You've analyzed, benchmarked, and cleaned up your code? Ok great, here are some further investigations you can attempt:

- Write functions to insert, update, and delete items in the histogram and analyze/benchmark them.
- Restructure your histogram implementations as classes with their own methods for searching, inserting, updating, and deleting.
