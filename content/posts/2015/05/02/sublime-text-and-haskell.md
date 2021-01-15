---
layout: post
author: "Umair Saeed"
title: "Sublime Text &amp; Haskell"
date: 2015-05-02 16:07:28 -0700
comments: false
lastmod: 2021-01-15
categories: [haskell, tools]
---

There are several excellent posts about setting up the Haskell development environment. One of the best ones is Tony Lawrence's [Configuring Your Haskell Environment][1]. I encourage you to take a look at his post first.

<!--more-->

Tony's post is more than a year old though, and it looks like a couple of things have changed since he wrote his post, especially with the latest version of Haskell and [SublimeHaskell][2]. I wasted a good chunk of time trying to get around those problems, so now writing this post in case others run into similar issues when setting up the SublimeHaskell plugin.


<!-- more -->

Here's a summary of the steps to setup the Haskell dev environment (from [Tony's blog][2]).

- Install the Haskell platform.
- Issue `cabal update` to update the list of available packages for Haskell.
- Install the requisite packages by issuing `cabal install aeson haskell-src-exts haddock hdevtools`.
- Install Sublime Text 3.
- Install Package Control for Sublime Text 3.
- Install SublimeHaskell via Package Control.

This last step doesn't work seamlessly. When I installed SublimeHaskell, I ran into the following error message in Sublime Text:

```console
SublimeHaskell: Failed to compile ModuleInspector

/Users/umair/Library/Application Support/Sublime Text 3/Packages/SublimeHaskell/ModuleInspector.hs:141:12:
    Not in scope: data constructor ‘H.PNeg’
    Perhaps you meant ‘H.PRec’ (imported from Language.Haskell.Exts)
```

The problem is described in more detail in [issue #182][3] on [SublimeHaskell's GitHub page][2]. The summary is that the master branch of SublimeHaskell is too slow to accomodate the removal of `PNeg` from `haskell-src-exts`. The solution is to use the `hsdev` branch of SublimeHaskell. This is done as follows (recommended by [MoreAxes][4] on comment thread for [issue #182][3]):


 > - In Sublime Text, launch Package Control (`cmd-shift-p`), and use the 'Add Repository' command to add `https://github.com/SublimeHaskell/SublimeHaskell/tree/hsdev`.
 > - Install SublimeHaskell with Package Control normally. Package Control looks in user-defined repositories first, so it is able to get SublimeHaskell from the `hsdev` branch.
 > - Restart Sublime Text.

You can now enter and build Haskell code from Sublime Text. To verify your setup, enter the following code in Sublime Text, and then hit `cmd-B`

```haskell
main :: IO()
main = do
    print "Hello World"
```

You will see the following printed:

```console
"Hello World"
[Finished in 0.2s]
```

Here's a screenshot of my test file, with the output of the program:

![Haskell in Sublime Text](/img/sublime-text-haskell-test.png)


[1]: http://tonylawrence.com/blog/2014/01/01/configuring-your-haskell-environment/ "Configuring Your Haskell Environment"

[2]: https://github.com/SublimeHaskell/SublimeHaskell "SublimeHaskell"

[3]: https://github.com/SublimeHaskell/SublimeHaskell/issues/182 "SublimeHaskell issue # 182"

[4]: https://github.com/MoreAxes "GitHub user MoreAxes"