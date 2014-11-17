---
layout: post
title: RT-Thread源码分析：rt_mutex里owner的作用
category: opinion
description: 
---

在rt-thread源码中的rt_mutex_take()里有这样一段代码

    if (mutex->owner == thread)  
    {  
        /* it's the same thread */  
        mutex->hold ++;  
    } 

 之前不理解在什么情况下线程会重复进入其所持有锁的保护区域，后来看到Vxworks Programmers Guide5.5里的一段解释和对应的例子
 
Mutual-exclusion semaphores can be taken recursively. This means that the semaphore can be taken more than once by the task that holds it before finally being released. Recursion is useful for a set of routines that must call each other but that also require mutually exclusive access to a resource.

    /* Function A requires access to a resource which it acquires by taking  
     * mySem;   
     * Function A may also need to call function B, which also requires mySem:  
     */   
       
    /* includes */   
    #include "vxWorks.h"   
    #include "semLib.h"   
    SEM_ID mySem;   
       
    /* Create a mutual-exclusion semaphore. */   
    init ()   
        {   
        mySem = semMCreate (SEM_Q_PRIORITY);   
        }   
    funcA ()   
        {   
        semTake (mySem, WAIT_FOREVER);   
        printf ("funcA: Got mutual-exclusion semaphore\n");   
        ...    
        funcB ();   
        ...   
         semGive (mySem);   
        printf ("funcA: Released mutual-exclusion semaphore\n");   
        }   
    funcB ()   
        {   
        semTake (mySem, WAIT_FOREVER);   
        printf ("funcB: Got mutual-exclusion semaphore\n");   
        ...    
        semGive (mySem);   
        printf ("funcB: Releases mutual-exclusion semaphore\n");   
        }  

假设thread1调用funcA(),funcA再调用funcB(),这是没有问题的
假设thread2直接调用funB(),funB()里的这段临界区也是可以被保护的
这都得益于mutex的owner机制，如果是二值信号量这样操作的话，funcB()试图获得mySem的时候就会挂起，不能返回funcA()，mySem就一直得不到释放，形成死锁，也就是rt-thread manual里提到的
 
这个特性与一般的二值信号量有很大的不同，在信号量中，因为已经不存在实例，线程递归持有会发生主动挂起（最终形成死锁）
 
owner机制还有一个作用，就是通过优先级继承实现优先级翻转，参考stackoverflow上的一个解答:
Because the recursive mutex has a sense of ownership, the thread that grabs the mutex must be the same thread that release the mutex. In the case of non-recursive mutexes, there is no sense of ownership and any thread can usually release the mutex no matter which thread originally took the mutex. In many cases, this type of "mutex" is really more of a semaphore action, where you are not necessarily using the mutex as an exclusion device but use it as synchronization or signaling device between two or more threads.

Another property that comes with a sense of ownership in a mutex is the ability to support priority inheritance. Because the kernel can track the thread owning the mutex and also the identity of all the blocker(s), in a priority threaded system it becomes possible to escalate the priority of the thread that currently owns the mutex to the priority of the highest priority thread that is currently blocking on the mutex. This inheritance prevents the problem of priority inversion that can occur in such cases. (Note that not all systems support priority inheritance on such mutexes, but it is another feature that becomes possible via the notion of ownership).

If you refer to classic VxWorks RTOS kernel, they define three mechanisms:

mutex - supports recursion, and optionally priority inheritance
binary semaphore - no recursion, no inheritance, simple exclusion, taker and giver does not have to be same thread, broadcast release available
counting semaphore - no recursion or inheritance, acts as a coherent resource counter from any desired initial count, threads only block where net count against the resource is zero.
 
在使用rt_mutex_release()释放锁的时候，除了将mutex的value加1(mutex->value++)，还需要将mutex的owner指向NULL(mutex->owner = RT_NULL)，否则如果该mutex的owner还是这个线程，当下次这个锁未被占有，而这个线程又试图通过rt_mutex_take()获得锁的时候，就只有hold++，而没有value--，起不到临界区保护的作用

**补充**：Linux系统里是不支持recursive lock的(参考《Linux Kernel Development》第三版)，内核里对应上锁的API是mutex_lock()
Linux, thankfully, does not provide recursive locks. This is widely considered a good thing. Although recursive locks might alleviate the self-deadlock problem, they very readily lead to sloppy locking semantics
 
但是，在应用层编程上， pthread_mutexattr_settype()里可以设置flag为PTHREAD_MUTEX_RECURSIVE，对应上锁的API是pthread_mutex_lock()
