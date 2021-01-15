---
layout: post
author: "Umair Saeed"
title: "Maybe as an Applicative Functor"
date: 2015-08-21 09:53:20 -0700
comments: false
categories: [haskell]
---


`Maybe` type is made an instance of the `Applicative` type class as follows:

```haskell
instance Applicative Maybe where
    pure = Just
    Nothing <*> _ = Nothing
    (Just f) <*> something = fmap f something
```

<!--more-->

It took me a several attempts before I could parse this definition. I was particularly puzzled by the line `(Just f) <*> something = fmap f something`. To help me understand this better, I decided to build this definition back up from the very basics.

First, let's look at the `Functor` type class definition:

```haskell
class Functor f where
    fmap :: (a -> b) -> f a -> f b
```

This means that for a given functor type `f` (i.e. type that is an instance of the `Functor` type class), `fmap` takes a function from `a -> b` and a functor (box) that contains `a` and returns a functor (box) that contains `b`. An intuitive way to think about this is that `fmap` opens the box containing `a` and applies the function `a -> b` to it, which results in `b`.

Now let's see how `Maybe` is an instance of the `Functor` type class:

```haskell
instance Functor Maybe where
    fmap func Nothing  = Nothing
    fmap func (Just x) = Just (func x)
```

- Line 2: Applying a function to `Nothing` results in `Nothing`.
- Line 3: From the definition of the `Functor` type, we know that the type of `func` is `a -> b` and `Just x` corresponds to `f a`. Applying the function to `x` inside the box results in a value of type `b` in the functor box.


Now, let's look at the definition of the `Applicative` type class:

```haskell
class (Functor f) => Applicative f where  -- 1
    pure :: a -> f a                      -- 2
    (<*>) :: f (a -> b) -> f a -> f b     -- 3
```

For a type `f` that is an instance of the `Applicative` type class, here is what each line means:

1. `f` must also be a functor (i.e. be an instance of the `Functor` type class).
2. The `pure` function takes an arbitrary type `a` and brings it into the functor. i.e. `pure` puts `a` in a box of type `f`.
3. `<*>` takes a functor (box) of type `f` that contains a function of type `a -> b`, and a functor (box) of type `f` that contains type `a`. It results in a functor (box) of type `f` that contains `b`.



With the preamble out of the way, let's make `Maybe`  an instance of the `Applicative` type class. To do that, I need to implement the `pure` and `<*>` methods for the `Maybe` type. Below is a line-by-line implementation:

```haskell
instance Applicative Maybe where
    pure x = Just x
```

For the `Maybe` type, `pure` simply wraps an arbitrary type in `Just`, thus making it a `Maybe` value. E.g. writing `pure 4 :: Maybe Int` in GHCi results in `Just 4`.


```haskell
    (<*>) Nothing _ = Nothing
```

Here, `Nothing` maps to `f (a -> b)` from the `Applicative` class definition. We cannot extract a function out of `Nothing`, so the result will be `Nothing` regardless of the second argument.

```haskell
    (<*>) (Just func) Nothing = Nothing
```

In the line above, `(Just func)` maps to `f (a -> b)`, and `Nothing` maps to `f a` from the class definition. `<*>` extracts `func` out of `Just func`, and applies it to `Nothing`. Applying a function to `Nothing` results in `Nothing` (or, using the box analogy, applying a function to an empty box results in an empty box)

```haskell
    (<*>) (Just func) (Just x) = Just (func x)
```

`(Just func)` maps to `f (a -> b)`, and `Just x` maps to `f a` from the `Applicative` class definition. `<*>` extracts the function from `Just func`, and applies it to `x` inside the `Just x` box. The result is `Just (func x)`.


Now, let's put the definition of `<*>` for `Maybe` type next to the definition of the `fmap` function:

```haskell
    (<*>) (Just func) Nothing  = Nothing
    (<*>) (Just func) (Just x) = Just (func x)

    fmap func Nothing  = Nothing
    fmap func (Just x) = Just (func x)
```

This makes it obvious that in the definition of `<*>`, once we extract `func` out of `Just func`, we simply map that function over the second argument of `<*>` (which will be of `Maybe` type as well). This means that the `<*>` implementation for `Maybe` can be re-written as:

```haskell
    <*> (Just func) something = fmap func something
```

This is exactly how the `<*>` function is implemented at the beginning of this blog post.

Finally, [Functors, Applicatives, And Monads In Pictures][1] by [Aditya Bhargava][2] is one of the best posts I've read on functors & applicatives. I highly recommend it.


[0]: http://learnyouahaskell.com/functors-applicative-functors-and-monoids#applicative-functors "Learn You a Haskell for Great Good, Applicative Functors"
[1]: http://adit.io/posts/2013-04-17-functors,_applicatives,_and_monads_in_pictures.html "Functors, Applicatives, And Monads in Pictures"
[2]: https://twitter.com/_egonschiele "Aditya Bhargava's Twitter Page"

