### 基础概念
#### 线程

* 进程里执行代码的部分；
* 包含一系列机器指令所必须的机器状态，包括当前指令位置(一般为PC寄存器)、栈顶指针SP、通用寄存器、地址和数据寄存器等
* 线程不包括进程中的其他数据，如地址空间和文件描述符

#### 进程

* 线程加上地址空间、文件描述符和其他数据
* 一个进程中的所有线程共享文件和地址空间，包括程序段、数据段和堆栈

#### 进程 vs 线程

* 多个线程可以共享一个地址空间，而做不同的事情
* 在多处理器系统中，一个进程中的多个线程可以同时做不同的工作
* 系统在线程间切换比在进程间切换快得多
* 每一个进程有独立的虚拟地址空间，但同一个进程中的线程共享相同的地址空间和其他进程数据

#### 异步(asynchronous)
事情相互独立地发生，除非有强加的依赖性。任何两个彼此独立运行的操作都是异步的。

* 如果没有同时执行多个活动，那么异步就没有优势
* 如果开始了一个异步活动，然后什么也不做就等待他结束，则并没有从异步获得好处

#### 并发(concurrency)

* 实际上可能是串行发生的事情好像同时发生一样
* 并发描述的是单处理器系统中线程或进程的行为
* 在POSIX中，并发的定义要求“延迟调用线程的函数不应该导致其他线程的无限期延迟”
* 并发操作之间可能任意交错，导致程序相互独立的运行(一个程序不必等到另一个程序结束后才开始运行)，但并发并不代表操作同时进行

![并发][concurrency]

#### 并发 vs 并行
并发：指并发序列同时执行。指事情在相同的方向上独立进行(没有交错)。

![并行][parallelism]

* 真正的并行只能在多处理器系统中存在
* 但并发可以在单处理器系统和多处理器系统中都存在
* 并发能在单处理器系统中存在是因为并发实际上是并行的假象
* 并行则要求程序能够同时执行多个操作
* 而并发只要求程序能够假装同时执行多个操作

### 线程安全
#### 什么是线程安全？

* 定义：指代码能够被多个线程调用而不会产生灾难性后果
* 特点：不要求代码在多个线程中高效的运行，只要求能够安全地运行

#### 实现线程安全的工具
pthreads 互斥量、条件变量、线程私有数据

#### 如何实现线程安全？

* 一般方法：
	* 对不需要保存永久状态的函数，通过整个函数调用的串行化实现
	* 比如，进入函数时加锁，退出函数时解锁
	* => 函数可以被多个线程调用，但一次只能有一个线程调用该函数
* 更有效的方式
	* 将线程安全函数分为多个小的临界区
	* => 允许多个线程进入该函数，但不能同时进入一个临界区
* 更好的方式
	* 将代码改造为对临界对象(数据)的保护而非对临界代码的保护
	* => 可使不同时访问相同临界数据的线程完全并行的执行

###  可重入
#### 可重入

* 有时用来表示有效的线程安全，即通过采用比将函数或库转换成一系列区域更为复杂的方式使代码成为线程安全的
* 可重入的函数应该避免依赖任何静态数据，最好避免依赖线程间任何形式的同步
* 互斥量和线程私有数据可以实现线程安全，但通常需要改变接口来使函数可重入

#### 举例

* pthreads 为了使 readdir() 函数可重入，增加 readdir_r() 函数，并在该函数内避免任何锁操作
* 让调用者在搜索目录时分配一个数据结构来保存 readdir_r() 的环境

#### 特点
这种方式只有调用者才知道数据如何使用。

### 并发系统基本功能
#### 基本功能

* 执行环境：是并发实体的状态；提供建立、删除、维护环境的方式
* 调度：决定在某个给定时刻该执行哪个环境，并在不同的环境中切换
* 同步：为并发执行的环境提供协调访问共享资源的机制

什么是同步？——让线程协调地完成工作的机制

#### 同步的实现方式

* 互斥量
* 条件变量
* 信号量
* 事件
* 消息机制：管道/Socket/Posix消息队列

#### 线程、互斥量、条件变量关系

* 线程是计算机中的可执行单元，是 CPU 调度单位
* 互斥量和条件变量都是线程同步的手段
* 互斥量阻止线程间发生不可预期的冲突
* 一旦避免了冲突，条件变量让线程等待直到可以安全地执行

整理来自：[Reference](http://blog.csdn.net/livelylittlefish/article/details/7918110)

[concurrency]: https://github.com/AngryHacker/ocean/blob/master/creative/image/concurrency.jpg
[parallelism]: https://github.com/AngryHacker/ocean/blob/master/creative/image/parallelism.jpg
