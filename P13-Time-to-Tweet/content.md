---
title: Time to Tweet
slug: time-to-tweet
---

The time has come. We've reached the final step. It's time to connect take all of our hard work, to give our bot a body where it can live on and on, generating strange and hilarious non sequiturs for eons to come. Or at least, as long as you can keep it up on Twitter without getting shut down by their spam filter.

In this tutorial, we're going to create a fake Twitter account that we can program to tweet out our generated phrases. When we're finished, your web app will include a "Tweet This" button next to the generated quote which, when pressed, will tweet the quote to the fake Twitter account.

There are a few steps involved to integrate with Twitter. For the purposes of this tutorial, we're going to use [Single-User OAuth](https://dev.twitter.com/oauth/overview/single-user) to authenticate our application with the Twitter API, allowing us to post tweets to our fake user account.

Ok, let's get set up. Start by completing the following steps:

- Create a new Twitter account at [https://twitter.com/signup](https://twitter.com/signup)
- Create a new Twitter application at [https://apps.twitter.com/](https://apps.twitter.com/)
- Generate API tokens ([docs](https://dev.twitter.com/oauth/overview/application-owner-access-tokens))

To send an authenticated request to the Twitter API, you need (at least) 5 things:

1. The API endpoint to send a request to (in this case, we'll just be using the [POST statuses/update endpoint](https://dev.twitter.com/rest/reference/post/statuses/update) of the REST API)
2. The consumer key for your application (tells the Twitter API which application is sending the request)
3. The consumer secret for your application (used to sign the request)
4. The access token key for your application (tells the Twitter API which user is sending the request)
5. The access token secret for your application (used to sign the request)

Putting all of these pieces together, you can create an OAuth-compliant HTTP request to send to the Twitter REST API.

That's a lot of jargon. If your head is spinning, no worries. It's not critical that you understand how all of this works, as we're going to use a library to take care of the difficult parts for us. OAuth is worthy of study on its own, but it is beyond the scope of this tutorial.

The important pieces you need for now are those keys and secrets. The first thing we'll do is add those values to our application.

Adding Keys and Secrets
==
Create a new file in your project called `twitter.py`. In this file, we'll write code to create authenticated requests to send to the Twitter API and define a function to let our main application code send tweets.

Next we'll add the keys and secrets from our Twitter application. But, we run into an issue of security here - those keys and secrets should be kept secret, which means that they should absolutely NOT be stored in our version-controlled source code.

So how do we get around this problem of needing to access a value without including it in our source code? Environment variables to the rescue!

Every program you execute has access to the [environment variables](https://en.wikipedia.org/wiki/Environment_variable) for its process. These variables are private to the process, so no outside parties can see them. This is a great way to store data that we want to keep private.

To see what the environment variables are for a particular Python program, add these lines:

	import os
	print(os.environ)

This will show you a dictionary of all the environment variables available to the process. While many of these will be system-defined, you can also set your own.

There are a few ways to set environment variables. The simplest is to prefix a shell command with a list of environment variable assignments:

	$ MY_VAR=hello python script.py

In the above example, the environment variable `MY_VAR` was set to the string `'hello'` for the process running `python script.py`. `script.py` would then be able to retrieve the value `'hello'` by calling `os.environ.get('MY_VAR')`.

This method can get kinda clunky and repetitive if you have to enter in the environment variables every single time you run your script. No fun.

So a common pattern to make things simpler is to store your environment variables in a file (commonly named `.env`) and loading them dynamically in your Python script. If you don't add the `.env` to your version control repository, it won't become public, thus maintaining security.

To help us solve this problem, we'll create a `.env` file to store our environment variables and use the [python-dotenv package](https://github.com/theskumar/python-dotenv) to load them into our `twitter.py` script.

Create a file in the root directory of your project called `.env`. Then declare and assign environment variables for your Twitter consumer key & secret and access token key & secret (one per line). It should look something like this:

	TWITTER_CONSUMER_KEY=yourconsumerkeyhash
	TWITTER_CONSUMER_SECRET=yourconsumersecrethash
	TWITTER_ACCESS_TOKEN=youraccesstokenhash
	TWITTER_ACCESS_TOKEN_SECRET=youraccesstokensecrethash

Then, in `twitter.py`, import the `dotenv` module (you'll need to install it with `pip3`, of course) and load your `.env` file:

	import dotenv
	dotenv.load_dotenv('.env')

After doing that, your `os.environ` dictionary should contain all of the variables defined in `.env`. Make sure that they do by calling `os.environ.get()` for each of the variable names.

Finally, to make sure that you don't ever commit `.env` to your version control repository, add it to your `.gitignore` file with the following command:

	$ echo ".env" >> .gitignore

Be sure to commit changes to your `.gitignore` file.

We've just solved a core security issue in software development: making private values accessible to a program without publicly exposing them.

Be aware that because you don't include `.env` in your repository, when you push your application to Heroku, it won't have access to the same environment variables. To resolve this issue, you need to [set environment variables explicitly for your Heroku instance](https://devcenter.heroku.com/articles/config-vars).

Interacting with the Twitter API
==
By now, you should have a `twitter.py` script that looks something like the following:

	import os
	import dotenv

	dotenv.load_dotenv('.env')

	consumer_key = os.environ.get('TWITTER_CONSUMER_KEY')
	consumer_secret = os.environ.get('TWITTER_CONSUMER_SECRET')
	access_token = os.environ.get('TWITTER_ACCESS_TOKEN')
	access_token_secret = os.environ.get('TWITTER_ACCESS_TOKEN_SECRET')

The next step is to send a request to the Twitter API. To do this, we'll be using our old friend the [requests library](http://www.python-requests.org/) and its corresponding OAuth library, [requests-oauthlib](https://github.com/requests/requests-oauthlib).

Start off by installing them both:

	$ pip3 install requests requests_oauthlib

Then, add the following code to `twitter.py` to create a new authorized session:

	from requests_oauthlib import OAuth1Session

	session = OAuth1Session(consumer_key,
	                        client_secret=consumer_secret,
	                        resource_owner_key=access_token,
	                        resource_owner_secret=access_token_secret)

We'll use the `client` object to send OAuth-signed requests to Twitter. Let's try it out. Add the following lines of code to the end of `twitter.py`:

	# The URL endpoint to update a status (i.e. tweet)
	url = 'https://api.twitter.com/1.1/statuses/update.json'

	# The contents of status (i.e. tweet text)
	status = 'If you are reading this on Twitter, the API request worked!'

	# Send a POST request to the url with a 'status' parameter
	resp = session.post(url, { 'status': status })

	# Show the text from the response
	print(resp.text)

Then run the script:

	$ python twitter.py

It may take a few seconds for the request to process and a response to come back, but you should see a JSON dump soon. And then, if you go look at your timeline on Twitter, you should see that new tweet there!

Now that we've got the API integration working, let's wrap it up into a nice utility method so that the rest of our app can import our `twitter` module and use it to easily post tweets. Define the following function:

	def tweet(status):
	    resp = session.post(url, { 'status': status })
	    return resp.text

Now we can easily use this function from anywhere else in our app and tweet away! We can even tweet from the REPL:

	$ python
	>>> from twitter import tweet
	>>> tweet('Chirp.')
	...
	>>> tweet('Blirp.')
	...

Add a Tweet Button to the Web App
==
The final step of this tutorial is to add a "Tweet This" button to the web application. Two things are needed to accomplish this task:

1. A new POST route in our Flask server file (i.e. `app.py`) that will tweet out whatever data is sent to it.
2. An HTML form and button in the `index.html` template which will send a POST request to our new route along with the contents of the generated text

Let's start with the new route. It will look a lot like our original route, except we'll restrict it to just `POST` requests. Add this to the main app file:

	@app.route('/tweet', methods=['POST'])
	def tweet():
	    status = request.form['sentence']
	    print(status)

To send a POST request from `index.html`, add a `<form>` tag with an `action` attribute set to `/tweet` (the route to post data to). Nested within your form, add a hidden `<input>` tag with a name of `sentence` and a value equal to the generated sentence. Here's an example:

	<blockquote>{{ sentence }}</blockquote>
	<form action="/tweet" method="post">
	  <input type="hidden" name="sentence" value="{{ sentence }}">
	  <button type="submit">Tweet this</button>
	</form>

With this code in our HTML, fire up the web server, open a browser, and click on the "Tweet this" button. You should see the value of the generated sentence printed to your server log console.

*Note: to do this, you will likely need to import the `request` object from `flask` in addition to the `Flask` and `render_template` functions.*

Now that we have access to the sentence from within the `/tweet` route, all we have to do is use our `twitter.tweet()` function to actually post it to Twitter! Update the route to the following:

	@app.route('/tweet', methods=['POST'])
	def tweet():
	    status = request.form['quote']
	    twitter.tweet(status)
	    return redirect('/')

Again, to make this work you'll need to also import the `redirect` function from `flask` and also the `twitter` module that we built earlier.

With that in place, we finally have a fully functional Twitter integration complete with a user interface! Time to celebrate! Or, if you prefer, keep building more features... ;D

Stretch Goals
==
- Seed sentences with a word (build backwards and forwards). Make this functionality available via a new Flask route
- Make Twitter bots "listen" and respond to other bots when they tweet a certain keyword (or set of keywords). Use the same keyword to seed the response. (Make sure to time-limit this otherwise could create infinite feedback loop between bots and get them booted from the API)
