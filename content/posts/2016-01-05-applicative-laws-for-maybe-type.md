---
layout: post
author: "Umair Saeed"
title: "Applicative Laws for `Maybe` Type"
date: 2016-01-05 07:32:58 -0800
comments: false
categories: [haskell]
---


Applicative functors come with a set of laws that apply for all Applicative instances. These laws are as follows:

- Identity: `pure id <*> v = v`

- Homomorphism: `pure f <*> pure x = pure (f x)`

- Interchange: `u <*> pure y = pure ($y) <*> u`

- Composition: `pure (.) <*> u <*> v <*> w = u <*> (v <*> w)`

<!--more-->

For more information about these laws, check out [this Haskell wiki post][0].

`Maybe` is an Applicative functor, and this post will verify that the applicative laws hold for the `Maybe` instance.

Before we begin, let's review the [definition][1] for `Applicative` type-class:

```haskell
class Functor f => Applicative f where
    pure :: a -> f a
    (<*>) :: f (a->b) -> f a -> f b
```


`Maybe` is made an instance of the `Applicative` class as:

```haskell
instance Applicative Maybe where
    pure x                   = Just x
    (<*>) Nothing  _         = Nothing
    (<*>) (Just f) someMaybe = fmap f someMaybe
```

Note that the last line above [can be re-written as][2]:

```haskell
    (<*>) (Just f) Nothing  = Nothing
    (<*>) (Just f) (Just x) = Just (f x)
```

Now, let's test the Applicative laws for the `Maybe` type one by one:


## Identity

We need to prove that the following holds for the `Maybe` type:

```haskell
pure id <*> v = v
```

Let's start from the *left* side of the equation:

```haskell
pure id <*> v

-- applying definition of pure for Maybe
(Just id) <*> v
-- v could be either Nothing, or (Just x)
case v of
    Nothing  -> Nothing
    (Just x) -> Just (id x)
-- applying id to x is simply x
case v of
    Nothing  -> Nothing
    (Just x) -> Just x

v  -- the right side of equation
```


## Homomorphism

The law's statement is:

```haskell
pure f <*> pure x = pure (f x)
```

To prove this for `Maybe` type, let's begin from the *left* side of the equation:

```haskell
pure f <*> pure x

-- applying definition of pure
(Just f) <*> (Just x)
-- applying defintion of <*>
Just (f x)
-- un-applying the definition of pure
pure (f x) -- the right side
```


## Interchange

The law's statement is:

```haskell
u <*> pure y = pure ($y) <*> u
```

Starting from the *left* side:

```haskell
u <*> pure y

-- applying definition of pure
u <*> (Just y)

-- u can be either Nothing, or (Just f). Let's evaluate both
case u of
    Nothing  -> Nothing
    (Just f) -> Just (f y)

-- re-writing (f y) using function application operator
case u of
    Nothing  -> Nothing
    (Just f) -> Just (f $ y)

-- Using the section representation of the $ infix operator
case u of
    Nothing  -> Nothing
    (Just f) -> Just (($y) f)

-- Un-applying the definition of <*>
case u of
    Nothing  -> Nothing
    (Just f) -> Just ($y) <*> (Just f)

-- Replacing the Nothing clause with an equivalent (via
-- the defintion) of <*> for Maybe
case u of
    Nothing  -> Just ($y) <*> Nothing
    (Just f) -> Just ($y) <*> (Just f)

-- Un-applying the definition of pure and u
pure ($y) <*> u -- Equal to the right side
```



## Composition

The law's statement is:

```haskell
pure (.) <*> u <*> v <*> w = u <*> (v <*> w)
```

Starting from the *left* side:

```haskell
pure (.) <*> u <*> v <*> w

-- applying definition of pure
(Just (.)) <*> u <*> v <*> w

-- u, v & w can all be either Nothing or (Just something).
-- From the definition of <*>, we know that if either
-- argument to <*> is Nothing, the entire expression reduces
-- to Nothing.
-- Therefore, I am only going to focus on the Just cases
(Just (.)) <*> (Just f) <*> (Just g) <*> (Just x)

-- Appying <*> to the first two terms
(Just (.) f) <*> (Just g) <*> (Just x)

-- Appying <*> to the first two terms again
(Just (.) f g) <*> (Just x)

-- Applying the function composition
(Just (f.g) ) <*> (Just x)

-- Applying <*> one more time
(Just (f.g) x)

-- via the definition of the function composition
-- operator
(Just f (g x))

-- Applying Homomorphism law
(Just f) <*> Just (g x)

-- Applying Homomorphism law to the 2nd term
(Just f) <*> ( (Just g) <*> (Just x) )

-- Un-applying the definitions of u, v, w
u <*> (v <*> w)    -- Equal to the right side
```



[0]: https://wiki.haskell.org/Typeclassopedia#Laws_2 "Applicative Laws"
[1]: https://hackage.haskell.org/package/base-4.8.1.0/docs/Control-Applicative.html "Control.Applicative"
[2]: http://umairsaeed.com/blog/2015/08/21/maybe-as-an-applicative-functor/ "Maybe as an Applicative Functor"
