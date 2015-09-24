---
title: Let's Build a Twitter Bot
slug: lets-build-a-twitter-bot
---

Let's build something fun and silly! :-) Let's make a Twitter bot that generates sentences that sound realistic, but not always sensible. Here's an example from the Ron Paul bot:
> What currency are your thoughts on abolishing America's income tax altogether and get others to look into our private communications.

So how do we make this? Well, not all at once. We're gonna do it in parts, step-by-step. We'll start by making a simple Python script that can randomly generate words from a dictionary. We'll iteratively build until we have a full-fledged language model that can generate approximately grammatical sentences. To learn a basic grammar, we'll need to collect a set of documents and parse their text to learn relationships between words. Once we have trained a basic language model that we're satisfied with, we'll deploy it to a web server on Heroku using the Flask framework, and finally connect it to Twitter.

Getting Started
==

We'll be using *Classroom for GitHub* to manage assignments throughout the program. You'll need to:

1. Go to the [assignment page](https://classroom.github.com/assignment-invitations/ee7800512cf80bc1faba6ef2bb229780)
2. Accept the assignment and follow the link to the created repository
3. Clone the repository and use it throughout this project.

> [info]
> 
> Don't worry if you are not familiar with using Git and GitHub yet -- there will be an in-depth lecture soon.
> 
> Before we go any further, you will need to create a GitHub account. Your GitHub account will function as a professional portfolio of your work so be sure to choose a professional username that you won't be embarrassed to put on your résumé!
>
> Go to [GitHub](https://github.com/join) and create an account with a professional username. Choose *Free* on step two.
>
> You can skip this step if you already have a GitHub account!

<!-- Make School -->

> [action]
> 
> Now, let's make sure that you have an SSH key set up with GitHub. Type the following command into the *Terminal* app:
> 
>       ssh -T git@github.com
> 
> If it says something like `Permission denied (publickey).` then you need to [follow the steps here](https://help.github.com/articles/generating-ssh-keys/).
> 
> You should also make sure that Git knows who you are. Enter the following commands in the *Terminal* app (using your actual name and email address associated with your GitHub account):
> 
>       git config --global user.name "Your Name"
>       git config --global user.email "your.name@students.makeschool.com"
> 
> You can add additional emails to your GitHub account [here](https://github.com/settings/emails).

<!-- Make School -->

> [info]
> 
> Now that Git is all set up, we can clone the repository with the following command:
> 
>       git clone {clone SSH URL}
> 
> You can find the SSH clone URL on the right hand pane of the repository that was created for you. Make sure to use the SSH clone URL (not HTTPS). The complete command should look something like this:
> 
>       git clone git@github.com:MakeSchool-17/twitter-bot-python-username.git
> 
> Now that you have your repository cloned, you can use the `cd` command to change directories into the repository.
> 
>       cd twitter-bot-python-username
> 
> `twitter-bot-python-username` should match the directory that was created by `git clone` (it's the same as name of your repository).
> 
> You should get into the habit of *commiting* and *pushing* your code at the end of each page of this tutorial. You can do commit and push with the following commands:
> 
>       git add --all
>       git commit -m "{summary of changes}"
>       git push origin master
> 
> This always has to be done from within the repository's directory so make sure to `cd` into it first if you have not already.
> 
> `git add --all` adds all the files to staging so they are ready to be saved. `git commit` commits the changes -- kind of like taking a snapshot. `{summary of changes}` should be a short summary of what the commit contains. Try to keep this under 50 characters in length. Finally, `git push origin master` *pushes* those the *commit* to GitHub. This saves backs up your changes online and allows the instructors to see your code.
> 
> We'll go over all of this and more in a lecture so don't worry if it doesn't make sense yet. Just make sure to commit and push your changes regularly using the process above.

Python Specs
==

Before you get going, there are two specifications (or *specs*, as they are commonly referred to) for this project:

1.  Use **Python 3** (not Python 2).

2.  Use modules. Modules are a common pattern in Python that allows files to be run independently (as a *script*) and also imported by other files (as *modules*).

For example, here is a program that will return a random Monty Python quote -
`python_quote.py`

	import random

	quotes = ("It's just a flesh wound.",
	          "He's not the Messiah. He's a very naughty boy!",
	          "THIS IS AN EX-PARROT!!")

	rand_index = random.randint(0, len(quotes) - 1)
	print(quotes[rand_index])

This code can only be run as a script because all lines are always executed, so importing this as a module into another program would make it print out a quote, even if we only want to access the `quotes` tuple. In Python, the common pattern for making files dual-purpose (modules as well as executables) is to use a conditional that will only run certain code if the current process file is the current source file, like this:

	import random

	quotes = ("It's just a flesh wound.",
	          "He's not the Messiah. He's a very naughty boy!",
	          "THIS IS AN EX-PARROT!!")


	def random_python_quote():
	    rand_index = random.randint(0, len(quotes) - 1)
	    return quotes[rand_index]

	if __name__ == '__main__':
	    quote = random_python_quote()
	    print(quote)


This script can be imported within another file in the same directory with `import python_quote`, which would give access to the `random_python_quote()` function, or it can be executed directly, like so:

	$ python3 python_quote.py
	It's just a flesh wound.

	$ python3 python_quote.py
	He's not the Messiah. He's a very naughty boy!

> [info]
>
> When using `virtualenv`, both the `python` and `python3` commands are aliases to the actual python command, so you can use either.


Rearrange Words
==

Off to the races! The first step is to get our bearings with Python by writing a basic script with only a few moving parts.

To start, we'll be building a script that randomly rearranges a set of words provided as command-line arguments to the script.

For example, if you run the following command (assuming your script name is *rearrange.py*, though you can use any name you like)

	$ python3 rearrange.py how now brown cow

You should see output like this:

	brown cow now how

Of course, because it is random, repeated executions of the script with the same arguments will produce different results:

	$ python3 rearrange.py how now brown cow
	brown cow how now

	$ python3 rearrange.py how now brown cow
	now how cow brown

That's it. You have all of the specs needed to produce a correct program.

> [action]
>
So, are you ready? Go code!

<!-- html comment to break boxes -->


*Wait! I don't know Python!*
==
No problem! Python is a friendly language with lots of great resources for those of us who are just getting started.

One recommended site is the [official Python 3 Tutorial](https://docs.python.org/3/tutorial/), although you are welcome (and encouraged!) to use other resources to learn.

Some of the topics you'll definitely need to research are:

- variable assignment
- function definitions
- core data types: strings, integers, floats
- collection types: lists, tuples
- the system module (for accessing command-line arguments)
- the random module (for generating random numbers)

Where to Go From Here
==

> [action]
>
Finished already? If you still have time left, you can keep building off of this idea or try writing other programs that work with words, for example:

- String reversals: reverse words, sentences
- An interactive "mad libs" script
- Anagram generator
