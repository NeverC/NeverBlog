title: 'The Introduction of Memory Leaks'
date: 2014-05-13 21:30:02
categories: 转载文章
tags: Java, JVM, Memory Leaks, Lifetime
---
One of the most significant advantages of Java is its memory management. You simply create objects and Java Garbage Collector takes care of allocating and freeing memory. However, the situation is not as simple as that, because memory leaks frequently occur in Java applications.

<!--more-->

## What are Memory Leaks?
Definition of Memory Leak: objects are no longer being used by the application, but Garbage Collector can not remove them because they are being referenced.To understand this definition, we need to understand objects status in memory. The following diagram illustrates what is unused and what is unreferenced.

![](http://never-blog.qiniudn.com/2014_05_2.jpg)

From the diagram, there are referenced objects and unreferenced objects. Unreferenced objects will be garbage collected, while referenced objects will not be garbage collected. Unreferenced objects are surely unused, because no other objects refer to it. However, unused objects are not all unreferenced. Some of them are being referenced! That’s where the memory leaks come from.
## Why Memory leaks happen?
Let’s take a look at the following example and see why memory leaks happen. In the example below, object A refers to object B. A’s lifetime (t1 – t4) is much longer than B’s (t2 – t3). When B is no longer being used in the application, A still holds a reference to it. In this way, Garbage Collector can not remove B from memory. This would possibly cause out of memory problem, because if A does the same thing for more objects, then there would be a lot of objects that are uncollected and consume memory space.
It is also possible that B hold a bunch of references of other objects. Those objects referenced by B will not get collected either. All those unused objects will consume precious memory space.

![](http://never-blog.qiniudn.com/2014_05_3.jpg)
## How to prevent Memory Leaks?
The following are some quick hands-on tips for preventing memory leaks.
1. Pay attention to Collection classes, such as HashMap, ArrayList, etc., as they are common places to find memory leaks. When they are declared static, their life time is the same as the life time of the application.
2. Pay attention to event listeners and callbacks. A memory leak may occur if a listener is registered but not unregistered when the class is not being used any longer.
3. “If a class manages its own memory, the programer should be alert for memory leaks.” Often times member variables of an object that point to other objects need to be null out.

Origin address：http://www.programcreek.com/2013/10/the-introduction-of-memory-leak-what-why-and-how/
