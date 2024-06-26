---
title: "操作系统：同步互斥机制"
tags: ["Operating System"]
categories: ["Operating System"]
date: 2023-05-04T20:51:49+08:00
---

# 为何需要同步互斥机制

同步互斥存在的意义只针对**多个任务都会修改同一块内存的场景**。这块内存也叫**临界区**，
要求是必须各个任务独占访问的。比如说许多线程都会往 ringbuffer 中填数据，
必须使用同步互斥机制才能保证数据的正确性。

所以说，在以下的场景中，无需考虑同步互斥：
- 如果你只有1个CPU，该CPU上只运行1一个线程
- 如果会存在多个线程（可能是多个core或者一个core上的多线程环境），但是他们不会涉及同一块内存
- 即便是多个线程访问了同一块内存，但是都是**读操作**


>需要同步互斥的场景在OS内核和用户态程序中都很常见：
>- 内核中常见的临界资源包括：对内存区域的**引用计数**操作，或者对调度队列的修改操作等。
>- 用户态那就更不用说了，同步互斥的场景很多，比如典型的读者写者问题(Buffer)
>
>
>因为OS内核和用户态程序的权限不同，所以实现同步互斥的方案也不太相同。


# 同步互斥的常见方案

## per-cpu 变量

OS 内核里有些数据结构如果不需要CPU之间共享，可以定义成per-cpu形式。
>比如说调度队列，每个CPU只关心自己核上队列的情况，如果想要访问其他CPU的，
>比如进程迁移请通过核间通信IPI来做，并不能直接访问。

> per-cpu 变量通常被安排在不同的 cache line，避免 cache 的频繁刷新

- 优点：多 CPU 之间互不干扰
- 缺点：
  - 要求逻辑独立， 极少数临界资源可以实现为 per-CPU 形式
  - 需要考虑内核抢占的影响，如果OS内核修改percpu变量时被调度，新的进程也可能修改这个变量。
    如果在中断服务函数中可能修改，还需要另外关闭内核中断。



## 原子操作

如果临界资源只是一个基础类型变量，比如说一个Flag或者引用计数。那么实现同步互斥的逻辑就比较简单。

我们知道，如果多个CPU同时对一个变量做修改(`flag++`)，结果是不可知的。这是因为一次修改其实在处理器来看分为三步:
1. load mem => register
2. update register
3. store register => mem

ISA 会提供一些原子操作的指令，将这三步绑定在一起，一旦有一个core执行了写动作，会对该内存总线独占，
只有此次写入完成后，其他core才能发起写入请求。

>这就行了？并不是
>
>原子操作只能排除上面描述的问题出现，但是多CPU情况下的同步互斥还很复杂。比如说，一个Core修改了
>这个变量，至于其他core能不能看到这次写操作，就和 memory consistency 和 cache coherence 相关，
>**实际应用中还需要考虑这些来保证逻辑的正确性**。

原子操作指令的特权级别是用户级的，也就是说OS内核和用户态程序都可以用，比如说C++就提供了相应的原子操作库函数。

- 缺点：仅适用于临界资源是简单的基础数据类型的情况。当然，**复杂操作的同步互斥实现底层也是在原子操作上做封装**





## 锁和信号量

锁的实现底层就是利用了原子操作，锁应用的场景包含OS内核和用户态:
- OS内核:
  - 自旋锁 Spinlock
  - 互斥锁 Mutex
  - 读写锁以及其改进版 RCU
- 用户态:
  - Futex（Fast Userspace mutex)

>关于信号量
>
>信号量和锁的本质是相同的，书籍会写信号量是解决同步问题，锁是解决互斥问题。二值信号量就是互斥锁。


# 选择合适的同步互斥机制

(1) 内核中**避免使用自旋锁**: 获取不到锁的 CPU 会一直死等，浪费 CPU 资源

(2) 若临界区仅仅是一个共享的整数变量操作，那么用**原子操作**即可完成，不需要复杂的锁。
例如各类数据结构的引用计数通常声明为 `atomic_t`。


# References

- [Let's Talk Locks!](https://www.youtube.com/watch?v=7OpCf6f_BAM&t=83s)