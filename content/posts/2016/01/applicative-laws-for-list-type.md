---
layout: post
author: "Umair Saeed"
title: "Applicative Laws for `[]` Type"
date: 2016-01-06
comments: false
categories: [haskell]
---

Haskell's list type `[]` is an Applicative functor. Similar to the previous post, this post will verify that the applicative laws hold for the `[]` type.

<!--more-->

    This is Part-2 of my series on verifying Applicative laws for various Haskell types. Part-1 is [Applicative Laws for Maybe Type][0].

For reference, `[]` is made an instance of the `Applicative` class as:

```haskell
instance Applicative [] where
    pure x      = [x]
    (<*>) fs xs = [f x | f <- fs, x <- xs]
```

For review, here are the applicative laws:

- Identity: `pure id <*> v = v`

- Homomorphism: `pure f <*> pure x = pure (f x)`

- Interchange: `u <*> pure y = pure ($y) <*> u`

- Composition: `pure (.) <*> u <*> v <*> w = u <*> (v <*> w)`

Now, let’s test the Applicative laws for the `[]` type one by one:

## Identity

We need to prove that the following holds for the `[]` type:

```haskell
pure id <*> v = v
```

Consider the *left* side of the equation:

```haskell
pure id <*> v

-- applying definition of pure for []
[id] <*> v

-- applying definition of <*>
[id x | x <- v]

-- applying id
[x | x <- v]

-- list comprehension reduces to v
v  -- Same as the right side
```


## Homomorphism

The law's statement is:

```haskell
pure f <*> pure x = pure (f x)
```

Starting from the *left* side:

```haskell
pure f <*> pure x

-- applying definition of pure
[f] <*> [x]

-- Note that both the lists have one element each. So
-- applying the definition of <*>, this reduces to:
[f x]

-- un-applying pure
pure (f x) -- Same as the right side
```


## Interchange

The law's statement is:

```haskell
u <*> pure y = pure ($y) <*> u
```

Starting from the *left* side,

```haskell
u <*> pure y

-- applying definition of pure
u <*> [y]   -- A list of functions applied to
            -- a single element list

-- via definition of <*>
[f y | f <- u]

-- replacing f y by the function application operator
[f $ y | f <- u]

-- Using the section representation of the $ infix operator
[($y) f | f <- u]

-- Un-applying the definition of <*>
[($y)] <*> u

-- Un-applying pure
pure ($y) <*> u -- Same as the right side
```



## Composition

The law's statement is:

```haskell
pure (.) <*> u <*> v <*> w = u <*> (v <*> w)
```

Starting from the *left*:

```haskell
pure (.) <*> u <*> v <*> w

-- applying definition of pure
[(.)] <*> u <*> v <*> w

-- as u, v, w are all lists, for clarity re-writing
-- u as fs, v as gs, w as xs
[(.)] <*> fs <*> gs <*> xs

-- applying definition of <*>
[(.) f | f <- fs] <*> gs <*> xs

-- applying definition of <*>
[z g | z <- [(.) f | f <- fs], g <- gs] <*> xs

-- replace the value of z, and reduce
[(.) f g | f <- fs, g <- gs] <*> xs

-- applying function composition
[f.g | f <- fs, g <- gs] <*> xs

-- applying the definition of <*>
[ z x | z <- [f.g | f <- fs, g <- gs],
        x <- xs]

[ f.g x | f <- fs, g <- gs, x <- xs]

-- applying definition of function composition
[ f (g x) | f <- fs, g <- gs, x <- xs] -- (A)
```

In the same manner, let's consider the *right* side of the equation:

```haskell
u <*> (v <*> w)

-- as u, v, w are all lists, for clarity re-writing
-- u as fs, v as gs, w as xs
fs <*> (gs <*> xs)

-- applying definition of <*>
fs <*> ( [g x | g <- gs, x <- xs] )

-- applying definition of <*>
[f z | f <- fs, z <- [g x | g <- gs, x <- xs] ]

-- replacing z
[f (g x) | f <- fs, g <- gs, x <- xs] -- (B)
```

(A) & (B) prove that both the left and right side of the composition law reduce to the same statement, and thus composition law is proved for the `[]` type


[0]: http://umairsaeed.com/blog/2016/01/05/applicative-laws-for-maybe-type/
