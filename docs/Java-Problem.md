# 记录常见的一些java问题

1. Switch能否用string做参数?
    * 在jdk 7 之前，switch 只能支持 byte、short、char、int 这几个基本数据类型和其对应的封装类型。
switch后面的括号里面只能放int类型的值，但由于byte，short，char类型，它们会自动 转换为int类型（精精度小的向大的转化），所以它们也支持。
对于精度比int大的类型，比如long、float，doulble，不会自动转换为int，如果想使用，就必须强转为int，如(int)float;
    * jdk1.7后，整形，枚举类型，boolean，字符串都可以。
    * jdk1.7并没有新的指令来处理switch string，而是通过调用switch中string.hashCode,将string转换为int从而进行判断。
- - - -
2. Object有哪些公用方法？
    * Object是所有类的父类，任何类都默认继承Object。
    * clone()     保护方法，实现对象的浅复制，只有实现了Cloneable接口才可以调用该方法，否则抛出CloneNotSupportedException异常
    * equals()    在Object中与==是一样的，子类一般需要重写该方法
    * hashCode()  该方法用于哈希查找，重写了equals方法一般都要重写hashCode方法。这个方法在一些具有哈希功能的Collection中用到
    * getClass()  final方法，获得运行时类型
    * wait()      使当前线程等待该对象的锁，当前线程必须是该对象的拥有者，也就是具有该对象的锁。wait()方法一直等待，直到获得锁或者被中断。
    * wait(long timeout)
            设定一个超时间隔，如果在规定时间内没有获得锁就返回。 
            调用该方法后当前线程进入睡眠状态，直到以下事件发生： 
                1. 其他线程调用了该对象的notify方法 
                2. 其他线程调用了该对象的notifyAll方法 
                3. 其他线程调用了interrupt中断该线程 
                4. 时间间隔到了 
            此时该线程就可以被调度了，如果是被中断的话就抛出一个InterruptedException异常
    * notify()    唤醒在该对象上等待的某个线程 
    * notifyAll() 唤醒在该对象上等待的所有线程
    * toString()  转换成字符串，一般子类都有重写，否则打印句柄
3. 进程和线程的区别?
    * 进程和线程都是一个时间段的描述，是CPU工作时间段的描述，不过是颗粒大小不同。
    * 进程是cpu资源分配的最小单位，线程是cpu调度的最小单位。
    * 以前进程既是资源分配也是调度的最小单位，后来为了更合理的使用cpu(实际上是cpu性能越来越好)，才将资源分配和调度分开，就有了线程。
    * 线程是建立在进程的基础上的一次程序运行单位。
4. 有了Thread为什么还要runnable?
    * 避免点继承的局限，一个类可以继承多个接口。
    * 一个类继承Thread，不适合多个线程共享资源，实现Runnable接口，可以方便实现资源共享
因为一个线程只能启动一次，通过Thread实现线程时，线程和线程所要执行的任务是捆绑在一起的。
也就使得一个任务只能启动一个线程，不同的线程执行的任务是不相同的，所以没有必要，也不能让两个线程共享彼此任务中的资源。
一个任务可以启动多个线程，通过Runnable方式实现的线程，实际是开辟一个线程，将任务传递进去，由此线程执行。
可以实例化多个 Thread对象，将同一任务传递进去，也就是一个任务可以启动多个线程来执行它。这些线程执行的是同一个任务，所以他们的资源是共享。
    * 两种不同的线程实现方式本身就决定了其是否能进行资源共享。
5. wait()和sleep()的区别?
    * 对于sleep()方法，我们首先要知道该方法是属于Thread类中的。而wait()方法，则是属于Object类中的。
    * sleep()方法导致了程序暂停执行指定的时间，让出cpu该其他线程，但是他的监控状态依然保持者，当指定的时间到了又会自动恢复运行状态。
    * 在调用sleep()方法的过程中，线程不会释放对象锁。
    * 而当调用wait()方法的时候，线程会放弃对象锁，进入等待此对象的等待锁定池，只有针对此对象调用notify()方法后本线程才进入对象锁定池准备获取对象锁进入运行状态。
6. foreach与正常for循环效率对比？
    * 直接for循环效率最高，其次是迭代器和 ForEach操作。 作为语法糖，其实 ForEach 编译成 字节码之后，使用的是迭代器实现的。
    * 循环ArrayList时，普通for循环比foreach循环花费的时间要少一点；循环LinkList时，普通for循环比foreach循环花费的时间要多很多。 
    * 需要循环数组结构的数据时，建议使用普通for循环，因为for循环采用下标访问，对于数组结构的数据来说，采用下标访问比较好。 
    * 需要循环链表结构的数据时，一定不要使用普通for循环，这种做法很糟糕，数据量大的时候有可能会导致系统崩溃。    
7. 简述RuntimeException、和checkedException、Error三者的关系？
    * Exception分两种，checked和unchecked。checked Exception就是在写代码的时候，IDE(比如Eclipse)会要求你写try catch的那种Exception，比如IOException。
    * 这种Exception是Java的设计者要求你的程序去处理的。
    * 这种异常一般不会影响程序的主体，容易手动诊断修复，所以Java要求你在catch下面写出处理的代码，以保证程序遇到此类exception之后还可以正常运行。
    * unchecked这一类就是你在代码处理了checked exception之后，你在运行时候依然会遇到的exception，所以又叫做RunTimeException，比如NullPointerException, IndexOutOfBoundsException。
    * 此类exception相较于前面那种更容易影响程序运行，从设计者角度不提倡从程序中catch出来并处理，当然你也可以这么做。
    * Error就是程序运行时候抛出的最严重级别的错误了，如VirtualMachineError，ThreadDeath。抛出了Error的程序从Java设计的角度来讲，程序基本不可以通过后续代码修复，从而理应终止。
    * 当然，从语法上来讲，所有这些都可以被写进catch里面，但是Error因为上述原因，不应该被代码处理。
8. 第一范式:(1NF)无重复的列 第二范式:(2NF)属性完全依赖于主键 第三范式:(3NF)属性不依赖于其它非主属性 
