---
layout: post
author: "Umair Saeed"
title: "Index Based List Operations Using folds in Haskell"
date: 2015-05-16 08:53:31 -0700
comments: false
categories: [haskell]
---

When working with lists in Haskell, occasionally there's a need to perform index based operations, such as adding an element at a particular index. As a Haskell *newbie*, using `foldl` or `foldr` is not the first idea that comes to mind when indices are involved. However, there is a general pattern that can be applied when using folds for index-based list operations.

<!--more-->

For example, consider the case where we need to add an element at a particular index:

```haskell
-- inserting at an index, using foldr
insertAt :: a -> [a] -> Int -> [a]
insertAt x ys n = foldr insertHelper [] $ zip [0..] ys
    where
        insertHelper (i,y) acc = if i == n
            then x : y : acc
            else y : acc

```


The basic idea is simple: We simply zip `[0..]` with the input list (`ys`) to get a list of tuples. The first element of each tuple is the index of the element in the list `ys` (input list). This allows us to perform index based tests in the helper function, as shown in `insertHelper` above.

Both left and right folds can be used for index based list operations. Here's the same example using `foldl`.

```haskell
-- inserting at an index, using foldl
insertAtL :: a -> [a] -> Int -> [a]
insertAtL x ys n = foldl insertLHelper [] $ zip [0..] ys
    where
        insertLHelper acc (i,y) = if i == n
            then acc ++ [x] ++ [y]
            else acc ++ [y]

```


Both left and right fold produce the same result. Adding an element to the head of the list (via `:`) is more efficient than concatenating lists (via `++`), so generally I prefer using the 1st version, i.e. one that uses `foldr`


[Here's a gist][gist-list] with examples of  `insertAt`, `updateAt`, and `deleteAt` functions, all implemented using folds.


[gist-list]: https://gist.github.com/umairsd/cdcb397941762fe02d05