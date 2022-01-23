### Java基础
- 线程安全
  - 线程安全的根本 (JVM内存模型) 
  
    线程安全的本质是由于可见行、有序性、原子性。可见性，JVM的内存模型粗略分为工作内存（线程私有）和主内存（线程公有），Java中实例化的变量都存放在堆中（可以对应为主内存），线程中的引用是主内存变量的拷贝，线程处理完逻辑后把变量结果同步回主内存，由此引发出变量同步的问题。有序性，JVM在为字节码的时候，会把没有逻辑关联的语句重新排序（就是跟写代码时候的顺序不同）。原子性，a=a+1，a+1不是原子性操作，a+1，然后赋值给a。
  - Volatile关键字原理
  
    Volatile修饰的变量，执行写操作后立即同步到主内存，同时线程中的缓存失效，线程发现缓存失效重新获取主内存中的值。Volatile通过"内存屏障"防止指令重排。
  - Synchronized关键字原理，如何优化耗时，Synchronized(String.class)会怎么样；

    在实例方法前加Synchronized关键字，锁住的是对象实例；
    在静态方法前加Synchronized关键字，锁住的是这个类；
    在方法内部加Synchronized关键字，锁住的是(xx)内的对象；
    Synchronized是可重入锁，以线程为单位的可重入。
    
  - 线程的状态
  
    [线程状态-博客园](https://www.cnblogs.com/aspirant/p/8876670.html)
    
    wait、notify是Object类拥有的方法，成对出现，wait会让出cpu和锁资源，线程进入阻塞，notify后 cpu唤醒对应锁资源的线程，重新进入可运行状态。
    
    sleep、yield是 Thread拥有的方法，sleep让出cpu资源，间隔时间后重新进入可运行状态。yield让出cpu资源，cpu重新选择线程进入可运行状态，也有可能选到原本yield的线程，yield的作用在于告诉cpu当前线程的工作快完成了。
    
    join，在当前线程中调用其他线程的join，当前线程阻塞，直到被调用join的线程执行完毕。
    
    
  - 线程安全的单例实现
    
    饿汉式（直接成员变量就new）、懒汉式（调用的时候才初始化）、双重锁 (指令重排可能导致不安全，new Object()不是原子操作)、静态内部类（Holder）
    
    JVM保证了类加载的时候是线程安全的，所以用static修饰类成员，达到了单例的线程安全
    
  - 可重入锁、不可重入锁
  
    可重入是指当前线程获得了锁资源，当前线程可以直接再次获取锁资源。ReentrantLock和Synchronized都是可重入锁，可重入锁避免单线程的死锁。
  - 线程池；

    核心线程也可以回收，allowCoreThreadTimeOut。工作原理，线程池接到一个任务，先判断核心线程数是否够，不够就直接新起一个核心线程来执行，否则核心线程都满了，则判断缓存队列是否满，不满就加入缓存队列，满了就判断maxPoolSize是否满，不满就新起线程来执行，满就根据拒绝策略处理。
    
    非核心线程在处理完任务之后，到了超时时间就销毁。AbortPolicy, 丢弃任务并抛出异常（默认）；DiscardPolicy，丢弃任务但不抛异常；DiscardOldestPolicy，丢弃队列最前面的任务，执行现在任务；CallerRunsPolicy，由调用线程池的线程处理任务。
  - 线程安全的集合及其原理；

    HashMap由数组、链表、红黑树三种数据结构组成，把key做哈希值运算，然后得到的数组的index，如果数组对应的位置为空，就插入，否则放到对应数组下标的链表中，链表长度为8，超过就变成红黑树。
    HashMap不安全的原因，在发生hash碰撞的时候，两个线程计算得到的哈希值一样，数值index一样，此时数组为空的话，则会发生两个线程对同一index值做修改。
    
    JDK1.8后ConcurrentHashMap由桶（数组）、链表、红黑树三种数据结构组成，采用 CAS(campare and swipe) 和 synchronize实现线程安全，锁的粒度是每个桶(即每个数组元素为单位)。CAS是乐观锁，默认没有锁，若遇到锁则一直循环等待。synchronize是悲观锁，默认有锁。

  
  - ***协程***

    - C#协程
    
    unity中C#实现的协程，是在游戏主线程中执行的函数，每一帧都判断协程中代码块是否完成，所以在unity的C#协程里没有并发执行的概念。C#的协程通过 IEnumerator 接口实现
    ```
    public interface IEnumerator{   
    object Current { get; } 
    bool MoveNext(); 
    void Reset(); }
    ```
    MoveNext true时协程里的代码继续往下走，false则跳出协程代码块，后面的代码不执行。yield封装了 IEnumerator的实现，内部实现了一个枚举类，通过MoveNext来遍历访问。
    
    - Kotlin协程

      [协程作用域及挂起原理](https://www.jianshu.com/p/a10a6082e91f)
      
      [协程的取消](https://medium.com/androiddevelopers/cancellation-in-coroutines-aa6b90163629)
      
      [协程异常处理](https://medium.com/androiddevelopers/exceptions-in-coroutines-ce8da1ec060c)
      
      
- JVM 原理

