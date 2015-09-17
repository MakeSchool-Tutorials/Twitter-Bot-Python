---
title: Parsing Text: Clean Up
slug: parsing-text-clean-up
---

Once we have a corpus, we first need to clean up the text to make sure we parse and count only legitimate words, excluding any HTML tags or symbols. Here's an example sentence found in the Ron Paul corpus:

> *The belief that money created out of thin air can work economic miracles, if only properly “managed,” is pervasive in D.C.*

Fortunately, Diffbot extracts the relevant text from the soup of HTML tags, converting this:

	<p><font face="Times New Roman, Times, serif" size="3">The belief that money created out of thin air can work economic miracles, if only properly &#147;managed,&#148; is pervasive in D.C. </font></p>

into this:

	The belief that money created out of thin air can work economic miracles, if only properly &#147;managed,&#148; is pervasive in D.C.

However, we're still left with smart-quotes around *“managed”* that are HTML-encoded (if you search for `&#147;` or any other HTML character code on Google, it will convert the code to its corresponding character).

Our goal is to generate realistic sentences, so having jumbles of non-English within our generated text is not idea. So, we need to figure out a way to remove those unwanted characters.

The name for this process is *parsing*, and you can think of parsing like a filter that you pass data through: only the stuff you want gets through, and the cruft gets left behind. In some cases, you might do some *conversions*–for example, converting all HTML character codes to their ASCII/Unicode equivalent (take a look at this [list of HTML to ASCII character conversions](http://www.ascii.cl/htmlcodes.htm)).

Your job is to write a parser that will clean up your corpus text, converting it to a more usable form that you will then use to *tokenize* in the next tutorial, and from those tokens start generating sentences.

Exactly *how* you clean up your corpus is up to you. Some steps you may want to consider:

* Removing unwanted punctuation (e.g. _ or * characters around words)
* Converting HTML character codes to their ASCII equivalent (e.g. &#8212; to —)
* Normalizing punctuation (e.g. convert both types of quotes – "" and “” – to regular quotes "")

One important piece to keep in mind, however, is that as you keep developing this project you will likely notice certain things that should have been cleaned up but that you missed earlier. For example, maybe your corpus includes a few occurrences of the § (section) character.

You'll want to change your clean-up parser to remove those characters, so it is a good idea to *design your parser so that it is easy to modify in the future.* There are many techniques to encourage this, some important ones being: proper naming of variables and functions, separating concerns, and writing many small & specific functions instead of a few large and general-purpose ones.

> [action]
>
Got it? Ok great, go code!

Help! I Can't Parse this Parsing Stuff!
==
Parsing may seem daunting, but you'll soon realize that it's conceptually quite simple. Additionally, there are plenty of tools in the Python core language and standard library to help us out.

To help wrap your head around parsing, start off by tackling a more manageable chunk of text like a short sentence. This technique of starting small and learning through rapid experimentation is one you've probably noticed being encouraged over and over, and is one that you should reach for as second-nature soon enough.

Start by picking a sentence that has at least one character you want to remove, and a few others that you want to convert. Then, work step-by-step to figure out how to remove/replace these characters from the source text, returning the "cleaned-up" new text.

Some strategies for approaching this (which you are welcome to expand upon or come up with your own):

* Iterate through each character of the source text, and append it to a list of "clean" characters *unless* it is a character to be removed (you can use [list membership operations](https://docs.python.org/3/library/stdtypes.html#common-sequence-operations) to do this). After iterating through all of the characters, return the joined list of "clean" characters.
* Iterate a list of characters to remove, check if they are found in the source text, and if so delete them. Do the same for characters (or character sequences) to replace, except don't remove them, but, you know, *replace* them.

Some tools you may find useful (by no means an exhaustive list, just a starting point):

* [str.find()](https://docs.python.org/3/library/stdtypes.html#str.find), [str.split()](https://docs.python.org/3/library/stdtypes.html#str.split), [str.replace()](https://docs.python.org/3/library/stdtypes.html#str.replace)
* [regular expression (re) library](https://docs.python.org/3/library/re.html)
* [html library](https://docs.python.org/3/library/html.html)

Where to Go From Here
==
Finished already? If you still have time left, make your code readable. Then improve design and performance:

* Is your parser as fast as it could be? What is the difference between using regular expressions and character-by-character iteration?
* Modify your parser so that it can be used as both a module (imported by another script) and as a stand-alone, executable script that, when invoked from the command line with a file argument, will print out the cleaned-up version, e.g.:

	$ cat source.txt
	We _really_ love parsing &#38; tokenization.

	$ python cleanup.py source.txt
	We really love parsing & tokenization.
