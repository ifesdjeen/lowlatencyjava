# Approximate Data Structures

Approximate (also called Probabilistic) Data Structures are data structures that allow optimizing
space for the precision of the answer. Usually they use some sort of `hash function` to represent
the set in the most compact manner. In comparison with the deterministic data structures,
they consume less memory and can be queried in a constant time.

Here we will talk mostly about the algorithms that are used heavily in stream processing, such
as `Bloom Filters`, used for set membership queries, `HyperLogLog` used for the cardinality
estimation, `Streaming Histogram` that represents distribution of data over the seen sequence.
Also, `Skip List` used for the fast fast searches within an ordered sequence of elements.

## Count Min Sketch

[Count-min sketch](http://dimacs.rutgers.edu/~graham/pubs/papers/cm-full.pdf) is a data structure
for summarizing data streams. With a Count-min Sketch you can approximate cardinality (how many
times a certain element was seen) of the stream.

Count min sketch helps you to understand how many times certain item has occurred within the stream.
You can think about it as about the database which you can __only retrieve a count from__,
without being able to __retrieve a specific value__.

### Example

Let's say you run a website, schwitter.com and you'd like to know how many times each visitor
has been on the website. The most straightforward implementation is to create a hash table
and associate username with a counter. Although, as your userbase grows you start noticing
that you have to use more and more space to back these queries.

Using Count-min sketch, you can get an estimate of how many times the user `@alex` has
visited your website with a certain probability.

Another property of this data structure is that it's biased. It can overestimate
(respond with the number that is larger than the expected value), but never underestimate.
In other words, the best way to formulate it's answers is "at least {$$}N{/$$} times".

### General Idea

Create a table of integer numbers of `width` {$$}w{/$$} and `height` {$$}d{/$$} and
fill it with zeroes.  Take {$$}d{/$$} independent hash functions. Hash function should
return a value from {$$}0{/$$} to {$$}w{/$$}. For the sake of example, let's take 3
{$$}w=10{/$$} and {$$}d=3{/$$}. On the first step, our table would like that:

```
+---+---+---+---+---+---+---+---+---+---+---+
| 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 |
+---+---+---+---+---+---+---+---+---+---+---+
| 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 |
+---+---+---+---+---+---+---+---+---+---+---+
| 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 |
+---+---+---+---+---+---+---+---+---+---+---+
```

We'd like to initialize our sketch with a value of `@alice`, as she just visited the
website. For that, we first calculate the hash value of {$$}d[1]("alice"){/$$}. Let's
say our functions have returned `5`, `3` and `10`:

{$$}
d\textsubscript{1}("alice")=5;
d\textsubscript{2}("alice")=3;
d\textsubscript{3}("alice")=10;
{/$$}

That means that we have to update (increment) the 5th, 3rd and 10th column in the
corresponding rows:


```
  0   1   2   3   4   5   6   7   8   9   10
+---+---+---+---+---+---+---+---+---+---+---+
| 0 | 0 | 0 | 0 | 0 | 1 | 0 | 0 | 0 | 0 | 0 |
+---+---+---+---+---+---+---+---+---+---+---+
| 0 | 0 | 0 | 1 | 0 | 0 | 0 | 0 | 0 | 0 | 0 |
+---+---+---+---+---+---+---+---+---+---+---+
| 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 1 |
+---+---+---+---+---+---+---+---+---+---+---+
```

So far so good. Now the user `@bob` has visited the website. For his username,
hash functions return `8`, `2` and `4`:

{$$}
d\textsubscript{1}("bob")=8;
d\textsubscript{2}("bob")=2;
d\textsubscript{3}("bob")=4;
{/$$}

And we update 8th, 2nd and 4th columns of corresponding rows again:

```
  0   1   2   3   4   5   6   7   8   9   10
+---+---+---+---+---+---+---+---+---+---+---+
| 0 | 0 | 0 | 0 | 0 | 1 | 0 | 0 | 1 | 0 | 0 |
+---+---+---+---+---+---+---+---+---+---+---+
| 0 | 0 | 1 | 1 | 0 | 0 | 0 | 0 | 0 | 0 | 0 |
+---+---+---+---+---+---+---+---+---+---+---+
| 0 | 0 | 0 | 0 | 1 | 0 | 0 | 0 | 0 | 0 | 1 |
+---+---+---+---+---+---+---+---+---+---+---+
```

Now, `@alex` has visited the website, and the hash functions return

{$$}
d\textsubscript{1}("alex")=8;
d\textsubscript{2}("alex")=1;
d\textsubscript{3}("alex")=5;
{/$$}

And it looks like we've hit our first collision: in the first row,
we already have updated the 8th row when we were calculating counts
for `@bob`.

### Algorithm

The algorithm itself is quite straightforward:

  * {$$}d{/$$} is calculated from {$$}d = \lceil {ln {\frac{1}{\sigma}}} \rceil{/$$}
  * {$$}w{/$$} is calculated from {$$}w = \lceil {\frac{e}{\varepsilon}} \rceil{/$$}
  * You take {$$}d{/$$} linear hash functions

### Update

  * You for rows with indexes from {$$}0{/$$} to {$$}d-1{/$$} (zero-based index), you calculate a hash of given value
  * Having a current update row denoted as {$$}i{/$$}, and {$$}i{/$$} hash-function denoted as {$$}h{/$$}, and current value
    in your table denoted as {$$}old{/$$}
  * You update a table at position {$$}[i, h(value)]{/$$} to {$$}old + 1{/$$}

## Lookup

  * You for rows with indexes from 0 to {$$}d-1{/$$} (zero-based index), you calculate a hash of given value
  * Having a current update row denoted as {$$}i{/$$}, and {$$}i{/$$} hash-function denoted as {$$}h{/$$}
  * You find a maximum of values at positions {$$}[i, h(value)]{/$$}
  * Profit!

### Independent Hash Functions
