---
comments: false
date: 2012-07-18 13:46:06+00:00
layout: post
title: "A Quine in Objective-C"
author: "Umair Saeed"
categories: [puzzles]
---

A quine is a program that takes no input and outputs its own source code. It has been a while since I last wrote a quine, so I figured I'll write one in Objective-C. In general, quines follow a fairly simple formula. The program contains a string that includes all the code before the string and all the code after the string. Depending on the programming language, the string might also contain format string (for languages that use format-strings to print to `stdout`)

<!--more-->

Here is the code for the quine. I've broken the code below into segments to make it simpler to understand:

```objc
#import <Foundation/Foundation.h>
int main (int argc, const char * argv[]) {
  @autoreleasepool {
    NSString *str=@" \
      #import <Foundation/Foundation.h> %c \
      int main (int argc, const char * argv[]) { @autoreleasepool { NSString *str= \
      %c%c%@%c; \
      NSLog(str, 10, 64, 34, str, 34);} return 0;}";
    NSLog(str, 10, 64, 34, str, 34);
  }
  return 0;
}
```


Before I go on, 10, 64 and 34 are the ASCII character codes for `newline`, `@` and `"`respectively. To make it clearer, I've broken the string into four lines. The first two lines contain the source code before the string. The 3rd line is the format string and the last line is the source code after the string. Here's the code with all lines collapsed:


```objc
#import <Foundation/Foundation.h>
int main (int argc, const char * argv[]) {
  @autoreleasepool {
    NSString *str=@"#import <Foundation/Foundation.h> %c int main (int argc, const char * argv[]) { @autoreleasepool { NSString *str=%c%c%@%c; NSLog(str, 10, 64, 34, str, 34);} return 0;}";
    NSLog(str, 10, 64, 34, str, 34);
  }
  return 0;
}
```


Finally, here's the output when you execute this program. As you can tell, this is the same as the original program (minus the whitespace)

```objc
#import <Foundation/Foundation.h>
 int main (int argc, const char * argv[]) { @autoreleasepool { NSString *str=@"#import <Foundation/Foundation.h> %c int main (int argc, const char * argv[]) { @autoreleasepool { NSString *str=%c%c%@%c; NSLog(str, 10, 64, 34, str, 34);} return 0;}"; NSLog(str, 10, 64, 34, str, 34);} return 0;}
```

