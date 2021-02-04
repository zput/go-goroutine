---
title: golang调度
date: 2019-02-28 15:03:00
tags:
  - golang
  - goroutine
categories:
    - golang调度
toc: true
layout: page
---

## 基本框架

- part1: 进入main函数之前的初始化:
  - begin ---> main的过程;
    - 会包括gorountine是如何产生的
- part2: 退出[exit]:
  - 非main函数goroutine退出
- part3: 调度:
  - 调度循环
    - 从一个goroutine,到如何进入下一个goroutine
      - main函数运行go关键字, 创建新的goroutine, 放入队列, 等待调度.(这里可以参考part1的main Goroutine的创建过程)
  - 调度的时机[调度时机，即什么时候会发生调度]
    - 被动调度: goroutine执行某个操作因条件不满足需要等待而发生的调度；
      - goroutine进入睡眠[比如是阻塞在chan上面,那么睡眠是:放在chan的goroutine挂入channel的读取队列];
      - 重新运行schedule()
      - 唤醒睡眠中的goroutine;
      - 唤醒空闲的P和唤醒创建工作线程;
        - goroutine(*被创建出来后/创建运行了一段时间后*)如何放入运行队列[P中];
    - 主动调度: goroutine主动调用Gosched()函数让出CPU而发生的调度；
    - 抢占调度: goroutine运行时间太长或长时间处于系统调用之中而被调度器剥夺运行权而发生的调度。

![调度](https://raw.githubusercontent.com/zput/myPicLib/master/zput.github.io/20200910164826.png)



## 最终的路线图

![goroutine调度完整图形](https://raw.githubusercontent.com/zput/myPicLib/master/zput.github.io/goroutine%E8%B0%83%E5%BA%A6%E5%AE%8C%E6%95%B4%E5%9B%BE%E5%BD%A2.png)





为什么需要P?


P的本地运行队列
  - 双端队列
  - 怎么排列的?
  - 是否是浪费了一个index空间.
  - 本地运行队列是255还是256?

```go
// runqput tries to put g on the local runnable queue.
// If next is false, runqput adds g to the tail of the runnable queue.
// If next is true, runqput puts g in the _p_.runnext slot.
// If the run queue is full, runnext puts g on the global queue.
// Executed only by the owner P.
func runqput(_p_ *p, gp *g, next bool) {
	if randomizeScheduler && next && fastrand()%2 == 0 {
		next = false
	}

	if next {
	retryNext:
		oldnext := _p_.runnext
		if !_p_.runnext.cas(oldnext, guintptr(unsafe.Pointer(gp))) {
			goto retryNext
		}
		if oldnext == 0 {
			return
		}
		// Kick the old runnext out to the regular run queue.
		gp = oldnext.ptr()
	}

retry:
	h := atomic.LoadAcq(&_p_.runqhead) // load-acquire, synchronize with consumers
	t := _p_.runqtail
	if t-h < uint32(len(_p_.runq)) {
		_p_.runq[t%uint32(len(_p_.runq))].set(gp)
		atomic.StoreRel(&_p_.runqtail, t+1) // store-release, makes the item available for consumption
		return
	}
	if runqputslow(_p_, gp, h, t) {
		return
	}
	// the queue is not full, now the put above must succeed
	goto retry
}
```



	h := atomic.LoadAcq(&_p_.runqhead) // load-acquire, synchronize with consumers
	t := _p_.runqtail
	if t-h < uint32(len(_p_.runq)) {
		_p_.runq[t%uint32(len(_p_.runq))].set(gp)
		atomic.StoreRel(&_p_.runqtail, t+1) // store-release, makes the item available for consumption
		return
	}
P初始化的时候看下这个,这三个字段是怎么初始化的?
runqhead runqtail





- 把golang assmble example 作为一篇博客 + golang的stack的结构是怎么样的.


初始化全局变量g0
runtime/asm_amd64.s 
	// create istack out of the given (operating system) stack.
	// _cgo_init may update stackguard.
	MOVQ	$runtime·g0(SB), DI
这个 $runtime.g0---> 







https://github.com/golang/go/blob/d029058b5912312963225c40ae4bf44e3cb4be76/src/runtime/HACKING.md



