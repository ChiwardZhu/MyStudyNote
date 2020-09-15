```java
public class TestThread1 extends Thread {
    @Override
    public void run() {
        for (int i = 0; i < 200; i++) {
            System.out.println("执行run线程"+i);
        }
    }

    public static void main(String[] args) {
        TestThread1 testThread1 = new TestThread1();
//        testThread1.run(); 
        testThread1.start();
        for (int i = 0; i <2000 ; i++) {
            System.out.println("执行主线程"+i);
        }
    }
}
```

* 总结：
  1. 线程开启不一定立即执行，由CPU调度执行
  2. run();方法：run()方法只是类的一个普通方法而已，如果直接调用Run方法，程序中依然只有主线程这一个线程，其程序执行路径还是只有一条，还是要顺序执行，还是要等待run方法体执行完毕后才可继续执行下面的代码，这样就没有达到写线程的目的。
  3.  start();方法：用start方法来启动线程，真正实现了多线程运行，这时无需等待run方法体代码执行完毕而直接继续执行下面的代码。通过调用Thread类的 start()方法来启动一个线程，这时此线程处于就绪（可运行）状态，并没有运行，一旦得到cpu时间片，就开始执行run()方法，这里方法 run()称为线程体，它包含了要执行的这个线程的内容，Run方法运行结束，此线程随即终止。



1. 继承Thread类
   * 子类继承Thread类具备多线程能力
   * 启动线程：子类对象.start()
   * 不建议使用：避免OOP单继承局限性
2. 实现Runnable接口
   * 实现接口Runnable具有多线程能力
   * 启动线程：传入目标对象+Thread对象.start()
   * 推荐使用：避免单继承局限性，灵活方法，方便同一个对象被多个线程使用



```java
public class TestThread4 implements Runnable {
    private int ticknum=10;
    public void run() {
        while (ticknum>=0){
            System.out.println(Thread.currentThread().getName()+"抢到了第"+ticknum+"张票");
            ticknum--;
        }
    }

    public static void main(String[] args) {
        //实现类对象
        TestThread4 testThread4 = new TestThread4();
        //代理类对象
        new Thread(testThread4,"小明").start();
        new Thread(testThread4,"老师").start();
        new Thread(testThread4,"黄牛党").start();
    }
}
/*输出结果：
小明抢到了第10张票
小明抢到了第9张票
老师抢到了第10张票
老师抢到了第7张票
老师抢到了第6张票
老师抢到了第5张票
老师抢到了第4张票
老师抢到了第3张票
老师抢到了第2张票
老师抢到了第1张票
老师抢到了第0张票
小明抢到了第8张票
黄牛党抢到了第9张票
*/
```

