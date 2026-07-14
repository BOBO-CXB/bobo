# JUC

### 1、什么是JUC

```java
java.util.concurrent
```

### Runnable 没有返回值，效率相比Callable相对较低



### 2、线程和进程

#### 进程：一个运行的程序，动态，QQ.exe. Music.exe 程序的集合

#### 一个进程往往可以包含多个线程，至少包含一个线程

#### 线程：java默认有两个线程，main和GC，Thread，Runnable，Callable



### Java真的可以开启线程吗？无法开启，只可以通过调用本地方法

#### 本地方法，底层C++，Java无法直接操作硬件

```java
private native void start0();
```



### 3、并发和并行

并发：逻辑上的同时发生

- 指在同一时刻只能有一条指令执行，但多个进程指令被快速的轮换执行，使得在宏观上具有多个进程同时执行的效果，但在微观上并不是同时执行的，只是把时间分成若干段，使多个进程快速交替的执行。

并行：物理上的同时发生。CPU多核

- 指在同一时刻，有多条指令在多个处理器上同时执行；线程池



### 单核系统无法实现并行

​		在单核系统中，一个CPU一次只能执行一条指令，所以如果CPU是单核的，使用多线程或者多进程任务，这些任务其实是并发执行的，操作系统会不断的切换多个任务。

​		因此单核CPU是不能够实现并行的，虽然并发最终的结果可能和并行一样，但是真实的并行只可能出现在多核CPU的系统中。

```java
ackage com.yzu.code;

public class Demo_01 {
    public static void main(String[] args) {
        //获取CPU核数
        //CPU密集型，IO密集型
        System.out.println(Runtime.getRuntime().availableProcessors());
    }
}

```

### 并发编程的本质：充分利用CPU的资源



### 线程状态：

```java
   public enum State {
        /**
         * Thread state for a thread which has not yet started.
         */
       //新生
        NEW,

       //运行
        RUNNABLE,
   
       //阻塞
        BLOCKED,

       //等待
        WAITING,

       //超时等待
        TIMED_WAITING,

       //终止
        TERMINATED;
    }
```



### wait/sleep的区别：

- #### 来自不同的类------ wait===》Object，sleep===》Thread

- #### 关于锁的释放，wait会释放锁，sleep不会释放

- #### 使用的范围不同，wait只能在同步代码块中使用，sleep可以在任何地方睡

- #### 是否需要捕获异常，wait不需要捕获异常，sleep必须要捕获异常



### 3、Lock锁

> 传统Synchronized [ˈsɪŋkrənaɪzd]

```java
package com.yzu.code;


/*真正的多线程开发，公司中的开发，降低耦合性
  线程就是一个单独的资源类，没有任何附属的操作
  1、属性 2、方法
*/

public class Demo_02 {
    public static void main(String[] args) {
        //并发：多线程操作同一个资源类，把资源类丢入线程中
        Ticket ticket = new Ticket();

        //Lambda表达式 (参数)->{代码}
        new Thread(()->{
            for (int i = 0; i < 40; i++) {
                ticket.sale();
            }

        },"A").start();

        new Thread(()->{
            for (int i = 0; i < 40; i++) {
                ticket.sale();
            }
        },"B").start();

        new Thread(()->{
            for (int i = 0; i < 40; i++) {
                ticket.sale();
            }
        },"C").start();

    }
}


//资源类OOP
class Ticket{
    //属性、方法
    private int number = 30;

    //卖票的方式
    //synchronized本质，锁，队列
    public synchronized void sale(){
        if (number>0){
            System.out.println(Thread.currentThread().getName()+"卖出了第"+(number--)+"张票,剩余："+number);
        }
    }
}
```



> Lock接口

#### 实现类： 

- #### ReentrantLock 可重入锁（常用）

- #### ReentrantReadWriteLock.ReadLock 读锁

- #### ReentrantReadWriteLock.WriteLock 写锁



### ReentrantLock 可重入锁

#### 有两个构造方法

- 默认为构造非公平锁NonfairSync()
- 还有一个构造方法需要传入一个Boolean变量，true为公平锁FairSync()

```java
public ReentrantLock() {
        sync = new NonfairSync();
    }

    /**
     * Creates an instance of {@code ReentrantLock} with the
     * given fairness policy.
     *
     * @param fair {@code true} if this lock should use a fair ordering policy
     */
    public ReentrantLock(boolean fair) {
        sync = fair ? new FairSync() : new NonfairSync();
    }
```



公平锁FairSync()：十分公平，先来后到顺序执行

非公平锁NonfairSync()（默认）：十分不公平，可以插队

```java
package com.yzu.code;

import java.util.concurrent.locks.Lock;
import java.util.concurrent.locks.ReentrantLock;

public class Demo_03 {
    public static void main(String[] args) {
        //并发：多线程操作同一个资源类，把资源类丢入线程中
        System.out.println("========Lock========");
        Ticket2 ticket = new Ticket2();

        //Lambda表达式 (参数)->{代码}
        new Thread(()->{
            for (int i = 0; i < 40; i++) {
                ticket.sale();
            }

        },"A").start();

        new Thread(()->{
            for (int i = 0; i < 40; i++) {
                ticket.sale();
            }
        },"B").start();

        new Thread(()->{
            for (int i = 0; i < 40; i++) {
                ticket.sale();
            }
        },"C").start();

    }
}


//资源类OOP
//Lock锁
/*
* 1、首先新建一个锁new ReentrantLock();
* 2、lock.lock();加锁
* 3、try=》业务代码
* 4、finally=》lock.unlock();解锁
* */
class Ticket2{
    //属性、方法
    private int number = 30;

    Lock lock = new ReentrantLock();


    //卖票的方式

    public void sale(){

        lock.lock();//加锁

        try {
            //业务代码
            if (number>0){
                System.out.println(Thread.currentThread().getName()+"卖出了第"+(number--)+"张票,剩余："+number);
            }

        }catch (Exception e){

        }finally {
            lock.unlock();//解锁
        }

    }
}
```



> Synchronized 和 Lock 的区别

1、Synchronized是内置的关键字，而Lock是一个java类

2、Synchronized无法判断获取锁的状态，Lock可以判断是否获取到了锁

3、Synchronized会自动释放锁，Lock锁必须要手动释放锁，如果不释放锁，会发生死锁

4、Synchronized 线程1（获得锁，阻塞） 线程2（等待，傻傻的等）；Lock锁就不一定会等待下去lock.trylock

5、Synchronized锁是可重入锁，不可以中断，非公平，Lock锁，可重入锁，可以判断锁，可以设置公平锁和非公平锁

6、Synchronized适合锁少量的代码同步问题，Lock锁适合锁大量的同步代码





> 锁是什么，如何判断锁的是什么