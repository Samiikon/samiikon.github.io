---
title: "Diving into Erlang pt. 1"
date: 2018-07-12
author: Sami
twitter: samiikon
description: "Some of my thoughts about Erlang."
---

So, Erlang. Why did I chose to try it? Well it kind of bumped into my radar via **CouchDB**. I've been using it for few months and it is for most parts programmed in Erlang. In CouchDB it is possible to do some efficient db queries with Erlang vs regular queries written in Javascript. Another reason to pick Erlang was that it is **functional language** and I haven't used any of those so far. Mostly I've used PHP, Python and Javascript.

_Disclaimer: Meaning of this blog post is to document and reinforce my learning of the basics of Erlang, not to be any kind of a tutorial. Although if my findings and thoughts happen to help people, that's just a huge plus._

## Journey through official tutorial

### Ready, set, erl!
As I started going through [the tutorial](http://erlang.org/doc/getting_started/seq_prog.html), first thing that clearly differentiated Erlang from any other programming language I've used so far was the syntax. It seems that first thing that any Erlang-file/Erlang-module contains, is the declaration of the modules name:

`-module(tutorial).`

This would be the first line in Erlang-file named _tutorial.erl_, as the name of the module has to be same as the filename. This line also shows noticeable difference from anything I've programmed before. All functions and statements end with `.` instead of widely used `;`.

Next thing I noticed is that Erlang is one of these languages where you have to declare functions in the beginning of the file:

`-export([double/1]).`

And this would mean that there is function named double and it takes on parameter. Also I found it to be quite refreshing that you don't need to declare datatypes explicitly. The actual code for this function is quite simple:

```erlang
double(X) ->
    2 * X.
```

So the function definition is not that different from what I've used before. However, two things peaked my interest. First of all, there is no keyword for function declaration(`def` in Python or `function` in PHP). Second thing I found interesting was that there is also no keyword for `return`. Instead, the last expression in the function is returned.

### A bit more complex function
The double function shown before is very simple as it just takes one argument and returns it doubled. Next function example is recursive, as it calls itself. The function calculates factorial for given number and it goes as follows:

```erlang
fac(1) ->
    1;
fac(N) ->
    N * fac(N - 1).
```

So here we see our first semicolon as _"wait, the function doesn't end here"_-operator. The function itself is declared in two parts for different argument values. For argument value 1, function would just return 1. For the rest of values(N > 1, no checking if argument value is viable), function goes into full self calling mode. Recursion loop ends when loop gets down to argument value of 1. Lets just write the loop open as pseudo code:

```erlang
fac(1) = 1
fac(2) = 2 * fac(1) = 2 * 1 = 2
fac(3) = 3 * fac(2) = 3 * 2 = 6
fac(4) = 4 * fac(3) = 4 * 6 = 24

so fac(4) could be written open as
4 * 3 * 2 * 1 = 24
```
That is how I understood inner workings of that specific recursion.

### The very basic data types
So far we have only tinkered with **numbers**, so it is time to introduce ourselves to **atoms**, **tuples** and **lists**. Atoms are just simple names, like `hello`, `phone_number` or `centimeter`. An atom should begin with lower-case letter and contain only alphanumeric characters, underscores or @. If other characters are needed, the atom should be enclosed in single quotes: `'Sami'`,`'phone number'`

```erlang
convert(M, inch) ->
    M / 2.54;
convert(N, centimeter) ->
    N * 2.54.
```
This example uses atoms to specify which part of the `convert` function to use. To convert centimeters to inches, we would call the function like this: `convert(10, inch).`

Usage of the convert function wasn't very intuitive, so it is time to get familiar with tuples. **Tuple** is a datatype with fixed number of elements in it, quite like tuples in Python. There is one main difference between Erlang and Python tuples. Erlang tuple has only fixed number of elements, but the content of the elements can be changed whereas in Python the content in tuple elements cannot be changed. So previous example with the help of tuples looks like this:

```erlang
convert_length({centimeter, X}) ->
    {inch, X / 2.54};
convert_length({inch, Y}) ->
    {centimeter, Y * 2.54}.
```

So here the function gets a tuple as an argument and returns a tuple. These tuples contain the unit of measurement as an atom and numeric value.

And then it is time for lists. This wonderful data type can be found in most programming languages on some form. Erlang lists are declared as follows:  `[element1, element2]`

**Lists** in Erlang have this interesting _"head and tail"_-syntax to access the contents of a list. It is easier to show than tell, so here goes:

```erlang
% Declare First and TheRest
> [First | TheRest] = [1,2,3,4,5].
  [1,2,3,4,5]
> First.
  1
> TheRest.
  [2,3,4,5]
```

So `|` is used to divide which elements from the start of the list you would like to access, and which are left to the tail part.


## Journey so far
Here would be a nice spot to wrap up this first part of my Erlang journey. So far Erlang has been interesting and refreshing. It feels a bit like mathematics when compared to object-oriented languages. 'Sequential programming'-chapter of Erlangs getting started guide is so long that it has enough material for at least one more blog post.
