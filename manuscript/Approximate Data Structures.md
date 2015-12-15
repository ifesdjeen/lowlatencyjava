# Approximate Data Structures

Approximate (also called Probabilistic) Data Structures are data structures that allow optimizing
space for the precision of the answer. Usually they use some sort of `hash function` to represent
the set in the most compact manner. In comparison with the deterministic data structures,
they consume less memory and can be queried in a constant time.

Here we will talk mostly about the algorithms that are used heavily in stream processing, such
as `Bloom Filters`, used for set membership queries, `HyperLogLog` used for the cardinality
estimation, `Streaming Histogram` that represents distribution of data over the seen sequence.
Also, `Skip List` used for the fast fast searches within an ordered sequence of elements.

## Bloom Filters

## Count Min Sketch

[Count-min sketch](http://dimacs.rutgers.edu/~graham/pubs/papers/cm-full.pdf) is a __sublinear space__
data structure for summarizing data streams.

Count min sketch helps you to understand how many times certain item has occured in your stream.
It's like a database which you can __only retrieve a count from__, without being able to __retrieve
a precise value__. So you can ask it "how many times have I seen Alex?" but you can never ask it
"what items have you seen at all?".

### Algorithm

The algorithm itself is quite straightforward:

  * You create a table, filled with zeroes, of a `width` {$$}w{$$} and `height` {$$}d{$$}
  * $d$ is calculated from {$$}d = ⌈ln 1/δ ⌉{$$}
  * $w$ is calculated from {$$}w = ⌈e/ε⌉{$$}
  * You take {$$}d{$$} linear hash functions

### Update

  * You for rows with indexes from 0 to {$$}d-1{$$} (zero-based index), you calculate a hash of given value
  * Having a current update row denoted as {$$}i{$$}, and {$$}i{$$} hash-function denoted as {$$}h{$$}, and current value
    in your table denoted as {$$}old{$$}
  * You update a table at position {$$}[i, h(value)]{$$} to {$$}old + 1{$$}

## Lookup

  * You for rows with indexes from 0 to {$$}d-1{$$} (zero-based index), you calculate a hash of given value
  * Having a current update row denoted as {$$}i{$$}, and {$$}i{$$} hash-function denoted as {$$}h{$$}
  * You find a maximum of values at positions {$$}[i, h(value)]{$$}
  * Profit!


## Hyperloglog

## Skip List

# Bonus

## Streaming Histograms

##
