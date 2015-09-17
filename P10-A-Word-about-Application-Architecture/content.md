---
title: A Word about Application Architecture
slug: a-word-about-application-architecture
---

At this point in the project, it is a good idea to pause, step back, and take a look at the architecture of the code base. We've written a fair amount of code without much explicit thought given to the design or composition. In all likelihood, our code is messy and convoluted and in need of some careful refactoring.

When approaching questions of application architecture, there are a lot of directions to choose from. To clarify thinking about how to structure the application, start with questions such as:

* What are the key features of the application? Are these clearly separated into their own files, classes, and/or modules?
* Are the names of files, modules, functions, and variables appropriate and accurate? Would a new programmer be able to understand the names without too much contextual knowledge?
* Can files be used as [both modules and as scripts](https://docs.python.org/3/tutorial/modules.html#executing-modules-as-scripts)?
* Do modules all depend on each other or can they be used independently?
* What are the scopes of variables and are they appropriate for their use case? If there are global variables, why are they needed?
* Are the functions small and clearly specified, with as few side effects as possible?

These questions will guide you towards thinking about application architecture in a more nuanced way. If you find that your code can use improvement (and what code *couldn't* use improvement), refactor until you are happy with it.

One example high-level architecture for the application so far might look like this, with each file acting as both a module and a script:

	app.py          # main script, uses other modules to generate sentences
	cleanup.py      # module for cleaning up source text
	sample.py       # module for pulling a sample word from a histogram
	sentence.py     # module for generating a sentence from a list of words (tokens)
	tokenize.py     # module for creating lists of tokens from a text
	word_count.py   # module for generating histograms from a list of tokens

The primary interface to this application could be something like:

	$ python app.py corpus.txt
	Some random generated sentence based on word frequency.

And `app.py` could be something like this:

	import cleanup
	import tokenize
	import word_count
	import sample
	import sentence

	# define some functions that compose the above modules

	if __name__ == '__main__':
	  # code to run when file is executed

This is just one way to organize the code. What's important is that you think carefully about what kind of design makes the most sense for your code base.
