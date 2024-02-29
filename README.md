***记录日常学习打卡，持续更新***


### Java基础
- 线程安全
  - 线程安全的根本 (JVM内存模型) 
  
    线程安全的本质是由于可见性、有序性、原子性。可见性，JVM的内存模型粗略分为工作内存（线程私有）和主内存（线程公有），Java中实例化的变量都存放在堆中（可以对应为主内存），线程中的引用是主内存变量的拷贝，线程处理完逻辑后把变量结果同步回主内存，由此引发出变量同步的问题。有序性，JVM在为字节码的时候，会把没有逻辑关联的语句重新排序（就是跟写代码时候的顺序不同）。原子性，a=a+1，a+1不是原子性操作，a+1，然后赋值给a。
  - Volatile关键字原理
  
    Volatile修饰的变量，执行写操作后立即同步到主内存，同时线程中的缓存失效，线程发现缓存失效重新获取主内存中的值。Volatile通过"内存屏障"防止指令重排。
  - Synchronized关键字原理，如何优化耗时，Synchronized(String.class)会怎么样:
 
    
  在Java中，使用synchronized(String.class)表示你想要对String.class这个类的字面量对象进行同步。这个做法会锁定String类的Class对象，确保一次只有一个线程能执行由这个锁保护的代码块或同步方法。

  这种做法通常是不推荐的，原因包括：

  全局性的锁定：因为类的字面量在Java虚拟机（JVM）中是唯一的，所以这种锁实际上是全局性的。这意味着你的代码中任何使用了synchronized(String.class)的地方都会被同步，这可能会导致意想不到的性能瓶颈或死锁。

  String常量池：字符串字面量在Java中通常是被interned的，这意味着相同内容的字符串字面量会引用同一个对象。当你使用String对象作为锁时，你可能无意中和其他无关的代码同步，因为他们可能使用了相同的字符串字面量。

  可维护性和清晰性：使用类字面量作为锁通常会使代码难以理解和维护，因为它不是锁定特定的实例，而是与整个类相关联，这可能会在不同的代码部分引入隐藏的依赖关系。
  
    

    在实例方法前加Synchronized关键字，锁住的是对象实例；
    在静态方法前加Synchronized关键字，锁住的是这个类；
    在方法内部加Synchronized关键字，锁住的是(xx)内的对象；
    Synchronized是可重入锁，以线程为单位的可重入。
  
  - 死锁
    
    至少两个线程无序抢占彼此已经占有的锁资源，导致相关线程都进入阻塞。解决办法：按顺序获得所有的锁资源、获取锁资源加入超时判断。
  - interrupt方法

    [知乎](https://www.zhihu.com/question/41048032) 
    
    [如何停止一个线程](https://cloud.tencent.com/developer/article/1591144)
    
    
  - 线程的状态
  
    [线程状态-博客园](https://www.cnblogs.com/aspirant/p/8876670.html)
    
    wait、notify是Object类拥有的方法，成对出现，wait会让出cpu和锁资源，线程进入阻塞，notify后 cpu唤醒对应锁资源的线程，重新进入可运行状态。
    
    sleep和wait区别使用方面：
    从使用的角度来看sleep方法是Thread线程类的方法，而wait是Object顶级类的方法。
    sleep可以在任何地方使用，而wait只能在同步方法和同步块中使用。
    CPU及锁资源释放:
    sleep、wait调用后都会暂停当前线程并让出CPU的执行时间，但不同的是sleep不会释放当前持有对象的锁资源，到时间后会继续执行，而wait会释放所有的锁并需要notify/notifyAll后重新获取到对象资源后     才能继续执行。
    异常捕获方面：
    sleep需要捕获或者抛出异常，而wait/notify/notifyAll则不需要。
    
    
    sleep、yield是 Thread拥有的方法，sleep让出cpu资源，间隔时间后重新进入可运行状态。yield让出cpu资源，cpu重新选择线程进入可运行状态，也有可能选到原本yield的线程，yield的作用在于告诉cpu当前线程的工作快完成了。
    
    join，在当前线程中调用其他线程的join，当前线程阻塞，直到被调用join的线程执行完毕。
    
    
  - 线程安全的单例实现
    
    饿汉式（直接成员变量就new）、懒汉式（调用的时候才初始化）、双重锁 (指令重排可能导致不安全，new Object()不是原子操作)、静态内部类（Holder）
    
    JVM保证了类加载的时候是线程安全的，所以用static修饰类成员，达到了单例的线程安全
    
  - 可重入锁、不可重入锁
  
    可重入是指当前线程获得了锁资源，当前线程可以直接再次获取锁资源。ReentrantLock和Synchronized都是可重入锁，可重入锁避免单线程的死锁。
    
    [java锁升级](https://tech.meituan.com/2018/11/15/java-lock.html)
    
无锁

无锁没有对资源进行锁定，所有的线程都能访问并修改同一个资源，但同时只有一个线程能修改成功。

无锁的特点就是修改操作在循环内进行，线程会不断的尝试修改共享资源。如果没有冲突就修改成功并退出，否则就会继续循环尝试。如果有多个线程修改同一个值，必定会有一个线程能修改成功，而其他修改失败的线程会不断重试直到修改成功。上面我们介绍的CAS原理及应用即是无锁的实现。无锁无法全面代替有锁，但无锁在某些场合下的性能是非常高的。

偏向锁

偏向锁是指一段同步代码一直被一个线程所访问，那么该线程会自动获取锁，降低获取锁的代价。

在大多数情况下，锁总是由同一线程多次获得，不存在多线程竞争，所以出现了偏向锁。其目标就是在只有一个线程执行同步代码块时能够提高性能。

当一个线程访问同步代码块并获取锁时，会在Mark Word里存储锁偏向的线程ID。在线程进入和退出同步块时不再通过CAS操作来加锁和解锁，而是检测Mark Word里是否存储着指向当前线程的偏向锁。引入偏向锁是为了在无多线程竞争的情况下尽量减少不必要的轻量级锁执行路径，因为轻量级锁的获取及释放依赖多次CAS原子指令，而偏向锁只需要在置换ThreadID的时候依赖一次CAS原子指令即可。

偏向锁只有遇到其他线程尝试竞争偏向锁时，持有偏向锁的线程才会释放锁，线程不会主动释放偏向锁。偏向锁的撤销，需要等待全局安全点（在这个时间点上没有字节码正在执行），它会首先暂停拥有偏向锁的线程，判断锁对象是否处于被锁定状态。撤销偏向锁后恢复到无锁（标志位为“01”）或轻量级锁（标志位为“00”）的状态。

偏向锁在JDK 6及以后的JVM里是默认启用的。可以通过JVM参数关闭偏向锁：-XX:-UseBiasedLocking=false，关闭之后程序默认会进入轻量级锁状态。

轻量级锁

是指当锁是偏向锁的时候，被另外的线程所访问，偏向锁就会升级为轻量级锁，其他线程会通过自旋的形式尝试获取锁，不会阻塞，从而提高性能。

在代码进入同步块的时候，如果同步对象锁状态为无锁状态（锁标志位为“01”状态，是否为偏向锁为“0”），虚拟机首先将在当前线程的栈帧中建立一个名为锁记录（Lock Record）的空间，用于存储锁对象目前的Mark Word的拷贝，然后拷贝对象头中的Mark Word复制到锁记录中。

拷贝成功后，虚拟机将使用CAS操作尝试将对象的Mark Word更新为指向Lock Record的指针，并将Lock Record里的owner指针指向对象的Mark Word。

如果这个更新动作成功了，那么这个线程就拥有了该对象的锁，并且对象Mark Word的锁标志位设置为“00”，表示此对象处于轻量级锁定状态。

如果轻量级锁的更新操作失败了，虚拟机首先会检查对象的Mark Word是否指向当前线程的栈帧，如果是就说明当前线程已经拥有了这个对象的锁，那就可以直接进入同步块继续执行，否则说明多个线程竞争锁。

若当前只有一个等待线程，则该线程通过自旋进行等待。但是当自旋超过一定的次数，或者一个线程在持有锁，一个在自旋，又有第三个来访时，轻量级锁升级为重量级锁。

重量级锁

升级为重量级锁时，锁标志的状态值变为“10”，此时Mark Word中存储的是指向重量级锁的指针，此时等待锁的线程都会进入阻塞状态。
    
  - 线程池；

    核心线程也可以回收，allowCoreThreadTimeOut。工作原理，线程池接到一个任务，先判断核心线程数是否够，不够就直接新起一个核心线程来执行，否则核心线程都满了，则判断缓存队列是否满，不满就加入缓存队列，满了就判断maxPoolSize是否满，不满就新起线程来执行，满就根据拒绝策略处理。
    
    非核心线程在处理完任务之后，到了超时时间就销毁。AbortPolicy, 丢弃任务并抛出异常（默认）；DiscardPolicy，丢弃任务但不抛异常；DiscardOldestPolicy，丢弃队列最前面的任务，执行现在任务；CallerRunsPolicy，由调用线程池的线程处理任务。
  - 线程安全的集合及其原理；

    HashMap由数组、链表、红黑树三种数据结构组成，把key做哈希值运算，然后得到的数组的index，如果数组对应的位置为空，就插入，否则放到对应数组下标的链表中，链表长度为8，超过就变成红黑树。
    HashMap不安全的原因，在发生hash碰撞的时候，两个线程计算得到的哈希值一样，数值index一样，此时数组为空的话，则会发生两个线程对同一index值做修改。
    
    JDK1.8后ConcurrentHashMap由桶（数组）、链表、红黑树三种数据结构组成，采用 CAS(campare and swipe) 和 synchronize实现线程安全，锁的粒度是每个桶(即每个数组的首节点)。CAS是乐观锁，默认没有锁，若遇到锁则一直循环等待。synchronize是悲观锁，默认有锁。

- 生产者消费者

  [根本上理解](https://javabetter.cn/thread/shengchanzhe-xiaofeizhe.html#blockingqueue-%E5%AE%9E%E7%8E%B0%E7%94%9F%E4%BA%A7%E8%80%85-%E6%B6%88%E8%B4%B9%E8%80%85)

  LinkedBlockingQueue阻塞队列，put方法发现队列满则阻塞调用put方法的线程，take方法发现队列空则阻塞调用take方法的线程

- 在View的OnClick中加上Sleep(10s)会发生什么

  有可能会触发ANR，如果在Sleep期间，用户触摸了屏幕则会产生ANR（其它线程发消息过来也会触发）。如果在Sleep触发View刷新事件，事件会等到Sleep完成后再进行处理。Sleep期间主线程处于挂起状态

  
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

      
      
      [协程的取消](https://medium.com/androiddevelopers/cancellation-in-coroutines-aa6b90163629)
      
      [协程异常处理](https://medium.com/androiddevelopers/exceptions-in-coroutines-ce8da1ec060c)
      
    - 番外篇  Flow  

    冷流是一种一对一的关系，事件的接收从订阅开始（subscribe），事件发射完数据流则结束，不同订阅者接收到的数据一致，如下代码:
    
    ```
    Observable<Long> cold = Observable.interval(500, TimeUnit.MILLISECONDS); // emits a long every 500 milli seconds
    cold.subscribe(l -> System.out.println("sub1, " + l)); // subscriber1
    Thread.sleep(1000); // interval between the two subscribes
    cold.subscribe(l -> System.out.println("sub2, " + l)); // subscriber2
    
    //输出如下：
    sub1, 0    -> subscriber1 starts
    sub1, 1
    sub1, 2
    sub2, 0    -> subscriber2 starts
    sub1, 3
    sub2, 1
    sub1, 4
    sub2, 2
    ```
    
    热流是一种一对多的关系，一旦触发则一直发送数据（publish），不同时刻触发订阅的订阅者接收到的数据不一样（按照数据自身的变化），如下代码：
    
    ```
    Observable.interval(500, TimeUnit.MILLISECONDS)
    .publish(); // publish converts cold to hot
    
    ConnectableObservable<Long> hot = Observable
                                    .interval(500, TimeUnit.MILLISECONDS)
                                    .publish(); // returns ConnectableObservable
    hot.connect(); // connect to subscribe

    hot.subscribe(l -> System.out.println("sub1, " + l));
    Thread.sleep(1000);
    hot.subscribe(l -> System.out.println("sub2, " + l));
    
    //输出如下：
    sub1, 0  -> subscriber1 starts
    sub1, 1
    sub1, 2
    sub2, 2  -> subscriber2 starts
    sub1, 3
    sub2, 3
    ```
    
    StateFlow必须有初始值，订阅即接收到初始值。ShareFlow没有初始值，订阅后直到上游有事件发送才会接收到。StateFlow适合MVI开发模式，ShareFlow适合MVVM模式。[区别解释](https://medium.com/swlh/introduction-to-flow-channel-and-shared-stateflow-e1c28c5bc755)
    
    [StateFlow与SharedFlow](https://juejin.cn/post/6937138168474894343)
      
      
- JVM 原理
   
  Java虚拟机运行时数据区由方法区、堆、虚拟机栈、程序计数器、本地方法栈组成，其中方法区、堆是所有线程共享的，虚拟机栈、程序计数器、本地方法栈是每个线程独有的。
  - 程序计数器
 
    当前线程所执行的字节码的行号指示器，循环、跳转、异常处理、线程恢复等字节码指令都需要依赖程序计数器。
  - Java虚拟机栈

    与线程的生命周期一样，每个方法在线程执行时都会创建一个栈帧，用于存放局部变量表（包括基本数据类型boolean\byte\char\int\short\float\long）、方法出口等信息，一个方法从调用到完成，可以看作是在虚拟机栈中入栈到出栈的过程 (平时说的堆栈信息就是这么个意思)。
  - 本地方法栈

    作用与Java虚拟机栈类似，区别在于本地方法栈作用于Native方法。 
  - 堆

    Java堆是虚拟机所管理的内存中最大的一部分，用于存放所有的对象实例，所有的对象实例都在这里分配内存。
  - 方法区

    用于存储已被虚拟机加载的类信息、常量、静态变量、即时编译后的代码（JIT） 。

    JVM的方法区主要存放以下内容：

  1.类的元数据（Metadata）：包括类的名称、访问修饰符、常量池、字段描述、方法描述等信息。

  2.静态变量：类中使用static关键字修饰的变量，这些变量在方法区中存储。

  3.常量：包括字符串常量、final常量等。

  4.类的非静态变量信息：即使这些变量是未被实例化的，它们的信息也需要存放在方法区中。

  5.方法字节码：即编译后的Java方法代码。

  6.常量池：存放编译期生成的各种字面量和符号引用。

  7.符号引用：包括类和接口的全限定名、字段的名称和描述符、方法的名称和描述符等。


    
 类加载的5个时机，1）遇到new\getstatic\putstatic\invokestatic字节指令时。2）使用java.lang.reflect方法进行反射调用时。3）当初始化一个类时，发现父类还没加载，则先加载父类。4）虚拟机启动时，包含在主函数中（main() ）的类。5）JDK1.7动态语言支持，如果一个java.lang.invoke.MethodHandle实例最后的解析结果 REF_getStatic\REF_putStatic\REF_invokeStatic方法句柄。
   
 
 强引用，如new Object()，只要引用还在就不会回收。软引用，SoftReference，用于描述一些还有用但并非必须的对象，在系统将要发生内存溢出之前，将会把这些对象列进回收范围之中进行第二次回收。弱引用，WeakReference，被弱引用关联的对象只能活到下一次垃圾收集之前。虚引用，虚引用关联的对象，完全不会对其生命周期构成影响，无法通过虚引用取得一个对象实例，为对象设置虚引用的唯一作用是能在这个对象被回收时收到一个系统通知。
 
 - 垃圾收集算法
   - 标记-清除  

     分为标记、清除两个阶段，缺点是会产生不连续的内存碎片，且标记和清除都不是高效率。
   - 复制
     
     把内存块分为两块，一块用完后，把存活的对象复制去另外一块，清理对应的内存块。效率高，代价是把内存块一分为二，减少了可分配的内存。
     
   - 标记-整理

     与标记-清楚不同在于，回收对象时，把存活的对象往一端移动，不会产生内存碎片。 
 
 CMS垃圾收集器（Concurrent Mark Sweep）是基于标记-清除算法实现，其分为初始标记、并发标记、重新标记、并发清除，其中初始标记、重新标记是Stop The World的处理，基于标记-清除缺点是会产生内存碎片，且CMS无法处理浮动垃圾（因为是并发清理）。
 
 G1垃圾收集器整体基于标记-整理算法实现，把Java堆按Region划分，先回收价值最大的Region，且其停顿时间可预测（因为按Region划分）。

 - GCRoot说明及示例
  ```java
/**在Java开发中，以下几种对象可以作为GCRoot：

虚拟机栈（栈帧中的局部变量表）中引用的对象
方法区中类静态属性引用的对象
方法区中常量引用的对象
本地方法栈中JNI（Java Native Interface）引用的对象
示例代码及注释说明如下：
**/
public class GCRootExample {

    // 虚拟机栈（栈帧中的局部变量表）中引用的对象
    public void stackRoot() {
        Object obj = new Object(); // obj 是一个GCRoot
        // 可能的引用传递，如传递给其他方法或者线程等
    }

    // 方法区中类静态属性引用的对象
    private static Object staticObj; // staticObj 是一个GCRoot

    // 方法区中常量引用的对象
    private final Object constantObj = new Object(); // constantObj 是一个GCRoot

    // 本地方法栈中JNI引用的对象
    public native void nativeMethod();
}
在上面的示例中，obj、staticObj、constantObj都可以作为GCRoot，因为它们分别属于虚拟机栈中的局部变量表、方法区中的类静态属性、方法区中的常量。
  ```
 
 - 双亲委派机制

   JVM收到类加载请求的时候，当前的classloader不会直接发起加载，先交给父classloader去加载，只有父classloader加载失败，当前classloader才会发起加载。双亲委派保证了类不会重复加载，要破坏双亲委派则重写loadclass方法。

   [双亲委派详解](https://blog.csdn.net/swadian2008/article/details/122364441)
 
 
- 插件化
  
  app bundle原理：
  
  基于Split Apks加载apk文件，通过AAPT2可以指定Resource的pp字段或固定资源id来解决资源冲突的问题，AssetsManager#addAssetPath把插件的资源路径加入。在8.1及以上版本DexClassLoader和PathClassLoader基本上一样（都能加载外部Dex文件），在8.0以前DexClassLoader的构造方法有一个optimizeDirectiry的参数，用于指定dex2oat的文件。
  
  [Resource与AssetsManager的关系](https://sharrychoo.github.io/blog/android-source/resources-manager)
  
  [Qigsaw原理](https://blog.csdn.net/u014294681/article/details/116783861?spm=1001.2014.3001.5502)
  [Qigsaw自序原理](https://www.bookstack.cn/read/Qigsaw/spilt.3.spilt.2.c47ca9c7359b0d0d.md)
 
 Qigsaw本身具备热更新能力，配合Tinker，更新Qigsaw的配置json文件修改其中的插件版本号，达到热更效果（这是因为Qigsaw的配置文件位于assest目录下，所以需要借助Tinker的热修复能力，一个可以探索的方式是把配置文件写到app目录下的sdcard，然后从自己的服务器拉取）。单类加载器、插件资源id的pp位从7f开始递减。
 
 Art虚拟机优先加载primary dex也就是classes.dex，Tinker的原理即把patch.dex与apk中的classed.dex做合并成新的classes.dex达到热更新目的。

 [插件化原理解释](https://github.com/Demo-H/Android-Notes/blob/master/notes/android/Android%E6%8F%92%E4%BB%B6%E5%8C%96%E6%8A%80%E6%9C%AF%E2%80%94%E2%80%94%E5%8E%9F%E7%90%86%E7%AF%87.md)

 插件实现热修复：在 BaseDexClassLoader 里，有一个 DexPathList 变量，在 DexPathList 的实现里，有一个 Element[] dexElements 变量，这里面保存了所有的 dex。在加载 Class 的时候，就遍历 dexElements 成员，依次查找 Class，找到以后就返回
，需要重启应用才生效，因为已经加载的类在内存中就不会在加载 
- Jetpack Compose原理

通过Modifier来修改组件的padding\marging\宽高等属；通过State来做UI刷新，observerAsState可以把livedata转成State，从而可以继续使用viewmodel实现数据驱动刷新；AndroidComposeView继承ViewGroup，里面持有 LayoutNode调用draw方法；remember可以保存数据，在recompose(重绘)的时候对应的节点不用重新刷新；通过Layout这个Composeable实现自定义组件，measure\draw\position三个方法；

  [绘制原理](https://juejin.cn/post/6966241418046078983)
  
  [为什么需要compose,声明式UI](https://medium.com/androiddevelopers/understanding-jetpack-compose-part-1-of-2-ca316fe39050)
  
  [compose刷新的底层原理](https://medium.com/androiddevelopers/under-the-hood-of-jetpack-compose-part-2-of-2-37b2c20c6cdd)
  
  [Jetpack-LiveData组件的问题](https://mp.weixin.qq.com/s/Ai3__FSCXk-lCa4UFyEbYQ)
  
  [livedata丢失数据问题](https://juejin.cn/post/6844903846624362510)
  
  
- flock保活原理

由于android系统杀进程组是通过for循环40次每次5ms杀死进程，只要在这200ms内新拉起一个进程，则可以避开这200ms

通过反射调用ActivityManagerNative#getDefault方法获得ActivityManagerService的代理类ActivityManagerProxy，在Java层封装好Parcel数据，直接binder通信实现AMS#startService

[weishu](https://weishu.me/2020/01/16/a-keep-alive-method-on-android/) 
[gityuan](http://gityuan.com/2018/02/24/process-keep-forever/)

- FPS监控.  [tencent matrix的实现方案](https://github.com/Tencent/matrix/wiki/Matrix-Android-TraceCanary)

```
Choreographer.getInstance().postFrameCallback(new Choreographer.FrameCallback() {
    @Override    
    public void doFrame(long frameTimeNanos) {
        if(frameTimeNanos - mLastFrameNanos > 100) {
            ...
        }
        mLastFrameNanos = frameTimeNanos;
        Choreographer.getInstance().postFrameCallback(this);
    }
});
```

上面的callback每一帧都会调用，可以通过判断两帧间的时间差是否超过16ms来判断是否掉帧。系统对doFrame本身有实现，掉帧超过30帧则log: Choreographer: Skipped 60 frames!  The application may be doing too much work on its main thread

```
void doFrame(long frameTimeNanos, int frame) {
        final long startNanos;
        synchronized (mLock) {
            if (!mFrameScheduled) {
                return; // no work to do
            }

            if (DEBUG_JANK && mDebugPrintNextFrameTimeDelta) {
                mDebugPrintNextFrameTimeDelta = false;
                Log.d(TAG, "Frame time delta: "
                        + ((frameTimeNanos - mLastFrameTimeNanos) * 0.000001f) + " ms");
            }

            long intendedFrameTimeNanos = frameTimeNanos;
            startNanos = System.nanoTime();
            final long jitterNanos = startNanos - frameTimeNanos;
            if (jitterNanos >= mFrameIntervalNanos) {
                final long skippedFrames = jitterNanos / mFrameIntervalNanos;
                if (skippedFrames >= SKIPPED_FRAME_WARNING_LIMIT) {     // SKIPPED_FRAME_WARNING_LIMIT常量值，30
                    Log.i(TAG, "Skipped " + skippedFrames + " frames!  "
                            + "The application may be doing too much work on its main thread.");
                }
                final long lastFrameOffset = jitterNanos % mFrameIntervalNanos;
                if (DEBUG_JANK) {
                    Log.d(TAG, "Missed vsync by " + (jitterNanos * 0.000001f) + " ms "
                            + "which is more than the frame interval of "
                            + (mFrameIntervalNanos * 0.000001f) + " ms!  "
                            + "Skipping " + skippedFrames + " frames and setting frame "
                            + "time to " + (lastFrameOffset * 0.000001f) + " ms in the past.");
                }
                frameTimeNanos = startNanos - lastFrameOffset;
            }
        }

    }
```

- WebView优化

[webview秒开优化方案](https://juejin.cn/post/7016883220025180191#heading-8)。总结起来有：
  1. 全局 webview缩短内核初始化 (MutableContextWrapper可以改变context)，webview缓存池，公用模板，退出界面时清除界面数据，保留webview的模板样式。
  2. 通过 shouldInterceptRequest方法拦截 H5的请求加载，该方法返回WebResourceResponse，把通用的css、js文件放在客户端本地。
  3. 客户端并行请求数据，通过js接口返回给webview。

- 图片占用内存

  图片占用内存的大小主要取决于图片的像素数量和每个像素占用的位数。通常，不考虑图片压缩格式，每个像素占用的内存大小由颜色深度决定，例如：

   - ARGB_8888：每个像素4字节（32位，8位红色，8位绿色，8位蓝色，8位透明度）
   - RGB_565：每个像素2字节（16位，5位红色，6位绿色，5位蓝色）
   - ALPHA_8：颜色信息只由透明度组成，占8位
   - ARGB_4444：颜色信息由透明度与R（Red），G（Green），B（Blue）四部分组成，每个部分都占4位，总共占16位

例如图片的大小是 2592x1936 ，同时采用的位图配置（每个像素占用的位数）是 ARGB_8888 ，其在内存中需要的大小是 2592x1936x4字节，大概是 19MB。RGB_565则是 2592x1936x2字节 = 9.5M

[官方对图片加载处理的说明](https://developer.android.com/develop/ui/views/graphics)

JPG、JPEG、PNG、WEBP只是图片编码格式，影响的是图片存储大小。其中PNG是无损压缩

- Handler

  1. Handler的消息类型有：同步消息、异步消息、同步消息屏障三种，同步消息屏障其实是一个标志位，当设置同步消息屏障时，优先处理异步消息。屏障消息也有一个when字段，只能拦截其when之后的消息
  2. postDelay的实现是通过when关键字，死循环中取出when做比较，靠前的message插入队列头
  3. Handler的死循环可以保证UI线程不被回收
  4. HandlerThread是一个封装了Looper的Thread类，就是为了让我们在子线程里面更方便的使用Handler
  5. BlockCanary的原理，Handler在执行消息前后会通过一个Printer类打印日志，可以自定义Printer来获得消息执行前后的回调
  6. 设置同步屏障消息后，找到同步屏障消息（target为null），若没有找到则一直休眠，所以同步屏障消息使用后需要手动移除
  7. [Handler万文长字](https://juejin.cn/post/7016728431723282463#heading-10)

 
- 事件分发

  关于 dispatchTouchEvent和onInterceptTouchEvent的区别： [区别](https://stackoverflow.com/questions/9586032/android-difference-between-onintercepttouchevent-and-dispatchtouchevent)

  Activity、ViewGroup、View都有 dispatchTouchEvent和 onTouchEvent方法，ViewGroup多了一个onInterceptTouchEvent方法

  onTouch 和onTouchEvent 有什么区别: onTouch方式是View的OnTouchListener接口中定义的方法（单个View）。当一个View绑定了OnTouchListener后，当有Touch事件触发时，就会调用onTouch方法，onTouckEvent则不会调用

  ![image](https://github.com/EdisonLiao/PrepareAndroid/assets/14108176/a62ca054-a319-4d1c-968d-674c4298aa86)

  ![image](https://github.com/EdisonLiao/PrepareAndroid/assets/14108176/80725254-0447-4dd0-96c9-c2f280e1c50f)


- Serializable和Parcelable区别

两者最大的区别在于 存储媒介的不同，Serializable 使用 I/O 读写存储在硬盘上，而 Parcelable 是直接 在内存中读写。很明显，内存的读写速度通常大于 IO 读写，所以在 Android 中传递数据优先选择 Parcelable。
Serializable 会使用反射，序列化和反序列化过程需要大量 I/O 操作， Parcelable 自已实现封送和解封（marshalled &unmarshalled）操作不需要用反射，数据也存放在 Native 内存中，效率要快很多。

- 进程间通信（Bundle、AIDL、文件、ContentProvider、Socket、Messagener）

[AIDL使用及原理解释](https://zhuanlan.zhihu.com/p/338093696)、[Messagener使用](https://zhuanlan.zhihu.com/p/343230358)

ContentProvider数据的提供及修改者，ContentResolver数据的查询者，ContentObserver监听某个Uri变化

- ANR
[ANR治理](https://blog.csdn.net/SEU_Calvin/article/details/128484901?spm=1001.2014.3001.5502)

- 后台打开Activity的方案
  - fullScreenPendingIntent全面屏通知
  - 通过SystemService获取到ActivityManager,把对应包名调用moveTaskToFront
  - 获取系统权限，比如：SYSTEM_ALERT_WINDOW、Display over other apps
  - Hook Rom，比如小米：
 
    ```kotlin
    val method = intent.javaClass
                .getDeclaredMethod("addMiuiFlags", Int::class.javaPrimitiveType)
            method.invoke(intent, 0x2)
    ```

- ART和Dalvik的区别

ART采用的是AOT（Ahead-of-time）编译方式，在app安装的时候解析Dex，用dex2oat工具，解析为oat格式文件，加快app启动速度。Dalvik采用JIT（just-in-time）编译，app只有在打开的时候才会编译，dex编译为odex文件，app打开速度慢，但适合内存小的设备

  [文章](https://www.geeksforgeeks.org/difference-between-dalvik-and-art-in-android/)

- 两个Activity跳转的生命方法（都是standar模式的话）

一般情况下比如说有两个activity,分别叫A,B,当在A 里面激活B 组件的时候, A 会调用onPause()方法,然后B 调用onCreate() ,onStart(), onResume()。
这个时候B 覆盖了窗体, A 会调用onStop()方法. 如果B 是个透明的,或者是对话框的样式, 就不会调用A 的
onStop()方法

[屏幕旋转处理](https://blog.csdn.net/qq_16064871/article/details/46480525)

- Java动态代理

  通过InvokeHandler，hook接口的方法调用处，再通过Proxy.newInstance构造出接口示例化对象

- by lazy 和 lateinit

  bylazy默认是线程安全的（LazyThreadSafetyMode包括：SYNCHRONIZED（默认）、PUBLICATION、NONE），bylazy只有在第一次取值的时候初始化，变量用val修饰。lateinit用var修饰，可以多次赋值，不保证线程安全

- DOM、SAM、PULL解析
  DOM将整个XML文件加载到内存中，SAM和PULL逐行解析XML内容（PULL: 类似SAX，也是基于事件驱动的解析方式，但提供了类似于迭代器的接口，允许程序员通过迭代的方式逐步获取文档中的数据），android中使用的是PULL解析

- [Android异常处理流程](https://jiyang.site/posts/2019-11-22-java-%E5%92%8C-android-%E7%9A%84%E5%BC%82%E5%B8%B8%E5%A4%84%E7%90%86%E6%B5%81%E7%A8%8B/)

- LinkedHashMap（非线程安全）

  元素的顺序按照插入顺序（默认）或最近少使用顺序，get元素时，把对应元素移出插入队尾，队列头存放最早插入或者最近最少使用的元素，保持队尾是最新元素

- [深浅拷贝](https://blog.csdn.net/riemann_/article/details/87217229)

- [泛型](https://rengwuxian.com/kotlin-generics/)


  

  
  
 
 

