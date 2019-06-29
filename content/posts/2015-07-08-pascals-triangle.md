---
layout: post
author: "Umair Saeed"
title: "Pascal's Triangle"
date: 2015-07-08 06:51:27 -0700
comments: false
categories: [haskell]
---

One of the exercises in [Structure and Implementation of Computer Programs][sicp1] deals with generating elements of the [Pascal's Triangle][pascal2].

<!--more-->

The following pattern of numbers is called *Pascal's Triangle*


```console
    1
   1 1
  1 2 1
 1 3 3 1
1 4 6 4 1
...

```

The numbers at the edge of the triangle are 1, and each number inside is the sum of two numbers above it. The exercise asks us to *find the elements of Pascal's triangle by means of a recursive process*.

I find it helpful to *de-skew* the triangle so that the rows and columns line up visually. Here's the modified triangle:

```console
1
1 1
1 2 1
1 3 3 1
1 4 6 4 1
...
```


I decided to write the solution in Haskell, and here it is:


```haskell
-- A: Function that finds the element of Pascal's Triangle for a given row & column
pascal :: Int -> Int -> Int
pascal 0 _ = 1
pascal _ 0 = 1
pascal row col
    | row == col = 1
    | col > row  = error "Column can't be greater than row"
    | otherwise  = (pascal (row-1) (col-1)) + (pascal (row-1) col)

```


This code is great for generating the element at a given row and column (e.g. `pascal 3 2` produces `3`), but not so great if we wanted to generate the entire triangle up to `n` rows, as recursion would cause the code to generate the elements at row `n-1` multiple times.

In order to generate the entire triangle, it is better to do it via an iterative process as follows: generate the first row, and from that generate the second row, and so on. I other words, we are generating the next row of the triangle, by adding each pair in the current row. My solution is as follows:


```haskell
-- Takes an array of integers, and adds pairs of numbers to create a new
-- array. For example, [1,2,3,4] changes to [(1+2), (2+3), (3+4)], resulting
-- in [3,5,7]
sumOfPairs :: [Integer] -> [Integer]
sumOfPairs [] = []
sumOfPairs (x:[]) = []
sumOfPairs (x:y:ys) = (x+y) : sumOfPairs (y:ys)


-- Given a row of Pascal's triangle, returns the next row
next :: [Integer] -> [Integer]
next xs = 1 :  (sumOfPairs xs) ++ [1]


-- Generates the Pascal's triangle. This function generates an
-- infinite triangle, so its usage needs to be coupled with the
-- take command. For example: take 5 pascalTriangle
pascalTriangle :: [[Integer]]
pascalTriangle = pascalHelper [1]
    where pascalHelper xs = xs : pascalHelper (next xs)

```

Here are a couple of examples of generating the triangle:

```haskell
*Main> take 4 pascalTriangle
[[1],[1,1],[1,2,1],[1,3,3,1]]

-- Using mapM_ with print to 'pretty-print' the triangle
*Main> mapM_ print $ take 8 pascalTriangle
[1]
[1,1]
[1,2,1]
[1,3,3,1]
[1,4,6,4,1]
[1,5,10,10,5,1]
[1,6,15,20,15,6,1]
[1,7,21,35,35,21,7,1]
```


The function `pascalTriangle` can be made more concise by using the built-in `iterate` instead of my `pascalHelper`:


```haskell
pascalTriangle :: [[Integer]]
pascalTriangle = iterate next [1]
```


By this point, I was reasonably satisfied with the code. However, being relatively new to Haskell, I decided to explore and see if there were other (and possibly better and more idiomatic) ways to solve this problem. My favorite was by [Neil Mitchell][neilmitchell], who solved the problem in two lines! I encourage you to go read [Neil's entire post][neilmitchell]. Here's the two-line version of Haskell's triangle:


```haskell
-- By: Neil Mitchell
-- (http://neilmitchell.blogspot.com/2012/01/pascals-triangle-in-haskell.html)

next xs = zipWith (+) ([0] ++ xs) (xs ++ [0])
pascal = iterate next [1]
```


In my view, this code is very elegant and beautiful and does a great job of highlighting the power of Haskell.



[sicp1]: https://mitpress.mit.edu/sicp/
[pascal2]: https://en.wikipedia.org/wiki/Pascal's_triangle
[neilmitchell]: http://neilmitchell.blogspot.com/2012/01/pascals-triangle-in-haskell.html

