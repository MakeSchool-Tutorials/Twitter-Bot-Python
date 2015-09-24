---
title: Counting Tokens
slug: counting-tokens
---

Now that our corpus has been tokenized, we're ready to start counting occurrences of individual words so our language model understands how common or rare each word is.

By simply counting tokens, we'll create a probability distribution that we can sample stochastically to generate a set of words that's representative of word usage frequency in our corpus. It won't be a grammatical sentence yet, but at least it's unlikely to contain rare words.

Since we already built our own fancy custom hash table, we'll use that to store our word counts.

First, initialize an empty hash table with `word_counts = HashTable()`. Now iterate over all words in the tokenized corpus.

Given a string `word` as the loop variable, we need to check if this is the first time we've seen `word` or if we've seen it before. In the former case, we just set its `count` value to 1, while in the latter we *increment* its previous `count` by 1. Here's a snippet of code demonstrating this logic:

  if word in word_counts:
      word_counts[word] = word_counts[word] + 1
  else:
      word_counts[word] = 1

We can compare the results of this word count dict with the Unix dictionary we used earlier the tutorial.

If we subtract the Unix dictionary's keys from our word count dict's keys, we can find specialized/jargon words in our corpus or words we didn't normalize. We can even use this to refine our regular expressions and normalization techniques!

Once you've counted the tokens and generated a histogram, you can re-use your stochastic sampler to generate random sentences based on the probabilities of token frequencies.

> [action]
>
Got it? You've done this before, so you can plug in your previous `histogram()` function to work with your fancy, cleaned-up tokens. Go code!

When you're finished, read on and complete the rest of this tutorial.
