# 创建线程的方式

三种方式：

1. 实现Runnable接口的run方法
2. 继承Thread类并重写run方法
3. 使用FutureTask方式

## 继承Thread类并重写run方法

调用start方法线程没有马上执行而是处于就绪状态（获取了除CPU资源外的其他资源）

**优势**：在run() 方法内获取当前线程直接使用this 就可以了，无须使用Thread.currentThread() 方法； 

**劣势**：是Java 不支持多继承，如果继承了Thread 类，那么就不能再继承其他类；没有相关返回值。

## 实现Runnable接口的run方法

**优势**：可以继承其他类

**劣势**：没有返回值

## 使用FutureTask方式

```java
import java.util.concurrent.Callable;
import java.util.concurrent.ExecutionException;
import java.util.concurrent.FutureTask;

public class ThreadTest {
    //继承Thread类
    public static class MyThread extends Thread{
        @Override
        public void run(){
            System.out.println("I am a child thread");
        }
    }
	
    //继承Runnable接口
    public static class RunnableTask implements Runnable{

        @Override
        public void run() {
            System.out.println("I am a child thread");
        }
    }

    //创建任务，类似于Runnable
    public static class CallerTask implements Callable<String> {
        @Override
        public String call() throws Exception{
            return "hello";
        }
    }

    public static void main(String[] args){
        MyThread myThread=new MyThread();
        myThread.start();

        RunnableTask runnableTask=new RunnableTask();
        new Thread(runnableTask).start();
        new Thread(runnableTask).start();
		
        //创建异步任务
        FutureTask<String> futureTask=new FutureTask<>(new CallerTask());
        //启动线程
        new Thread(futureTask).start();
        try {
            //线程执行完成返回结果
            String result=futureTask.get();
            System.out.println(result);
        }catch (ExecutionException | InterruptedException e){
            e.printStackTrace();
        }
    }
}
```

