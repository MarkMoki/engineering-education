---
layout: engineering-education
status: publish
published: true
url: /semaphore-in-java/
title: Semaphore in Java
description: This article wil cover Semaphors in Java.
author: mark-moki
date: 2021-12-T00:00:00-17:10
topics: [Languages]
excerpt_separator: <!--more-->
images:

  - url: /engineering-education/semaphore-in-java/hero.jpg
    alt: Semaphore in Java
---
Semaphores in java are the subject of this article. In java, a semaphore uses a counter to manage access to a shared resource. It is a thread synchronization construct that is used to communicate between threads in order to avoid missing signals or to protect a critical section.
### Prerequisite
1. Before beginning this tutorial, you should be familiar with the Java programming language.
2. For server-side Java development, have (**IntelliJ IDEA**)[https://www.jetbrains.com/idea/] as the IDE.
### Table of contents
- [Uses of semaphores](#uses-of-semaphores)
- [Semaphore operation](#semaphore-operation)
- [Semaphore class](#semaphore-class)
- [Semaphore implementation ](#semaphore-implementation)
- [How the application works ](#how-the-application-works)
- [Semaphore types](#semaphore-types)
- [Using semaphores as locks](#using-semaphores-as-locks)
- [Conclusion](#conclusion)
### Uses of semaphores
- When multiple processes are running at the same time, semaphore variables can be used to keep them in sync.
- We can avoid race condition circumstances by using semaphores. 
- Semaphores ensure that any missed signals are not avoided. 
- Semaphores are used to track who has access to a common resource. To gain access, the user needs to have a counter that is greater than 0. Access can also be denied in this situation. There's a counter in the shared resource that keeps track of who has access to it. For this circumstance, a thread's access to the data is required by the semaphore.
### Semaphore operation
1. A semaphore is in charge of restricting access to a resource, as we may infer from the usage of counters. 
2. It is possible that the thread will gain access to the resource if its counter increases over zero and is denied access to the resource if the counter is zero.
3. When a thread finishes its work in the resources or no longer needs the resource, the counter is incremented. The permit is then released.
> Instead of creating your own semaphore classes, use the `Java.util.concurrent` library. There is no need to implement the semaphore class manually because it provides all of the functionality.
### Semaphore class 
The semaphore class has two constructors:
```Java
Semaphore(int number)
Semaphore(int number, boolean how)
```
__Number__ tells how many permits can be given out at the start. If you want to use a shared resource, you need to have enough threads to use it. When a resource is shared by multiple threads, only one can use it at a time. As is the default, all waiting threads are given permission in an unknown order, with the first thread getting permission first. How  can be used to limit how access is given to threads that are waiting for their turn.
#### Semaphore-class methods:
Semaphore class has the following methods:
- The `acquire()` method: Obtains the permit and waits for one to become available before returning true or false.
- The `release()` method: A permit can be released using this method.
- The `Permits()` method: Returns the current number of permits that are currently available.

### Semaphore implementation 
Let us see how we can implement semaphores in the following code below:
```Java
import java.util.concurrent.* ;

class SharedR {
  static int count = 0;
}

class MyThread extends Thread {
  Semaphore mysemaphore;
  String threadName;
  public MyThread(Semaphore mysemaphore, String threadName) {
    super(threadName);
    this.mysemaphore = mysemaphore;
    this.threadName = threadName;
  }@Override
  public void run() {
    if (this.getName().equals("Thread1")) {
      System.out.println("Starting " + threadName);
      try {
        System.out.println(threadName + " : waiting for a permit.");

        //Acquiring the lock 
        mysemaphore.acquire();

        System.out.println(threadName + " : getting a permit.");

        // Accessing the shared resource. 
        for (int i = 0; i < 5; i++) {
          SharedR.count++;
          System.out.println(threadName + ": " + SharedR.count);
          Thread.sleep(10);
        }
      }
      catch(InterruptedException exc) {
        System.out.println(exc);
      }

      // Releasing the permit. 
      System.out.println(threadName + ": releasing the permit.");
      mysemaphore.release();
    }
    else {
      System.out.println("Starting :" + threadName);
      try {
        System.out.println(threadName + ": waiting for a permit.");

        // acquiring the lock 
        mysemaphore.acquire();

        System.out.println(threadName + " gets a permit.");

        // Now, accessing the shared resource. 
        for (int i = 0; i < 5; i++) {
          SharedR.count--;
          System.out.println(threadName + ": " + SharedR.count);

          Thread.sleep(10);
        }
      }
      catch(InterruptedException exc) {
        System.out.println(exc);
      }
      // Releasing the permit. 
      System.out.println(threadName + ": releasing the permit.");
      mysemaphore.release();
    }
  }
}
public class SemaphoreDemo {
  public static void main(String args[]) throws InterruptedException {
    
    Semaphore mysemaphore = new Semaphore(1);

    // Creating two threads with name b1 and b2 
    MyThread b1 = new MyThread(mysemaphore, "Thread1");
    MyThread b2 = new MyThread(mysemaphore, "Thread2");

    b1.start();
    b2.start();
    b1.join();
    b2.join();

    //count will always be 0 after both threads complete their execution 
    System.out.println("count: " + SharedR.count);
  }
}
```
Output:
```bash
Starting :Thread2
Starting Thread1
Thread1 : waiting for a permit.
Thread2: waiting for a permit.
Thread1 : getting a permit.
Thread1: 1
Thread1: 2
Thread1: 3
Thread1: 4
Thread1: 5
Thread1: releasing the permit.
Thread2 gets a permit.
Thread2: 4
Thread2: 3
Thread2: 2
Thread2: 1
Thread2: 0
Thread2: releasing the permit.
count: 0
```
> Counter variable's final value will always be 0 in this program.
### How the application works 
When accessing the count variable in the shared class, a semaphore is utilized to keep track of how many threads are looking at it.

Thread `b1` increments and decrements the __SharedR.count__ five times each. As a safeguard, access to __SharedR.count__ can only be granted once a semaphore has granted permission for it. Upon completion, the permission is released. One thread at a time attempts to access the __SharedR.count__, as can be seen in the output.

In addition to their `start()` methods, thread classes provide a built-in `Sleep()` function that can be used from within them. Semaphore, which synchronizes access to __SharedR.count__, is used to demonstrate this. The `sleep()` method is invoked in the `start()` method between each visit to the counter. This means that the second thread can proceed. No other threads can access the resource until the first thread releases the semaphore. A total of five times, threads `b1` and `b2` each increment and decrement the __Shared.count__, respectively. Assembly code does not mix up the increments and decrements.

The semaphore is used to prevent both threads from concurrently accessing __Shared.count__. You can test this by omitting the calls to `acquire()` and `release()` in your script. There is no longer synchronized access to the common counter, therefore you will not always get a starting count of 0.
### Semaphore types
#### 1. Counting Semaphores

Counting semaphores are in handy when multiple processes are vying for control of a single vital section at the same time.
It is the `take()` method that is used to implement Semaphore.
#### 2. Bounded Semaphores

The maximum number of signals that can be stored in counting semaphores is unbounded. As a result, the upper bound is set using bounded semaphores.
#### 3. Timed Semaphores

Timers are semaphores that can be programmed to run at specific intervals for predetermined lengths of time. If you wait until the timer goes out for this long, all of your permissions will be released.
#### 4. Binary Semaphores

Unlike counting semaphores, binary semaphores can only take one of two possible values: 0 or 1. The binary semaphore is easier to implement than the counting semaphore. For the signal action to succeed, the value must be at least 1. Otherwise, it fails.
### Using semaphores as locks
A bounded semaphore can likewise be used as a lock, as demonstrated here. `Take()` and `release()` methods should be called in this case, as the higher limit must be set to 1.
Let's see an example below:
```Java
BoundedSemaphore ourBoundedSemaphore = new BoundedSemaphore(1);

ourBoundedSemaphore.take();
try {
}
finally {
  ourBoundedSemaphore.release();
}
```
### Conclusion
A counter on a semaphore restricts access to a shared resource. If the counter is greater than zero, the user is permitted access. Access is denied if the number is zero. There is a tally on the counter that shows how many permits have been given out for the shared resource. As a result, a thread must have a semaphore permit in order to access the resource.

Our study of Java Semaphores comes to an end here. With the examples, we learned about the fundamentals of Semaphores, including how they work and the many types of Semaphores.

I hope that you have now grasped the concept of semaphores and how they work in practice.