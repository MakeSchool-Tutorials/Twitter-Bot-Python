---
title: Counting Tokens
slug: counting-tokens
---

## Tokens and Counts in a Hash Table

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

## Better Frequency Analysis With Heaps

A common question when doing text analysis on a corpus is "what are the most common words?" With your word-counting histogram, it is possible to answer this question, but it is not the _optimal_ data structure for answering this question.

The reason for that is because your histogram maps **words** to their **counts**, not the other way around. More importantly, it is an unsorted structure by design, and finding the `n` most common words is a matter of sorting words by their frequency and then taking `n` words from the top of that list.

When we want to ask questions of "most" and "least", we usually want to use a data structure optimized for sorting. There are all kinds of ways to accomplish this, but for the sake of this tutorial we're going to explore a common and essential data structure: the **heap**.

> [info]
>
A [heap](https://en.wikipedia.org/wiki/Heap_(data_structure)) is a tree-based data structure. It organizes information as a collection of nodes, with each node having 0 or more children and one parent (except for the _root_ node, which has no parent). Nodes with no children are called "leaf" nodes.
>
A common type of heap is the _binary heap_, in which each node has at most 2 children. Carnegie Mellon has a [good description of binary heaps](http://www.cs.cmu.edu/~adamchik/15-121/lectures/Binary%20Heaps/heaps.html).

For our purposes, we'll be building a binary heap that prioritizes words by their count in descending order (highest count first). The advantage of this data structure is that we can efficiently find the word with the highest count, and also add word-count nodes to the heap and know that they will be prioritized correctly.

> [action]
>
Create your own implementation of a heap data structure. It should be prioritized by maximum value (i.e. highest count of words on at root), and support the following operations:
>
- **peek**: inspect the root node
- **insert**: add a new node
- **delete_max**: remove the root node (internally, heap will need to be _balanced_)

Once you have a heap, you can add word-count data to populate it, and know that the root node will always be the top-count word. With this feature, you can use your heap as a [priority queue](https://en.wikipedia.org/wiki/Priority_queue), where `delete_max` acts like `pop`, and repeated use will give you access to the `n` most common words.

Here's an example interface for a heap in Python:

    from heap import Heap

    counts = Heap()             # create a new heap
    counts.insert((3, "the"))   # insert some tuples of count/word pairs
    counts.insert((2, "dog"))
    counts.insert((1, "apple"))
    counts.insert((5, "red"))
    counts.insert((1, "ate"))

    counts.peek()               # => (5, "red")
    counts.delete_max()         # => (5, "red")
    counts.peek()               # => (3, "the")

Notice that the `delete_max()` method above is destructive: it modifies the heap in place.

> [action]
>
When you're finished implementing your heap, add it to your project as a separate module and provide a simple interface for finding the `n` most common words in a given corpus.

Help! I'm in a Heap of Trouble
==

First of all, apologies for the pun. Couldn't help it. Now then, what about heaps?

Heaps are actually quite simple, but it can be a little tricky to see how to implement them efficiently. First of all, it's helpful to visualize a heap so that our brains can see the structure as something concrete.

Here is a binary heap of the values 9, 4, 7, 3, 1, and 6, prioritized by maximum value:

    root -> (9)
        +----+----+
       (4)       (7)
     +--+--+   +--+
    (3)   (1) (6)

Take note of a few things about this heap: each child node is _less than_ its parent, and each node has no more than 2 children. The leaf nodes are all on the bottom, and filled out from left to right.

Is there anything about this tree that seems odd to you? What about that 6 below the 7 there? What's up with that? Intuitively, we might expect the 6 to not be on a lower level than the 4, but because it is not a _child_ of 4, it doesn't break the rules of a heap. A heap only says that each parent must be larger than its children. It does not dictate rules for siblings or cousins or aunts or uncles (that's a bit of poetic license there, we don't normally call nodes in a tree "cousins").

This flexibility allows heaps to be very efficient, but it means that you can't depend on nodes being absolutely ordered: a parent node is only guaranteed to be greater than all of its children, and that does not necessarily mean that it is greater than all other nodes in the heap. Unless of course it is the root node, in which case all other nodes in the heap _are_ its children or decendents of its children.

Keep that fact in mind when working with heaps.

Then, let's explore possible implementations of a heap. We could go with a list of node objects that point to two (optional) children. But there is another nifty way to store a binary heap: as a flat, ordered list.

To demonstrate, here is the heap from above as a list:

    [9,4,7,3,1,6]

In this structure, you can find the parent or child nodes for any given node by using its index. To find the parent, simply subtract one from the index and divide by 2:

    # index for parent of node 1 (index 4)
    (4 - 1) // 2
    # => 1

Index 1 (element with value 4) is indeed the parent node. To find the child nodes, multiply the index by 2 and add 1 (to find the left child) and 2 (to find the right child):

    # indices for children of node 7 (index 2)
    (2 * 2) + 1
    # => 5
    (2 * 2) + 2
    # => 6

5 is the index of node 7's left child (node 6), but the index 6 is out of bounds, i.e. node 7 doesn't have a right child. Make sure to account for that case in your code.

With that simple arithmetic, we can store a binary heap in a good old Python list. Hurrah!

For a more detailed description, this [article shows a binary heap implementation in Python](http://interactivepython.org/runestone/static/pythonds/Trees/BinaryHeapImplementation.html). If you choose to use it please don't waste your own learning by copy-pasting the code. Make sure you internalize and understand the concept before writing your own implementation.

The same goes for the example solution shown below: read and study, don't copy blindly!

> [solution]
>
    class MaxHeap():
        def __init__(self):
            self.list = [None] # Add null object to make tree arithmetic simpler
            self.size = 0
>
        def peek(self, n):
            # look at first n nodes of heap
            return self.list[1:n+1]
>
        def insert(self, node):
            # push item onto heap
            self.list.append(node)
            self.size += 1
            self.sift_up(self.size)
>
        def delete_max(self):
            # remove the root and restructure heap
            key = self.list[1]
            self.list[1] = self.list.pop()
            self.size -= 1
            self.sift_down(1)
            return key
>
        def sift_down(self, idx):
            while idx * 2 <= self.size:
                key = self.list[idx]
                max_child_idx = self.max_child(idx)
                max_child = self.list[max_child_idx]
                if max_child > key:
                    self.swap(idx, max_child_idx)
                idx = max_child_idx
>
        def sift_up(self, idx):
            while idx // 2 > 0:
                key = self.list[idx]
                parent_idx = idx // 2
                parent_key = self.list[parent_idx]
                if key > parent_key:
                    self.swap(idx, parent_idx)
                idx = parent_idx
>
        def max_child(self, idx):
            l_idx = idx * 2
            r_idx = idx * 2 + 1
            if r_idx > self.size:
                return l_idx
            if self.list[r_idx] > self.list[l_idx]:
                return r_idx
            else:
                return l_idx
>
        def swap(self, idx_a, idx_b):
            key = self.list[idx_a]
            self.list[idx_a] = self.list[idx_b]
            self.list[idx_b] = key
