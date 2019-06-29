---
title: "Manual Memory Management in Objective-C"
author: "Umair Saeed"
comments: false
date: 2011-01-24 19:10:40+00:00
layout: post
categories: [iOS]
---

Objective-C on iOS has no garbage collector, so it is up to the programmer to make sure that memory is properly freed once an object is no longer needed. On the other hand, Objective-C on the Mac does have a garbage collector (in Objective C 2.0). This blog post focuses on how to manage memory in the absence of a garbage collector.

<!--more-->

When managing memory manually, two major issues to watch out for are premature deallocation and memory leaks.

Cocoa Touch framework uses manual [reference counting](http://en.wikipedia.org/wiki/Reference_counting) to manage memory on the iOS devices. Reference counting works on the principle that once created, every object has an owner. During its existence, its owner may change and it may even have more than one owners. When the number of owners for an object drops to zero, it deallocates itself to free up the memory being used.

## `retain` & `release`

Owners are tracked via retain counts. When an object is created it always has a retain count of 1. To own an object its retain count is incremented via the `retain` message. On the other hand, when the object is no longer needed its ownership is relinquished by decrementing the retain count via the `release` message. When the count reaches zero, the object sends itself the `dealloc` message and returns all the memory back to the heap.

## `autorelease`

`autorelease` marks an object for future release (delayed release). When an object is sent the autorelease message, it is added to an instance of `NSAutoreleasePool`. The Autorelease pool keeps track of all the objects that have been sent the `autorelease` message. This pool is drained periodically, at which time all the objects within it are sent the release message.

`autorelease` is really handy when the creator of an object (e.g. a factory) simply creates the object and returns it to the caller. At this point, the creator has nothing to do with the object anymore, so it is up to the caller to retain the returned object in order to continue using it.


## An Example - A Ticket class

Let us work through an example to see manual memory management in action. Suppose I am writing a ticketing framework, and I have a Ticket entity. The header file for Ticket looks as:


```objc
@interface Ticket : NSObject {
  NSString *ticketId;
}

- (NSString *) ticketId;
- (void) setTicketId:(NSString *) tid;

- (id) initWithId:(NSString *) tid;
+ (id) ticketWithId:(NSString *) tid;

@end
```


And the implementation file for Ticket looks as follows:

```objc
#import "Ticket.h"
@implementation Ticket

- (void) dealloc
{
  [ticketId release];
  [super dealloc];
}

- (NSString *) ticketId
{
  return ticketId;
}

- (void) setTicketId:(NSString *)tid
{
  [tid retain];
  [ticketId release];
  ticketId = tid;
}

- (id) initWithId:(NSString *) tid
{
  if (self = [super init])
  {
    [self setTicketId:tid];
  }

  return self;
}

+ (id) ticketWithId:(NSString *)tid
{
  Ticket *newTkt = [[Ticket alloc] initWithId:tid];
  return [newTkt autorelease];
}

@end
```

There are three memory management points to note here:

1. Inside `dealloc`, an object must release all its instance variables first. Then it should go up its class hierarchy and release any instance variables of its superclass. We should never directly send the `dealloc` message to instance variables, as some other objects might still have references to those variables.

2. A setter must retain the value passed in before it releases the old value, as `tid` and `ticketId` could be pointers to the same object.

3. The `ticketWithId:` method creates a ticket and simply returns it to the caller. It has no use for the `newTkt`, but it owns `newTkt` by virtue of creating it. At this point, if `newTkt` were released before method exit, then the caller would get a pointer to unallocated heap. To avoid this, we put the `newTkt` on the autorelease pool. Periodically, the autorelease pool is drained and all the objects it it are sent the `release` message thus decrementing the retain count.

Essentially, the `ticketWithId:` method is saying that it does not want to be the owner for `newTkt` and puts that responsibility on the caller. If the caller wants to hold on to `newTkt` once it is returned, it must send it the `retain` message.


#### Using the Ticket class

```objc
// EXAMPLE-1
- (void) processTicketWithId:(NSString *)ticketId
{
  Ticket* tkt = [[Ticket alloc] initWithId:ticketId];
  // Do something with tkt
  // ...
  [tkt release];
}
```

At this point, the retain count for `tkt` is 1. Moreover, since the `processTicketWithId:` method created the `tkt` object, it is now the owner and thus is responsible for cleaning it up before this method exits. Clean up is done by sending it the `release` message
Let's see another example:

```objc
// EXAMPLE-2
- (void) processTicketWithId:(NSString *)ticketId
{
  Ticket* tkt = [Ticket ticketWithId:ticketId];
  [tkt retain];
  // Do something with tkt
  // ..
  [tkt release];
}
```

In this example, the memory for `tkt` wasn't allocated by `processTicketWithId` method, so it doesn't own the `tkt` object. However, as we've seen in the implementation of the Ticket class, the `ticketWithId:` method created the Ticket object and added it to the autorelease pool. In order to continue using `tkt`, we must retain it so that even if it is drained from the autorelease pool, we can still continue to use the `tkt` object. Once done, we need to clean up and send the release message.


## Summary of Memory Management Rules for Objective-C

- Rule-1: If you get/create the object from `new`, `alloc` or `copy`, you must `release` it when done.

- Rule-2: If you get the object any other way, assume that it has been autoreleased. If you want to hold on to this object, send it the `retain` message.

- Rule-3: If you `retain` an object, you must balance every `retain` with a `release`.

- Rule-4: Never send the `dealloc` message to an object directly. Others might be holding references to this object, and if deallocated, they'll be left with pointers to unallocated memory.
