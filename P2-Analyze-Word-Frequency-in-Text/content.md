---
title: Analyze Word Frequency in Text
slug: analyze-word-frequency-in-text
---

The next step towards our sentence generation program is to perform some basic textual analysis. In this case, we're going to analyze the frequency of words in a body of text.

For example, if the source text were the sentence `"one fish two fish red fish blue fish"`, then the word frequency would be `"one" -> 1, "fish" -> 4, "two" -> 1, "red" -> 1, "blue" -> 1`.

In this tutorial, we'll be writing a program which, given a source body of text, can perform operations to answer questions such as:

* What is the least/most frequent word(s)?
* How many different words are used?
* What is the average (mean/median/mode) frequency of words in the text?

The structure to represent this kind of data is called a histogram, which really just means a count of things by category. In Python, you can represent a histogram in a variety of ways using the different data structures available. Part of the challenge of this tutorial is how to design and use these data structures to create a histogram.

We've already seen how to read files in and break them up into words, but for this exercise we will want to use a more interesting file than `/usr/share/dict/words` because, of course, that file only includes one instance of each word. Not very interesting for frequency analysis.

So, the first thing you'll need to do is to find yourself a good body of source text. You can use any text you want, so long as you're not breaking any copyright laws and you can store it as plain text. A good source is [Project Gutenberg](https://www.gutenberg.org/), where you can easily download plain-text and copyright-free versions of classic books.

Once you have your source, you are ready to write your program. Here are the specifications that your program must satisfy:

* A `histogram()` function which takes a `source_text` argument (can be either a filename or the contents of the file as a string, your choice) and return a histogram data structure that stores each unique word along with the number of times the word appears in the source text.
* A `unique_words()` function that takes a `histogram` argument and returns the total count of unique words in the `histogram`. For example, when given the histogram for [The Adventures of Sherlock Holmes](https://www.gutenberg.org/ebooks/1661), it returns the integer `8475`.
* A `frequency()` function that takes a `word` and `histogram` argument and returns the number of times that `word` appears in a text. For example, when given the word `"mystery"` and the Holmes histogram, it will return the integer `20`.

> [action]
>
Using those functions, can you write code to answer the questions listed above for a given source text? What other insight can you derive from this analysis?
>
Enough talk. Let's start coding!

<!-- html comment to break boxes -->

> [info]
>
Wait! I'm confused. Where do I start?
Not to worry, we can slow down and think this through.
>
If you're not sure how to split a text file up into its words, take a look at Python's [`string.split()`](https://docs.python.org/3/library/stdtypes.html#str.split) method, or [`re.split()`](https://docs.python.org/3.1/library/re.html#re.split). Both methods return lists of strings. Play with them (using smaller strings than giant texts from classical books) until you feel comfortable with how they work.
>
Additionally, try using a much smaller body of text (like a blog post) as your source before scaling up to a book-length source text.
>
If the concept of a *histogram* is baffling you, well then let's go over some of the options for representing a histogram using Python's data structures. We'll use the sentence `"one fish two fish red fish blue fish"` as our source text to keep things small and simple.
>
As a list of lists:
>
	histogram = [['one', 1], ['fish', 4], ['two', 1], ['red', 1], ['blue', 1]]
>
As a list of tuples:
>
	histogram = [('one', 1), ('fish', 4), ('two', 1), ('red', 1), ('blue', 1)]
>
As a dictionary of key-value pairs:
>
	histogram = {'one': 1, 'blue': 1, 'two': 1, 'fish': 4, 'red': 1}
>
You could even invert the words and counts, like this:
>
	counts_list = [(1, ['one', 'two', 'red', 'blue']), (4, ['fish'])]
>
These are just a few options. The hard part is writing a function to build a histogram, but if you know how to iterate over a list, how to append items to another data structure, and how to do basic math, you've got all the tools you need to solve this problem.

Where to Go from Here
==
Finished already? If you still have time left, make your code readable. Then optimize and experiment:
* Benchmark and optimize! How can you make your program as performant as possible? If you optimize for reading and not for writing, how can you improve your program? Don't be afraid to try different data structures, and tweak those algorithms!
* If you used a *dictionary* data structure in your program, rewrite it using only the *list* and *tuple* data structures. How fast can you make your read operations? (Hint: consider the benefits of sorting...)
* Create an intermediate histogram file that can be easily read and converted to a histogram data structure in Python. It could just be a plain text file with words and counts separated by spaces, each on their own line, like this:


  brilliant 6
	dog 16
	is 1106
	the 5269
