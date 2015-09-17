---
title: Tokenization
slug: tokenization
---

With a cleaned-up source text, we can now tackle the more nuanced issues of breaking up the corpus into individual words that can subsequently be counted and reassembled into sentences.

For the purposes of clarity and correct language use, we're going to stop referring to the individual units as "words" and instead use the more correct term *token*. A token is an atomic unit within some collection, and it while many tokens will also be words, not all tokens will be words.

Let's use an example to discuss the difference between words and tokens. Say this is your source text:

	A on-demand water heater is a more "green" solution than traditional heaters.

The component *words* in this sentence are:

	['A', 'on', 'demand', 'water', 'heater', 'is', 'a', 'more', 'green', 'solution', 'than', 'traditional', 'heaters']

Assuming that `on-demand` is a *hyphenated phrase*, not a word, and the quotes around `green` are meant to be ignored, since there is no such thing as the word `"green"`. Notice that in reducing the sentence to its component words, all contextual meaning is lost.

However, if our goal is to break this sentence up into its component tokens, we are presented with some interesting choices. We could choose, for example, to count [hyphenated words](http://www.grammarbook.com/punctuation/hyphens.asp) as single tokens, thus preserving some of the original context. That choice would yield the following tokens:

	['A', 'on-demand', 'water', 'heater', 'is', 'a', 'more', 'green', 'solution', 'than', 'traditional', 'heaters']

We could further choose to retain enclosing quotes, wrapping up all their contents into a single token:

	['A', 'on', 'demand', 'water', 'heater', 'is', 'a', 'more', '"green"', 'solution', 'than', 'traditional', 'heaters']

This might prove useful when [quotes are used to denote titles or as "scare" quotes](https://en.wikipedia.org/wiki/Quotation_mark#Quotation_marks_in_English), yielding tokens like `"Hamlet"`, denoting Shakespeare's play, instead of `Hamlet`, the name of the title character or a capitalized word for a small village. But we run into a more difficult choice when dealing with quotations and direct speech. How should we tokenize the following sentence?

	"My, what big eyes you have," said Little Red Riding Hood.

Should we really count `"My, what big eyes you have,"` as a single token? Probably not. But the choice is ultimately up to you to decide *how* to tokenize.

One suggestion: start with a simpler pattern for tokens, and then gradually introduce more nuance. Eventually you might be able to tokenize words with their quotes only if there is a single word within quotes, or only if each word within the quotes is capitalized.

You will face many choices in how to design your tokenizer. Some things to be considerate of:

* Which characters should be ignored completely? (e.g. underscores, dashes, brackets, $, %, •, etc.)
* What counts as a single token?
* How should capitalization be handled? Should the two tokens `'Tree'` and `'tree'` be normalized into the same token `'tree'`?
* When should punctuation be included in the token? (e.g. `'etc.'`, `'Ms.'`, `'U.S.S.R.'`)

A side note on capitalization: if you choose to normalize words by converting them to lowercase, be aware that you will likely get false positives with this technique, for example the two "turkey"'s in: "Turkey melts are delicious." and "Turkey spans two continents." The country should be tokenized with capitalization, i.e. `'Turkey'`, whereas the animal should be tokenized as lowercase `'turkey'` so that it is treated as the same token as any other instance of `'turkey'`.

> [action]
>
Ready? Your task is to write a script to tokenize your cleaned-up corpus text, returning a list of tokens as strings. Got it? Go code!

Help! Where do I Start With this Tokenizing Business?
==
The best place to start is always with a smaller input (source text) so that you have something manageable to work with. When building your tokenizer, you will have to get your hands dirty with Regular Expressions, so begin by writing and refining regular expression patterns to tokenize chunks of text.

Let's do one together, building up a tokenizer function from scratch. Here's a sample source text from [The Adventures of Sherlock Holmes](http://www.gutenberg.org/ebooks/1661):

	One night--it was on the twentieth of March, 1888--I was returning from a journey to a patient (for I had now returned to civil practice), when my way led me through Baker Street. As I passed the well-remembered door, which must always be associated in my mind with my wooing, and with the dark incidents of the Study in Scarlet, I was seized with a keen desire to see Holmes again, and to know how he was employing his extraordinary powers.

This text is stored in the file `snippet.txt`. Then let's set up a skeleton `tokenize.py`:

	def tokenize(text):
	    tokens = []
	    return tokens

	if __name__ == '__main__':
	    import sys
	    source = open(sys.argv[1]).read()
	    tokens = tokenize(source)
	    print(tokens)

Now we can test this script by running

	$ python tokenize.py snippet.txt

Although of course at this point it doesn't do anything. So let's start by splitting the text in the source into tokens using whitespace as a delimiter:

> [solution]
>
	import re
>
	def tokenize(text):
	    tokens = split_on_whitespace(text)
	    return tokens
>
	def split_on_whitespace(text):
	    return re.split('\s+', text)
>
	if __name__ == '__main__':
	    # ...

Now we get a real list as our output:

	$ python tokenize.py snippet.txt
	['One', 'night--it', 'was', 'on', 'the', ...

And then we can add extra functions to further refine our list of tokens. First up, let's remove unwanted punctuation from the source text before splitting it up:

> [solution]
>
	def tokenize(text):
	    no_punc_text = remove_punctuation(text)
	    tokens = split_on_whitespace(no_punc_text)
	    return tokens
>
	def remove_punctuation(text):
	    no_punc_text = re.sub('[,.()]', '', text)
	    no_punc_text = re.sub('--', ' ', no_punc_text)
	    return no_punc_text
>
	# ...

Notice that the `remove_punctuation()` function has two steps: first, we delete all commas, periods, and parentheses by replacing them with an empty string. Then, we replace all double-dashes (meant to represent an em dash) with a single space so that the words connected by the double-dash are treated as separate tokens.

This should get you going. You can keep adding new steps to your `tokenize()` function until it performs as you want. If you keep your functions small and specific, it will be easier to change them over time as you (inevitably) discover ways to improve.

Where to Go From Here
==
Finished already? If you still have time left, make your code readable. Then consider doing more rigorous testing and making performance improvements:

* Are you sure that your tokenizer is performing exactly as you want? Write tests to ensure that you are getting the results you've designed for, and then run your tests with controlled input data.
* Come up with at least one other tokenization strategy and compare performance. Find ways to make your tokenizer as efficient as possible.
