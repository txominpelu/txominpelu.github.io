---
layout: post
title:  "JVM - Virtual threads"
date:   2024-07-24 10:00:00 +0200
categories: jvm concurrency virtual threads
---

What are virtual threads ?

Correspond with the concept of green threads. they're lighter than OS threads, we can have many more of them.

(Why are they lighter than OS threads ?)


- [User-level threads....... with threads. - Paul Turner - Google](ttps://www.youtube.com/watch?app=desktop&v=KXuZi9aeGTw&desktop_uri=%2Fwatch%3Fv%3DKXuZi9aeGTw)

My understanding from the video:

- Most of the overhead of context-switching comes from the overhead of making a scheduling decision by a modern CPU scheduler.
- The other part comes because re-scheduling a thread requires entering kernel mode and that still has a cost.
- The solution proposed in the video is to propose and API where when blocking a thread we already tell the scheduler 
to which other thread we want to handle control. This would have two benefits: 1) If we know which thread is better suited to retake control (e.g producer / consumer thread)
then we will make a better decision than the scheduler. 2) We will make a faster decision than the scheduler (and we know because of 
the previous point that much of the overhead of context-switching comes from making scheduling decisions)

- User level threads avoid the cost of making scheduling decisions and the cost of entering kernel mode. 
- At the cost of 


Disadvantages of asynchronous code:

- More complex programming model
- Observability suffers with asynchronous tasks
- Harder to preserve data locality. Asynchronous calls improve occupation of the threads (is good to make use of all your CPUs) at the cost of making it difficult to control where
do we want our tasks to run so that they make better use of CPU caches for example.



How are they implemented inside the JVM

 - Continuation: runnable that can be stopped and resumed.
 - Scheduler: mount


