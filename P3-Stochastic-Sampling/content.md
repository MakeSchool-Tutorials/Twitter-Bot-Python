---
title: Stochastic Sampling
slug: stochastic-sampling
---

Now that we can generate histograms (word frequency distributions), the next step is to start pulling words out of the histogram to produce sentences.

Eventually, we'll want this word-selection function to pull out words in an order that makes some kind of sense and is not completely random. Our bot may never be able to speak as well as Siri, but it can at least *approximate* real English grammar.

Before we get into that territory, we are going to build a program that can generate "sentences" from the words in the histogram through random selection or *stochastic sampling*, to use a more technical term.

Stochastic sampling means taking an element from a given collection at *random*. In this case, we'll start with pure randomness (or at least, as pure as a computer can get) and then introduce probabilistic selection to sample semi-randomly.

> [action]
>
Your first task is to write a function that takes a histogram (however you've structured yours) and returns a single word, at random. It should not yet take into account the distributions of the words.
>
It should work something like this:
>
	$ python sample.py one fish two fish red fish blue fish
	two
>
	$ python sample.py one fish two fish red fish blue fish
	fish
>
	$ python sample.py one fish two fish red fish blue fish
	blue
>
As usual, you can name your files and functions however you like, this is merely a template. Additionally, if you'd rather provide a file as the command-line argument instead of a list of words, you can do that too:
>
	$ python sample.py onefish.txt
	fish
>
	$ python sample.py onefish.txt
	two

Once you have that working, can you prove that your code is truly random? Granted, proving randomness is much more difficult than disproving it, but you can get close. Is the probability that any word is selected the same as for any other word? In other words, given the example above, is the likelihood that `sample.py` will print the word `'red'` the same as the likelihood it will print `'fish'`?

Then, once we have true randomness, let's introduce some weighting to change the probabilities for different words. A word with a higher weighting will have a higher probability of being selected.

The first weighting we'll apply is a frequency weighting: words which appear more frequently in the original text should be more likely to be selected by our sampling program.

For example, when using the sentence above, our sampling function should return `'fish'` 4 times as often as the word `'red'`.

The probabilities for each word in the sample sentence are as follows:

	one  => 0.125
	fish => 0.5
	two  => 0.125
	red  => 0.125
	blue => 0.125

Or, put in percentages, 50% for `'fish'`, and 12.5% for all the rest.

> [action]
>
There are many ways to use relative probabilities to generate these words. See if you can devise your own strategy, and then make improvements to it by comparing your work with your peers and examples you find online.
>
Ready? Go code!

Help! What is all this Stochastic Madness?
==
If you're feeling overwhelmed by the frustrating indeterminacy of all this, you're not alone. One of the nice, pleasant, comforting things about computer programming is working with perfectly reproducible, empirical processes. In general, it is best to design software that will produce the same outputs when given the same inputs.

Sometimes, though, we need randomness to help out. When we venture into the world of stochastic processes, we can no longer work with perfectly predictable outcomes, but instead must learn to work with relative probabilities.

If you're having trouble telling whether or not the probabilities you are designing for are actually being represented by your code, you can test your code by doing some basic statistical analysis on your outputs: quite simply, run your code a bunch of times and see if the probabilities for different outputs match what is expected.

For example, to test that my probabilistic word sampler program was working correctly, I selected a random word from the histogram 10,000 times and then looked at how frequently each word was selected:

	blue => 1295
	fish => 5048
	red  => 1207
	two  => 1209
	one  => 1241

None of these numbers are clean, round numbers. But that's fine, because the relative probability reflects what I expect (and designed for): `'fish'` appears about 4 times as often as each of the other words, which all appear in roughly the same frequency. It's tough to prove perfect randomness, but so long as the sample size is big enough and the outcomes match what's expected, we can be mostly certain that we've reached correctness.

Where to Go From Here
==
Finished already? If you still have time left, make your code readable. Then optimize and experiment:

- Optimize for speed of sampling (read time)
- Optimize for memory (use the least amount of space)
- Solve with only lists and tuples (no dictionaries)
- Combine other weighting techniques besides on top of the frequency weighting (e.g. weight words that begin with a vowel more than others)
