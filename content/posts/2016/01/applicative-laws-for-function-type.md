---
layout: post
author: Umair Saeed
title: "Applicative Laws for `((->) r)` Type"
date: 2016-01-07
comments: false
categories: [haskell]
---

Haskell's function type `((->) r)` is an Applicative functor. Similar to the previous two posts in this series, in this post I will verify that the applicative laws hold for the `((->) r)` type.

<!--more-->

*This is Part-3 of my series on verifying Applicative laws for various Haskell types. Here are Part-1 [Applicative Laws for Maybe Type][0] and Part-2 [Applicative Laws for [] Type][1]*

For reference, `((->) r)` is made an instance of the `Applicative` class as:

```haskell
instance Applicative ((->) r) where
    pure x = (\_ -> x)
    -- pure can also be written as:
    -- pure x = const x

    -- (<*>) :: f (a->b) -> f a -> f b
    -- (<*>) :: ((->) r (a->b)) -> ((->) r a) -> ((->) r b)
    -- (<*>) :: (r -> (a->b)) -> (r -> a) -> (r -> b)
    -- the entire lambda has type r -> b, which implies x :: r
    (<*>) f g = (\x -> f x (g x))
```

For review, here are the applicative laws:

- Identity: `pure id <*> v = v`

- Homomorphism: `pure f <*> pure x = pure (f x)`

- Interchange: `u <*> pure y = pure ($y) <*> u`

- Composition: `pure (.) <*> u <*> v <*> w = u <*> (v <*> w)`

## Identity

We need to prove that the following holds for the `((->) r)` type:

```haskell
pure id <*> v = v
```

Beginning from LHS:

```haskell
pure (id) <*> v
-- using definition of pure
const id <*> v

-- applying definition of <*>
(\x -> const id x (v x))

-- applying const
(\x -> id (v x))

-- applying id
(\x -> v x)

v -- RHS
```


## Homomorphism

The law's statement is:

```haskell
pure f <*> pure x = pure (f x)
```

Here's the proof:

```haskell
pure f <*> pure x

-- applying definition of pure
const f <*> const x

-- applying definition of <*>
(\y -> const f y (const x y))

-- applying const
(\y -> f (x))

-- As y is unused, replace with _
(\_ -> f x)

-- un-applying definition of pure
pure (f x) -- RHS
```


## Interchange

The law's statement is:

```haskell
u <*> pure y = pure ($y) <*> u
```

Starting from the LHS:

```haskell
u <*> pure y

-- applying definition of pure
u <*> const y

-- applying definition of <*>
\x -> u x (const y x)

-- applying const
\x -> u x (y)

\x -> u x y
```

Now, starting from the RHS:

```haskell
pure ($y) <*> u

-- applying definition of pure
const ($y) <*> u

-- applying definition of <*>
\x -> const ($y) x (u x)

-- applying const
\x -> ($y) (u x)

-- applying definition of function application operator ($)
\x -> (u x) $ y

\x -> u x y
```

As both the left and right sides reduce to the same statement, the interchange law is proved for the `((->) r)` type


## Composition

The law's statement is:

```haskell
pure (.) <*> u <*> v <*> w = u <*> (v <*> w)
```

Starting from the *right* side of the equation:

```haskell
u <*> (v <*> w)

-- applying definition of <*>
u <*> ( \y -> v y (w y) )

-- applying definition of <*>
\x -> u x ( (\y -> v y (w y)) x )

-- Apply the lambda ( (\y -> v y (w y)) to its
-- argument x, which results in:
\x -> u x ( v x (w x)) -- (A)

```

In the same manner, let's consider the *left* side of the equation:

```haskell
pure (.) <*> u <*> v <*> w

-- applying definition of pure
const (.) <*> u <*> v <*> w

-- applying definition of <*> to the 1st two terms
(\f -> const (.) f (u f)) <*> v <*> w

-- applying const
(\f -> (.) (u f)) <*> v <*> w

-- applying definition of <*> to the 1st two terms
(\g -> (\f -> (.) (u f)) g (v g)) <*> w

-- applying definition of <*>
\x -> (\g -> (\f -> (.) (u f)) g (v g)) x (w x)

-- Expanding the lambda (\g -> ...) by applying to x
\x -> ((\f -> (.) (u f)) x (v x)) (w x)

-- Expanding the inner lambda (\f -> ...) by applying to x
\x -> (( (.) (u x)) (v x))  (w x)

-- Using definition of function composition (.)
\x -> ((u x) . (v x)) (w x)

-- Using definition of (.), i.e. f.g x = f (g x)
\x -> u x ( v x (w x) ) -- (B)

```

(A) & (B) prove that both the left and right side of the composition law for `((->) r)` type reduce to the same statement, and thus the law is proved for the function type.


## Acknowledgements

Thanks to [Daniel Wagner][2] who nudged me in the right direction by answering my questions ([1][3] and [2][4]) on Stack Overflow.


[0]: http://umairsaeed.com/blog/2016/01/05/applicative-laws-for-maybe-type/ "Applicative Laws for Maybe Type"

[1]: http://umairsaeed.com/blog/2016/01/06/applicative-laws-for-list-type/ "Applicative Laws for List Type"

[2]: http://stackoverflow.com/users/791604/daniel-wagner "Stack Overflow User - Daniel Wagner"

[3]: http://stackoverflow.com/questions/33829415/applicative-laws-for-the-r-type "Applicative Laws for the ((->) r) type"

[4]: http://stackoverflow.com/questions/34538754/proving-composition-applicative-law-for-r-type "Proving Composition Applicative law for ((->) r) type"

