---
layout: post
title:  "Hotspot JVM's Fast Locking based on displaced mark word"
date:   2024-06-27 10:00:00 +0200
categories: jvm concurrency
---

# Introduction

This article describes the implementation of the fast locking algorithm that was introduced
by [Thin locks: featherweight synchronization for Java][thin-locks-paper] and is still
available (as of June 2024) under the *LM_LEGACY* locking mode. The approach is already very well described
in the aforementioned paper but this article aims to simplify the understanding of the approach
described in the paper by going step by step through what happens when a thread locks on
an object.

Note that the goal of the fast-locking algorithm is to minimize the overhead when a single thread tries
to acquire the lock for an object. This is known as a *non-contending* situation. When the thin locks approach
was introduced, reducing overhead in non-contending locks was particularly useful because some implementations 
of collections in the java standard library were written to be thread safe and to achieve that, they relied on 
synchronized methods. However, most of the time they were used in non-concurrent code, that still had to pay a big 
cost in terms of synchronization overhead.

# How does it work ?

## Acquire a lock

### TLDR

When a thread T1 tries to acquire the lock of an object, it attemps to set a value in the object header that identifies
our thread as the owner. This is done in an atomic operation usually known as compare-and-swap (CAS). If the CAS operation succeeds, 
our thread T1 has acquired the lock and it can continue running.


### Detailed explanation

Let's see a step by step process of a thread trying to acquire the lock for an object.

We can think of a java virtual machine running the following code:

{% highlight java linenos %}
public class MyClass {

    public static void main(String[] args) {
        getAccess();
    }

    public static void getAccess() {
        var a = new Object();
        synchronized(a) {
            // do something
        }

    }

}
{% endhighlight %}

First, let's look at two things that are relevant if we want to understand how our algorithm works:

- How Java objects are represented in the heap.
- How the virtual machine executes code.

### Object representation in memory

In the `getAccess` method, we first create a new object that needs to be allocated in the heap.

<img src="/assets/img/jvm-fast-locking-lock-record-based/object-a-allocated-in-heap.png" alt="">

In memory, the object is represented by the header and the content. The thin locking algorithm
relies on the object header to acquire and release the lock for an object.

<img src="/assets/img/jvm-fast-locking-lock-record-based/object-layout-in-heap.png" alt="">

### JVM Virtual machine Stack

The Java virtual machine is [stack based](https://en.wikipedia.org/wiki/Stack_machine). Every thread
will allocate a stack to keep track of the instructions to execute, local variables required and other
metadata. Every call to another method will create a new frame on top of the stack. Let's see
a simplified version of a thread dump representing the stack for the code that we introduced at the 
beginning of this post:


<img src="/assets/img/jvm-fast-locking-lock-record-based/thread-dump.png" alt="">

Now let's have a closer look at how the JVM handles the stack. We will now look at the stack
growing from the top to the bottom, because that's the way that it happens when the JVM
creates a new frame.


<img src="/assets/img/jvm-fast-locking-lock-record-based/thread-stack.png" style="height:400px;width:auto;display:block; margin-left:auto; margin-right:auto" alt="">

It is relevant to talk about the stack and its frames because the thin locking algorithm will use certain
parts of the stack to store locking information. 

## Step by step - Acquiring a lock

Now that we're aware of how objects are stored in memory and how the virtual machine executes instructions
we can describe how thin locking works.

Let's now see what happens when we reach a `synchronized` block:

1. The thread tries to acquire the lock.
2. The virtual machine, creates inside the stack frame a monitor entry, with a copy of the header (usually known
as displaced mark word) and a pointer to the object that the thread is trying to acquire the lock for. This is known as the lock record.

<img src="/assets/img/jvm-fast-locking-lock-record-based/monitor-entry-in-stack.png" alt="">

See [templateTable_aarch64.cpp](https://github.com/openjdk/jdk/blob/274c805c5137d9080a7670d864ecca8a0befc3f6/src/hotspot/cpu/aarch64/templateTable_aarch64.cpp#L3865)

Then, as described in [OpenJDK - Synchronization and Object Locking](https://wiki.openjdk.org/display/HotSpot/Synchronization):

{:start="3"}
3. The VM attempts to install a pointer to the lock record in the object's header word via a compare-and-swap operation.
See [templateTable_aarch64.cpp](https://github.com/openjdk/jdk/blob/274c805c5137d9080a7670d864ecca8a0befc3f6/src/hotspot/cpu/aarch64/sharedRuntime_aarch64.cpp#L1815)

4. If it succeeds, the current thread afterwards owns the lock. Since lock records are always aligned at word boundaries, the last two bits of the header word are then 00 and identify the object as being locked.

<img src="/assets/img/jvm-fast-locking-lock-record-based/thread-holds-lock.png" alt="">

<!--
- What's stored in the object header when the CAS succeeds ? A pointer to the monitor entry that 

- What if the CAS fails ?
    - If the lock is owned by the same thread but in a different frame, then the count is increased in the [fail case](https://github.com/openjdk/jdk/blob/274c805c5137d9080a7670d864ecca8a0befc3f6/src/hotspot/cpu/aarch64/sharedRuntime_aarch64.cpp#L1828) of the compare-and-swap instruction.
-->

## Drawbacks of this approach

- Inefficiency: bits are left unused, so that they can be used in case of synchronization based on the object, 
but we only ever run synchronized on a few out of all the existing objects. Today this implementation is only 
used under the LM_LEGACY locking mode, a more memory efficient locking mode LM_LIGHTWEIGHT is actually the default
approach. LM_LIGHTWEIGHT does a more efficient use of header bits by dropping the displaced markword and keeping
track of the locks for a thread in a lock stack.
- Additional complexity in the code to read the markword, that needs to handle two cases: if there's a lock it may
need to read the displaced mark word but if it there's no lock it can read it directly. (Note that this complexity
is multiplied by the fact that multiple architectures provide different implementations to read the mark word)

<!--
Unknown Questions ?

- Why do we need to keep the markword in the stack ? Who needs to check the displaced mark word ?
- Is the header bigger than the markword ?
- Where does the compressedKlassPointer live ?
-->


[thin-locks-paper]: https://dl.acm.org/doi/10.1145/277650.277734
