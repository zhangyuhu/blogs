---
title: "开源软件:并行编程框架TBB"
date: 2023-01-16T17:52:52+08:00
draft: false
description: "开源软件"
lightgallery: true
tags: [TBB]
categories: [开源软件]
---

<!-- TOC -->

- [TBB 基础](#tbb-基础)
  - [简介](#简介)
    - [并行](#并行)
    - [使用](#使用)
  - [接口](#接口)
    - [parallel_for](#parallel_for)
    - [parallel_reduce](#parallel_reduce)
    - [parallel_while](#parallel_while)
  - [graph](#graph)
  - [并发容器](#并发容器)
    - [concurrent_hash_map](#concurrent_hash_map)
    - [concurrent_vector](#concurrent_vector)
    - [concurrent_queue](#concurrent_queue)
  - [锁](#锁)
  - [Task Scheduler](#task-scheduler)

<!-- /TOC -->

## TBB 基础

Intel Threading Building Blocks  
[TBB API](https://spec.oneapi.com/versions/0.8/elements/oneTBB/source/nested-index.html)  

### 简介

#### 并行

1、指令级的并行；即所谓的微程序、指令流水线等，现在cpu的一级缓存、二级缓存都很大，所以这个cache的效果还是比较好的（基于局部性原理）  
2、线程级的并行；即同一个时刻多个函数在运行（现在的cpu好像都是多核的）  
3、服务级别的（比如一个游戏服务器中有商店服务、也有战斗服务、聊天服务等 这里的每个服务可能对应多个逻辑线程）  
4、节点级别的；即所谓的分布式系统，多个节点互相配合，使整个系统在逻辑上成为一个单一的系统。（google、qq等这些海量访问的服务统统是分布式的）。  
一般来说，第一个级别的并行直接做在硬件里面，第二个级别的并行会有一些基础的框架，第三和第四个级别的并行就是应用程序自己的架构的问题了。  
这里面实际上有一个争论：是在算法并行化上面花心思去研究还是采用分布式的框架来面对问题规模的增长？实际上2者各有利弊，前者可以充分利用已有硬件，但是对程序员的要求较高，维护开发成本高，风险大；后者容易实现但是浪费硬件，在有些情况下不是所有问题都可以用加个机器的方式可以解决的（比如客户端上的多媒体软件，其计算量极大，总不能要求所有用户都升级吧。）

Intel Threading Building Blocks，是为了方便程序员使用多核处理器的C++库，应该是对应上面的线程级的并行。  

#### 使用

整个程序逻辑没办法自动并行化，那就针对控制流进行并行化，所以TBB中提供了 parallel_for、parallel_while、 parallel_reduce等；（这些是TBB给C++程序员的比较高层的接口）并行肯定是多线程，这样的话数据竞争问题就比较棘手，所以TBB提供并发容器；如果觉得TBB提供的这些接口还没有办法解决性能问题，那就可以更深入的研究使用mutex、atomic、task等了；可以看出，TBB从几个层次上为程序员提供了支持。  

BB提供的接口：  
由底层到高层，task_scheduler--------concurrent_container--------parallel_for---pipeline简单说，TBB帮我们调度一个个task（比OS的调度要高效），实现高效的并行算法。  

要使用TBB进行并行化，首先程序员要知道哪些是可以并行化；其次，要熟悉TBB并行化的框架（主要是泛型编程）；再次，程序员要大概知道并行算法的执行步骤；最后，利用TBB的组件，实现并行化的算法。

### 接口

#### parallel_for

parallel_for 适用场合：多个数据或请求彼此没有依赖关系，所要进行的操作是一样的（典型SPMD）。
- blocked_range

```c++
// 典型的c++泛型编程 blocked_range 是要处理的多个数据，3个参数依次是开始的指针（迭代器）、结束指针、每个任务分配的数据数
//! Construct range over half-open interval [begin,end), with the given grainsize.
blocked_range( Value begin_, Value end_, size_type grainsize_=1 ) :
    my_end(end_), my_begin(begin_), my_grainsize(grainsize_)
{
    __TBB_ASSERT( my_grainsize>0, "grainsize must be positive" );
}
```

第一个参数表示起始，第二个参数表示结束，它们的类型为const_iterator，表示的区间为[begin，end)这样一个半开区间。第三个参数，grainsize，表示的是一个“合适的大小”块，这个块会在一个循环中进行处理，如果数组比这个grainsize还大，parallel_for会把它分割为独立的block，然后分别进行调度（有可能由多个线程进行处理）。  

这样我们知道，grainsize其实决定了TBB什么时候对数据进行划分，如果我们把grainsize指定得太小，那就可能会导致产生过多得block，从而使得不同block间的overhead增加（比如多个线程间切换的代价），有可能会使性能下降。相反，如果grainsize设得太大，以致于这个数组几乎没有被划分，那又会导致不能发挥parallel_for期望达到的并行效果，也没有达到理想得性能。所以我们在决定grainsize时需要小心，最好是能够经过调整测试后得到的值，当然你也可以如本例中一样不指定，让TBB帮你来决定合适的值（一般不是最优的）。一个调整grainsize的经验性步骤：  
1）首先把grainsize设得比预想的要大一些，通常设为10000  
2）在单处理机机器上运行，得到性能数据  
3）把grainsize减半，看性能降低多少，如果降低在5%-10%之间，那这个grainsize就已经是一个不错的设定。  

- parallel_for

```c++
//! Parallel iteration over range with simple partitioner.
/** @ingroup algorithms **/
template<typename Range, typename Body>
void parallel_for( const Range& range, const Body& body, const simple_partitioner& partitioner ) {
    internal::start_for<Range,Body,const simple_partitioner>::run(range,body,partitioner);
}
```

其参数：  
1）range：指定划分block的范围。  
2）body：指定对block应用的操作，Body可以看成是一个操作子functor，它的operator(...)会以blocked_range为参数进行调用，当然如果我们传过来的是一个函数指针也是可以的，只要它能以blocked_range为参数进行调用。  
3）partitioner：指定划分器，可选的两种simple_partitioner和auto_partitioner。  

``` C++
parallel_for(0,100,[](int v){cout<<v<<endl;});
```

``` c++
// parallel_forFibBody可以简单理解为一个函数对象（c++里是用运算符重载实现的，即（）是通信的接口）
parallel_for( blocked_range<int>( 1, my_n, 10 ), parallel_forFibBody(my_stream) );

struct parallel_forFibBody {
    QueueStream &my_stream;
    //! fill functor arguments
    parallel_forFibBody(QueueStream &s) : my_stream(s) { }
    // 这里是并行的代码
    void operator()( const blocked_range<int> &range ) const {
        int i_end = range.end();
        for( int i = range.begin(); i != i_end; ++i ) {
            my_stream.Queue.push( Matrix1110 ); // push initial matrix
        }
    }
};
```

#### parallel_reduce

parallel_reduce 适合于需要汇总的情况，即各个数据的结果需要汇总回来。

```c++
/** parallel_reduce overloads that work with anonymous function objects
    (see also \ref parallel_reduce_lambda_req "requirements on parallel_reduce anonymous function objects"). **/

//! Parallel iteration with reduction and default partitioner.
/** @ingroup algorithms **/
template<typename Range, typename Value, typename RealBody, typename Reduction>
Value parallel_reduce( const Range& range, const Value& identity, const RealBody& real_body, const Reduction& reduction ) {
    internal::lambda_reduce_body<Range,Value,RealBody,Reduction> body(identity, real_body, reduction);
    internal::start_reduce<Range,internal::lambda_reduce_body<Range,Value,RealBody,Reduction>,const __TBB_DEFAULT_PARTITIONER>
                          ::run(range, body, __TBB_DEFAULT_PARTITIONER() );
    return body.result();
}
```

``` c++
float ParallelSumFoo(float a[], size_t n) {

    return parallel_reduce(tbb::blocked_range<size_t>(0, n),
        float(0.f),
        [a](const tbb::blocked_range<size_t>& r, const float& x) -> float {
            float sum = x;
            for (size_t i = r.begin(); i != r.end(); ++i) {
                sum += foo(a[i]);
            }

            return sum;
        },
        [](const float& x, const float& y) -> float{
            return x + y;
        });
}
```

```C++
//注意分发下去和汇总回来的方法
float ParallelSumFoo( const float a[], size_t n ) {
    SumFoo sf(a);
    parallel_reduce(blocked_range<size_t>(0,n,IdealGrainSize), sf );
    return sf.sum;
}

class SumFoo {
    float* my_a;

    public:
        float sum;
        void operator()( const blocked_range<size_t>& r ) {
            float *a = my_a;
            for( size_t i=r.begin(); i!=r.end(); ++i )
                sum += Foo(a[i]);
        }

        SumFoo( SumFoo& x, split ) : my_a(x.my_a), sum(0) {} // 分发任务，注意这个构造器要求是线程安全的

        void join( const SumFoo& y ) {sum+=y.sum;} // 收集汇总结果

        SumFoo(float a[] ) :
            my_a(a), sum(0)
        {}
};


```

parallel_reduce的定义与parallel_for类似，但parallel_reduce却与其也有很大的不同点：  
1）operator()是非const  
parallel_reduce必须对SumFoo::sum进行更新，以便后面执行join()时使用。  
2）SumFoo有一个带有split类的构造函数  
带有split类的SumFoo构造函数，split参数用于原对象的引用，用于区分copy构造函数。  
3）join（）操作  
当任何一个subtask执行完成时就会调用join()方法，该方法将subtask执行完的结果merge到main body中。  
parallel_reduce执行流程如下图所示：  

<div align=center><img src="image/tbb_join.png"/></div>

具体来说就是：当任务调度器发现有可供调度的工作线程时，parallel_reduce就调用代用split的构造函数为处理器创建一个子任务（Invoking the splitting constructor to create a subtask for the processor）；当子任务执行完成，parallel_reduce则调用join()方法累加子任务的结果。

#### parallel_while

有时不知道循环何时结束，即使用for的end未知，在这种情况下可以使用parallel_while。

```C++
//注意pop_if_present、typedef Item* argument_type、operator()等部分的处理
// 串行版本
void SerialApplyFooToList( Item*root ) {
    for( Item* ptr=root; ptr!=NULL; ptr=ptr->next )
        Foo(pointer->data);
}

// 并行版本
class ItemStream {
    Item* my_ptr;

    public:
        bool pop_if_present( Item*& item ) { // 用于提供下一个迭代器
            if( my_ptr ) {
                item = my_ptr;
                my_ptr = my_ptr->next;
                return true;
            } else {
                return false;
            }
        };
        ItemStream( Item* root ) : my_ptr(root) {}
}

class ApplyFoo {
    public:
        void operator()( Item* item ) const { // 要求一定是const的
            Foo(item->data);
        }
        typedef Item* argument_type; // 此句是必须的
};

void ParallelApplyFooToList( Item*root ) {
    // parallel_while是个class
    parallel_while<ApplyFoo> w; //  先建立个对象
    ItemStream stream;
    ApplyFoo body;

    // 第一个参数提供数据指针，第二个参数提供函数体
    w.run( stream, body );
}
```

### graph

```c++
//! Makes an edge between a single predecessor and a single successor
template< typename T >
inline void make_edge( sender<T> &p, receiver<T> &s ) {
    internal_make_edge( p, s );
}
```

### 并发容器

大部分程序都有容器类，在多线程环境下就有数据污染的问题，为了使并发的线程串行化，一般是使用加锁的办法，如果这个容器由程序员自己来实现，难度还是比较大的，这样就需要有线程安全的容器类。

#### concurrent_hash_map

hash接口与stl类似  

#### concurrent_vector

grow_by(n) 插入n个item（动态增长）  
grow_to_at_least（）设定容器的大小  
size()  包括正在并发增长的部分 因为有可能会同时取，所以程序员需要自己维护自己的class的线程安全性  
clear() 不是线程安全的  

#### concurrent_queue

pop_if_present(item) 非阻塞，  
pop（） 阻塞，  
concurrent_queue::size() 负数时表示有多少个消费者在等待  
set_capacity（）指定队列大小，会使push操作被阻塞  
在并行时，paralell_while pipeline 的效率要高于concurrent_queue  

### 锁

如果觉得TBB的加锁效率不高，可以自己控制锁，最常用的是spin lock。

### Task Scheduler

整个TBB引擎的核心是 Task Scheduler（基于任务图来实现），提高效率的核心是threading stealing，保证cpu的效率。
