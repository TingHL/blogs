# 1. Java内部类

将一个类定义在另一个类里面或者一个方法里面，这种类称为内部类。分为：成员内部类、局部内部类、匿名内部类和静态内部类。

## 成员内部类

定义在一个类的内部，成员内部类可以无条件的访问外部类的所有成员属性和成员方法（包括private成员和静态成员）。（编译器在进行编译时，会将成员内部类单独编译成一个字节码文件，在文件中，增加了一个指向外部类对象的引用，指向外部的对象）

```java
//外部类 Circle
class Circle{
    private double radius=0;
    public static int count=1;
    public Circle(double radius){
        this.radius=radius;
    }
    
    //成员内部类
    class Draw{
        public void drawSahpe(){
            System.out.println(radius); //外部类的private成员
            System.out.println(count);  //外部类的静态成员
        }        
    }
}
```

当成员内部类拥有和外部类同名的成员变量或者方法时，会发生隐藏现象，即默认情况下访问的是成员内部类的成员。如果要访问外部类的同名成员，需要以下面的形式进行访问：

```java
外部类.this.成员变量
外部类.this.成员方法
```

成员内部类可以无条件地访问外部类的成员，而外部类想访问成员内部类的成员却不是这么随心所欲了。在外部类中如果要访问成员内部类的成员，必须先创建一个成员内部类的对象，再通过指向这个对象的引用来访问：

```java
class Circle {
    private double radius = 0;
 
    public Circle(double radius) {
        this.radius = radius;
        getDrawInstance().drawSahpe();   //必须先创建成员内部类的对象，再进行访问
    }
     
    private Draw getDrawInstance() {
        return new Draw();
    }
     
    class Draw {     //内部类
        public void drawSahpe() {
            System.out.println(radius);  //外部类的private成员
        }
    }
}
```

成员内部类是依附外部类而存在的，也就是说，如果要创建成员内部类的对象，前提是必须存在一个外部类的对象。创建成员内部类对象的一般方式如下：

```java
public class Test {
    public static void main(String[] args)  {
        //第一种方式：
        Outter outter = new Outter();
        Outter.Inner inner = outter.new Inner();  //必须通过Outter对象来创建
         
        //第二种方式：
        Outter.Inner inner1 = outter.getInnerInstance();
    }
}
 
class Outter {
    private Inner inner = null;
    public Outter() {
         
    }
     
    public Inner getInnerInstance() {
        if(inner == null)
            inner = new Inner();
        return inner;
    }
      
    class Inner {
        public Inner() {     
        }
    }
}
```



## 局部内部类

定义在一个方法或者一个作用域里面的类，**它和成员内部类的区别在于局部内部类的访问权限仅限于方法内或者该作用域内**。     

类似于方法里面的一个局部变量，不能有public、protected、private以及static修饰。

```java
class People{
    public People(){     
    }
}

class Man{
    public Man(){}
    
    public People getWonam(){
        class Woman extends People{  //局部内部类
            int age=0;
        }
        return new Woman();
    }
}
```

## 匿名内部类

匿名内部类也是不能有访问修饰符和static修饰符的。

匿名内部类是唯一一种没有构造器的类。正因为其没有构造器，所以匿名内部类的使用范围非常有限，大部分匿名内部类用于接口回调。匿名内部类在编译的时候由系统自动起名为`Outter$1.class`。一般来说，匿名内部类用于继承其他类或是实现接口，并不需要增加额外的方法，只是对继承方法的实现或是重写。

```java
scan_bt.setOnClickListener(new OnClickListener() {
             
            @Override
            public void onClick(View v) {
                // TODO Auto-generated method stub
                 
            }
        });
         
        history_bt.setOnClickListener(new OnClickListener() {
             
            @Override
            public void onClick(View v) {
                // TODO Auto-generated method stub
                 
            }
        });
```

其中，如下所示就是使用的匿名类：

```java
new OnClickListener() {             
            @Override
            public void onClick(View v) {
                // TODO Auto-generated method stub                 
            }
        }
```



## 静态内部类

定义在另一个类里面的类，只不过类的前面多了一个static关键字。静态内部类不需要依赖于外部类的，不能使用外部类的非static成员变量或者方法。

```java
public class Test{
    public static void main(String[] args){
        Outter.Inner inner=new Outter.Inner();
    }
}

class Outer{
    public Outter{
    }
    
    static class Inner{
        public Inner(){}
    }
}
```

## 其他

1. 局部内部类和匿名内部类只能访问局部final变量

   ```java
   public class Test{
       public static void main(String[] args){}
       
       public void test(final int b){
           final int a=10;
           new Thread(){
               public void run(){
                   System.out.println(a);
                   System.out.println(a);
               };
           }.start();
       }
   }
   ```

   

2. 每个内部类都能够独立的继承一个接口的实现，无论外部类是否已经继承了某个（接口的）实现，对于内部类都没有影响，内部类使得多继承的解决方案变得完整。

3. 创建静态内部类对象的一般形式为： 

   `外部类类名.内部类类名 xxx = new 外部类类名.内部类类名()`

   创建成员内部类对象的一般形式为： 

   `外部类类名.内部类类名 xxx = 外部类对象名.new 内部类类名()`


# 2.堆

通过`PriorityQueue`可以实现大顶堆和小顶堆

大顶堆：`PriorityQueue<>((x,y)->(y-x))`  //从最大的元素开始排序

小顶堆：`PriorityQueue<>()`  //从最小的元素开始排序 



























