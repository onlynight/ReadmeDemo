java中sleep()、wait()相同与不同详解
=================================

#相同

java中Thread#sleep和Object#wait方法都是暂停当前线程，当前线程让出CPU占用。并不存在调用sleep后还占用CPU，而调用wait不占用CPU这么一说，如果有看到类似的说法请自动过滤。sleep()和wait()还有一处相同，就是在哪个线程中调用即休眠哪个线程。

#不同

###1. sleep方法是Thread类的一个静态方法，而wait是Object的一个方法。
###2. 调用sleep后无法手动唤醒，调用sleep必须传入休眠时间，待休眠超时线程会自动唤醒；调用wait后可使用notify（notify同样是属于Object的方法）唤醒线程，同时wait也有带参数的方法，wait传入休眠时间后，待休眠超时线程将会被自动唤醒。
###3. 如果线程中使用了同步锁的情况下。sleep调用后如果未到休眠时间同步锁是不会释放的；wait必须在同步代码块中调用，wait调用后将会释放同步锁。

#解释说明

##1. 同步锁是什么，同步代码块是什么？

同步锁是什么？试想多台电脑链接一台打印机，如果三台调用同时打印该先给谁打印，或者说是同时给多台电脑打印？打印机是有限的资源，其工作原理也决定了他不能同时完成多想任务，所以打印机打印的时候必须有先后顺序，如果打印机正在使用的过程中有人请求打印，那么这个请求不能马上执行，当上一个打印任务完成后才能继续执行这个请求。这时候就诞生了同步锁的概念，我正在使用打印机的时候我把它锁起来，别人访问打印机时发现打印机被锁就只能等待；我使用完打印机后将打印机的锁打开，别人这时候访问打印机发现打印机没有锁那么他就可以使用打印机了。我们公共的部分是有一把锁，当我们要使用共享资源的时候先查看这把锁有没有锁，我们把这个锁称之为同步锁。

java中没有PV的概念，但是如果出现了多线程的情况也是有办法处理的，java为我们提供了synchronized关键字，当我们需要处理并发问题的时候就可以使用synchronized关键字来给对象或者类加锁。需要注意的是，只有当同步代码块中的代码执行完成之后同步锁才会被释放。

```java
// 给对象加锁1
Object obj = new Object();
synchronized (obj) {
    try {
        obj.wait();
    } catch (InterruptedException e) {
        e.printStackTrace();
    }
}

// 给对象加锁2
// this指针代表的是当前类的一个对象，所以对this加锁相当于对单个对象加锁，这个类的单个对象访问是互斥的，但是每个对象之间相互不影响。
synchronized (this){
	this.wait();
}

// 给类加锁1
// 对类加锁，这个类的所有对象访问都是互斥的，当有一个对象被占用时，其他对象都是不能访问的。
// synchronized 参数传入类名，表示对类加锁。
synchronized (Demo.class){
	Demo.class.wait();
}

// 给类加锁12
// 静态属性属于类而不属于对象，所以对类的静态属性加锁实际上是对类进行加锁操作。
// 对类的某一个静态属性加锁
synchronized (Demo.STATIC_VALUE){
}

```

##2. 栗子

```java
/**
 * Created by lion on 2017/2/5.
 */
public class ThreadDemo {

    public Float value = 1.23f;

    public static void main(String[] args) {
        demo1();
        demo2();
    }

    /**
     * demo1中验证{@see Thread#sleep(long)}使线程休眠后不影响同步锁的作用。
     */
    private static void demo1() {
        final ThreadDemo threadDemo = new ThreadDemo();

        System.out.println(Thread.currentThread().getName() + " value = " +
                threadDemo.value);

        new Thread(() -> {
            synchronized (threadDemo) {
                try {
                    Thread.sleep(2000);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                System.out.println(Thread.currentThread().getName() + " value = " +
                        threadDemo.value);
            }
        }).start();

        // 这里为了保证前一个线程比我们后一个线程先执行让当前线程休眠一下。
        try {
            Thread.sleep(1);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }

        new Thread(() -> {
            synchronized (threadDemo) {
                threadDemo.value = 2.34f;
                System.out.println(Thread.currentThread().getName() + " value = " +
                        threadDemo.value);
            }
        }).start();
    }

    /**
     * demo2验证{@see Object#wait()}调用后立即释放了释放了同步锁，
     * 并且演示了如果唤醒一个被wait的线程。
     */
    private static void demo2() {
        final ThreadDemo threadDemo = new ThreadDemo();

        System.out.println(Thread.currentThread().getName() + " value = " +
                threadDemo.value);

        new Thread(() -> {
            synchronized (threadDemo) {
                try {
                    threadDemo.wait();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                System.out.println(Thread.currentThread().getName() + " value = " +
                        threadDemo.value);
            }
        }).start();

        // 这里为了保证前一个线程比我们后一个线程先执行让当前线程休眠一下。
        try {
            Thread.sleep(1);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }

        new Thread(() -> {
            synchronized (threadDemo) {
                System.out.println(Thread.currentThread().getName() + " value = " +
                        threadDemo.value);
                threadDemo.notify();
            }
        }).start();
    }
}
```

demo1运行结果：

```java
main value = 1.23
Thread-0 value = 1.23
Thread-1 value = 2.34
```

从demo1的运行结果我们可以看到，Thread-1在等待Thread-0执行完成后才得以执行，这说明了sleep方法在同步代码块中不能释放同步锁。

demo2运行结果：

```java
main value = 1.23
Thread-1 value = 1.23
Thread-0 value = 1.23
```

从demo2的运行结果我们可以看到调用wait后同步锁被释放，因而Thread-1中的同步代码块才得以执行，Thread-1执行完成后再唤醒Thread-0让其执行。

