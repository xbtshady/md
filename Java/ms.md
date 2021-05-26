# 面试题整理

## java 基础

#### Java是解释运行吗？

不正确！
1，Java源代码经过Javac编译成.class文件
2，.class文件经JVM解释或编译运行。
（1）解释:.class文件经过JVM内嵌的解析器解释执行。
（2）编译:存在JIT编译器（Just In Time Compile 即时编译器）把经常运行的代码作为"热点代码"编译与本地平台相关的机器码，并进行各种层次的优化。
（3）AOT编译器: Java 9提供的直接将所有代码编译成机器码执行。

#### 请对比 Exception 和 Error，另外，运行时异常与一般异常有什么区别？

- Exception 和 Error都继承了Throwable类，在java中只有Throwable类才可以被抛出(throw)和捕获(catch)

- Error通常是在正常情况下不会出现的情况，每当出现的时候都会导致程序处于非正常状态和不可恢复状态，不需要捕获
- Exception 分为可检查异常和不检查异常，可检查异常在代码里面必须显示的捕获，这是编译器检查的一部分，不检查异常就是所谓的运行时异常，可以在编码时选择性的捕获

#### NoClassDefFoundError 和 ClassNotFoundException 有什么区别

- NoClassDefFoundError是编译的时候找不到类，也有可能编译的时候存在，运行的时候却找不到了，也就是可能打包的时候漏了，或是jar包出现了损坏
- ClassNotFoundException是在使用Class.foName()类似方法是出现的可检查异常

#### 谈谈final、finally、 finalize有什么不同？

- final
  -  final用来修饰类禁止继承，修饰方法禁止重写，修饰变量禁止修改
  - final并不是不可变，final 只能约束对象的个引用不可以被赋值，但是 例如list对象行为不被 final 影响，添加元素等操作是完全正常的
  - 想要实现不可变需要做到
    - 将 class 自身声明为 final，这样别人就不能扩展来绕过限制了。
    - 将所有成员变量定义为 private 和 final，并且不要实现 setter 方法。
    - 通常构造对象时，成员变量使用深度拷贝来初始化，而不是直接赋值，这是一种防御措施，因为你无法确定输入对象不被其他人修改。
    - 如果确实需要实现 getter 方法，或者其他可能会返回内部状态的方法，使用 copy-on-write 原则，创建私有的 copy。
- finally
  - finally 则是 Java 保证重点代码一定要被执行的一种机制。我们可以使用 try-finally 或者 try-catch-finally 来进行类似关闭 JDBC 连接、保证 unlock 锁等动作。
- inalize 是基础类 java.lang.Object 的一个方法，它的设计目的是保证对象在被垃圾收集前完成特定资源的回收。finalize 机制现在已经不推荐使用，并且在 JDK 9 开始被标记为 deprecated。

#### 强引用、软引用、弱引用、幻象引用有什么区别？

- 强引用（“Strong” Reference），就是我们最常见的普通对象引用，只要还有强引用指向一个对象，就能表明对象还“活着”，垃圾收集器不会碰这种对象。对于一个普通的对象，如果没有其他的引用关系，只要超过了引用的作用域或者显式地将相应（强）引用赋值为 null，就是可以被垃圾收集的了，当然具体回收时机还是要看垃圾收集策略。

  ```java
  // 强引用
  String strongReference = new String("abc");
  ```

- 软引用（SoftReference），是一种相对强引用弱化一些的引用，可以让对象豁免一些垃圾收集，只有当 JVM 认为内存不足时，才会去试图回收软引用指向的对象。JVM 会确保在抛出 OutOfMemoryError 之前，清理软引用指向的对象。软引用通常用来实现内存敏感的缓存，如果还有空闲内存，就可以暂时保留缓存，当内存不足时清理掉，这样就保证了使用缓存的同时，不会耗尽内存。

  ```java
  // 软引用
  String str = new String("abc");
  SoftReference<String> softReference = new SoftReference<String>(str);
  ```

- 弱引用（WeakReference）并不能使对象豁免垃圾收集，仅仅是提供一种访问在弱引用状态下对象的途径。这就可以用来构建一种没有特定约束的关系，比如，维护一种非强制性的映射关系，如果试图获取时对象还在，就使用它，否则重现实例化。它同样是很多缓存实现的选择。每次JVM进行垃圾回收时，该对象都会被回收。

  ```java
  // 弱引用
  String str = new String("abc");
  WeakReference<String> weakReference  = new WeakReference<String>(str);
  ```

- 对于幻象引用，有时候也翻译成虚引用，你不能通过它访问对象。幻象引用仅仅是提供了一种确保对象被 finalize 以后，做某些事情的机制，比如，通常用来做所谓的 Post-Mortem 清理机制.

  ```java
  // 幻象引用
  String str = new String("abc");
  PhantomReference<String> phantomReference  = new PhantomReference<String>(str);
  ```

- 总结：

  - 代表软引用的类：java.lang.ref.SoftReference 代表弱引用的类：java.lang.ref.WeakReference 代表虚引用的类：java.lang.ref.PhantomReference 他们同时继承了：java.lang.ref.Reference

  - 所有引用类型，都是抽象类 java.lang.ref.Reference 的子类，它提供了 get()，除了幻象引用（因为 get 永远返回 null），如果对象还没有被销毁，都可以通过 get 方法获取原有对象。这意味着，利用软引用和弱引用，我们可以将访问到的对象，重新指向强引用，也就是人为的改变了对象的可达性状态！

  - 如果软引用所引用的对象被垃圾回收器回收，Java虚拟机就会把这个软引用加入到与之关联的引用队列中。后续，我们可以调用ReferenceQueue的poll()方法来检查是否有它所关心的对象被回收。如果队列为空，将返回一个null,否则该方法返回队列中前面的一个Reference对象

  - 图片缓存框架中，“内存缓存”中的图片是以这种软引用来保存，使得JVM在发生OOM之前，可以回收这部分缓存

  - 弱引用可以和一个引用队列（ReferenceQueue）联合使用，如果弱引用所引用的对象被垃圾回收，Java虚拟机就会把这个弱引用加入到与之关联的引用队列中

  - 幻象引用仅仅是提供了一种确保对象被 finalize 以后，做某些事情的机制。如果一个对象仅持有虚引用，那么它就和没有任何引用一样，在任何时候都可能被垃圾回收器回收。虚引用必须和引用队列 （ReferenceQueue）联合使用。当垃圾回收器准备回收一个对象时，如果发现它还有虚引用，就会在回收对象的内存之前，把这个虚引用加入到与之关联的引用队列中。

    ```java
    Object counter = new Object();
    ReferenceQueue refQueue = new ReferenceQueue<>();
    PhantomReference<Object> p = new PhantomReference<>(counter, refQueue);
    counter = null;
    System.gc();
    try {
        // Remove是一个阻塞方法，可以指定timeout，或者选择一直阻塞
        Reference<Object> ref = refQueue.remove(1000L);
        if (ref != null) {
            // do something
            //可用来跟踪对象被垃圾回收器回收的活动，当一个虚引用关联的对象被垃圾收集器回收之前会收到一条系统通知。
        }
    } catch (InterruptedException e) {
        // Handle it
    }
    ```

  - 在静态内部类中，经常会使用虚引用。例如，一个类发送网络请求，承担callback的静态内部类，则常以虚引用的方式来保存外部类(宿主类)的引用，当外部类需要被JVM回收时，不会因为网络请求没有及时回来，导致外部类不能被回收，引起内存泄漏

#### String、StringBuffer、StringBuilder有什么区别？

- String 是 Java 语言非常基础和重要的类，提供了构造和管理字符串的各种基本逻辑。它是典型的 Immutable 类，被声明成为 final class，所有属性也都是 final 的。也由于它的不可变性，类似拼接、裁剪字符串等动作，都会产生新的 String 对象。由于字符串操作的普遍性，所以相关操作的效率往往对应用性能有明显影响。
- StringBuffer 是为解决上面提到拼接产生太多中间对象的问题而提供的一个类，我们可以用 append 或者 add 方法，把字符串添加到已有序列的末尾或者指定位置。StringBuffer 本质是一个线程安全的可修改字符序列，它保证了线程安全，也随之带来了额外的性能开销，
- StringBuilder 是 Java 1.5 中新增的，在能力上和 StringBuffer 没有本质区别，但是它去掉了线程安全的部分，有效减小了开销，是绝大部分情况下进行字符串拼接的首选。
- 扩展
  - StringBuffer的线程安全是通过所有的方法加上synchronized实现的
  - 为了实现修改字符序列的目的，StringBuffer 和 StringBuilder 底层都是利用可修改的（char，JDK 9 以后是 byte）数组
  - StringBuffer 和 StringBuilder 构建时初始字符串长度加 16（这意味着，如果没有构建对象时输入最初的字符串，那么初始值就是 16),我们如果确定拼接会发生非常多次，而且大概是可预计的，那么就可以指定合适的大小，避免很多次扩容的开销。扩容会产生多重开销，因为要抛弃原有数组，创建新的（可以简单认为是倍数）数组，还要进行 arraycopy。
  - 非静态的拼接逻辑在 JDK 8 中会自动被 javac 转换为 StringBuilder 操作；而在 JDK 9 里面，则是体现了思路的变化。Java 9 利用 InvokeDynamic，将字符串拼接的优化与 javac 生成的字节码解耦，假设未来 JVM 增强相关运行时实现，将不需要依赖 javac 的任何修改。
  - String 在 Java 6 以后提供了 intern() 方法，目的是提示 JVM 把相应字符串缓存起来，以备重复使用。
  - Java 6中被缓存的字符串是存在所谓 PermGen (永久代)里的，在后续版本中，这个缓存被放置在堆中，这样就极大避免了永久代占满的问题，甚至永久代在 JDK 8 中被 MetaSpace（元数据区）替代了。
  - 幸好在 Oracle JDK 8u20 之后，推出了一个新的特性，也就是 G1 GC 下的字符串排重。它是通过将相同数据的字符串指向同一份数据来做到的，是 JVM 底层的改变，并不需要 Java 类库做什么修改。也就是不需要在使用 intern() 方法了，在G1 GC 下的字符串排重中，需要使用参数开启-XX:+UseStringDeduplication
  - 在运行时，字符串的一些基础操作会直接利用 JVM 内部的 Intrinsic 机制，往往运行的就是特殊优化的本地代码，而根本就不是 Java 代码生成的字节码。Intrinsic 可以简单理解为，是一种利用 native 方式 hard-coded 的逻辑，算是一种特别的内联，很多优化还是需要直接使用特定的 CPU 指令
  - Java 的String，在历史版本中，它是使用 char 数组来存数据的，这样非常直接。但是 Java 中的 char 是两个 bytes 大小，拉丁语系语言的字符，根本就不需要太宽的 char，这样无区别的实现就造成了一定的浪费。在 Java 9 中，我们引入了 Compact Strings 的设计，对字符串进行了大刀阔斧的改进。将数据存储方式从 char 数组，改变为一个 byte 数组加上一个标识编码的所谓 coder，并且将相关字符串操作类都进行了修改。另外，所有相关的 Intrinsic 之类也都进行了重写，以保证没有任何性能损失。

















