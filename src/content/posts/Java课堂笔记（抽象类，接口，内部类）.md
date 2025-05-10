---
title: Java笔记
published: 2025-03-23
updated: 2025-03-23
description: '抽象类，接口，内部类'
image: ''
tags: [Java]
category: 'Java'
draft: false 
---

# Java笔记



## 抽象类

### 使用场景

当父类方法不确定的时候，需要声明但是不知道该如何实现的时候，可以将其声明为抽象方法，这个类就是抽象类

```java
class Animal {
    private String name;
    
    public Animal(String name) {
        this.name = name;
    }
    
    public void eat() {
        System.out.println("吃一个...");
    }
}
```

这个时候可以将该方法设计为抽象方法，所谓抽象方法就是没有实现的方法（没有方法体）

```java
abstract class Animal {
    private String name;

    public Animal(String name) {
        this.name = name;
    }

    public abstract void eat();
}
```

当将一个类中的方法设置为抽象方法的时候，要将类也声明为抽象类

使用方法

```java
访问修饰符 abstract 返回类型 方法名（）;
```

再用子类继承实现这些抽象方法

```java
class Dog extends Animal {
    public Dog(String name) {
        super(name);
    }

    public void eat() {
        System.out.println("Dog eat");
    }
}
```

抽象类的价值更多的是在于设计，在设计者设计好后，让子类继承并实现

### 抽象类细节

1.抽象类不能够被实例化

2.抽象类不一定要包含抽象方法

3.抽象方法所在的类必须用abstract修饰

4.abstract只能修饰类和方法

5.抽象类可以有任意成员，非抽象方法，构造器，静态属性

6.抽象方法不能有主体

7.如果一个类继承了抽象类，则这个类必须要实现所有的抽象方法，除非他自己也声明为abstract类

8.抽象方法不能够用private final static修饰，因为和重写实现型成悖论



### 模板设计模式

抽象类的实践

需求

1.有多个类，完成不同的任务

2.要求能够统计得到各自完成的时间

3.编程实现

先使用不同的类实现

```java
package com.npu.abstract_;

public class Abstract {
    public static void main(String[] args) {
        new AA().job();
        new BB().job();
    }
}

class AA {
    public void job() {
        long start = System.currentTimeMillis();
        long num = 0;
        for (long i = 0; i < 800000; i++) {
            num += i;
        }
        long end = System.currentTimeMillis();
        System.out.println(num);
        System.out.println(end - start);
    }
}

class BB {
    public void job() {
        long start = System.currentTimeMillis();
        long num = 0;
        for (long i = 0; i < 800000; i++) {
            num += i / 2;
        }
        long end = System.currentTimeMillis();
        System.out.println(num);
        System.out.println(end - start);
    }
}
```

可以将公共的部分提取出来

```java
package com.npu.abstract_;

public class Abstract {
    public static void main(String[] args) {
        new AA().calulateTime();
        new BB().calulateTime();
    }
}

class AA {
    public void calulateTime() {
        long start = System.currentTimeMillis();
        job();
        long end = System.currentTimeMillis();
        System.out.println((end - start));
    }

    public void job() {
        long num = 0;
        for (long i = 0; i < 800000; i++) {
            num += i;
        }
        long end = System.currentTimeMillis();
        System.out.println(num);
    }
}

class BB {
    public void calulateTime() {
        long start = System.currentTimeMillis();
        job();
        long end = System.currentTimeMillis();
        System.out.println((end - start));
    }

    public void job() {
        long num = 0;
        for (long i = 0; i < 800000; i++) {
            num += i / 2;
        }
        long end = System.currentTimeMillis();
        System.out.println(num);
    }
}
```

如果说加上了C在C类中还是必须要实现calulateTime方法，所以可以将job写为一个抽象方法，calulateTime在抽象类里实现，用各个子类继承实现job即可

```java
package com.npu.abstract_;

public class Abstract {
    public static void main(String[] args) {
        new AA().calulateTime();
        new BB().calulateTime();
    }
}

abstract class DD {
    public void calulateTime() {
        long start = System.currentTimeMillis();
        job();
        long end = System.currentTimeMillis();
        System.out.println((end - start));
    }

    public abstract void job();
}

class AA extends DD {
    public void job() {
        long num = 0;
        for (long i = 0; i < 800000; i++) {
            num += i;
        }
        long end = System.currentTimeMillis();
        System.out.println(num);
    }
}

class BB extends DD {
    public void job() {
        long num = 0;
        for (long i = 0; i < 800000; i++) {
            num += i / 2;
        }
        long end = System.currentTimeMillis();
        System.out.println(num);
    }
}
```

在这里运用到了动态绑定机制，job调用的是子类的具体实现

运用到模板设计模式，也已大幅度提高代码的复用性，简易性

## 接口

### 快速入门

现实中使用充电器，usb接口，都有着统一的尺寸规定，方便用户使用，这种思路在语言编程中也有对应使用

可以用以下代码模拟

有如下接口

```java
public interface UsbInterface {
    //规定接口的相关方法
    public void start();
    public void stop();
}
```

手机实现了接口

```java
package com.npu.interface_;
//实现接口
public class Phone implements UsbInterface{
    public void start() {
        System.out.println("Phone start");
    }
    public void stop() {
        System.out.println("Phone stop");
    }
}
```

相机实现了接口

```java
package com.npu.interface_;

public class Camera implements UsbInterface{
    public void start(){
        System.out.println("Camera start");
    }
    public void stop(){
        System.out.println("Camera stop");
    }
}
```

电脑使用接口

```java
package com.npu.interface_;

public class Computer {
    public void work(UsbInterface usbInterface) {
        //通过接口调用接口方法
        usbInterface.start();
        usbInterface.stop();
    }
}
```

就可以传入接口，使用接口实现的类的接口实现方法

```java
package com.npu.interface_;

public class Interface {
    public static void main(String[] args) {
        Camera camera = new Camera();
        Phone phone = new Phone();
        Computer computer = new Computer();
        computer.work(phone);
    }
}
```

代码中可以通过computer的work传入phone，调用phone的方法

### 基本介绍

接口就是给出一些没有实现的方法，封装在一起，在某个类要使用的时候，再根据具体的情况把这些方法写出来，语法如下：

```java
interface 接口名 {
	//属性
	//方法
}

class 类名 implements 接口名 {
	//属性
	//方法
	//必须实现的接口抽象方法
}
```

注意：在jdk7之前的接口中没有方法体，都是抽象方法，而在jdk8之后接口可以有静态方法，默认方法，也就是接口中可以有方法的具体实现

```java
package com.npu.interface_;

public interface AInterface {
    public int num = 0;
    public void hi();//在接口中不需要加abstract，默认为抽象方法
    public default void lo() {//默认的方法实现，使用default
        System.out.println(1);
    }
    public static void test() {
        System.out.println("test");
    }
}

class A implements AInterface {
    @Override
    public void hi() {
        System.out.println("hi");
    }
}
```

### 接口的使用场景

1.在设计飞机时，专家规定飞机需要实现的功能/规格即可，让别人具体实现

2.项目经理，管理三个程序员，功能开发一个软件，为了控制和管理软件，项目经理可以定义一些接口，然后由程序员具体实现

例如：3个程序员编写三各类，分别完成对于MySQL，Oracle，DB2数据库的 connect & close的定义，可以分别对三个数据库规定三个接口，三个程序员就必须要统一连接和关闭的方法名，接口也可以识别connect & close对应的是哪一个接口

接口的使用细节

1.接口不能够被实例化

2.接口中所有的方法是public方法，接口中的抽象方法可以不用abstract修饰方法也可以不用public修饰

3.一个普通类实现接口，就必须将接口中的方法都实现

4.抽象类实现接口，可以不用实现接口的方法

5.一个类可以同时实现多个接口（类的继承不允许继承多个类）

```java
class A implements IB, IC {
    //实现IB接口
    //实现IC接口
}
```

6.接口中的属性只能是final的，而且是public static final 修饰符，比如

```java
int a = 1;
//实际上是 public static final int a = 1;
```

7.接口中属性的访问形式：

```java
接口名.属性名
```

8.一个接口不能继承其他的类，但是可以继承多个别的接口

```java
interface A extends B, C {}
```

9.接口的修饰符只能够是public和默认，和类的修饰符同理

一个课堂练习，以下三种方式均可以成功访问

```java
package com.npu.interface_;

import com.sun.xml.internal.ws.api.model.wsdl.WSDLOutput;

public class AInterface {
    public static void main(String[] args) {
        A a = new A();
        System.out.println(a.num);//通过实例对象引用接口中的成员
        System.out.println(A.num);//通过类使用接口静态成员
        System.out.println(InterfaceTest.num);//通过接口使用静态成员
    }
}
interface InterfaceTest {
    int num = 1;
}

class A implements InterfaceTest {

}
```

### 接口VS继承

接口在Java中算单继承的补充，例如一只小猴，对于猴类来说，猴子天生就会爬树，这算是继承，但是如果要学会游泳，这只猴子就需要去学习游泳（实现游泳接口的功能）

接口比继承更加灵活，继承是is — a 的关系，而接口只需要满足 like — a 的关系

接口在一定程度上实现代码的解耦（接口的规范性 + 动态绑定机制）在后续讲解源码的时候会有所涉及

### 接口的多态特性

1.多态参数

例如在前面涉及到的入门，电脑在调用接口参数的时候，这个参数可以接受实现了这个接口的对象实例

```java
package com.npu.interface_;

public class Interface {
    public static void main(String[] args) {
        Camera camera = new Camera();
        Phone phone = new Phone();
        Computer computer = new Computer();
        computer.work(phone);//手机和相机都实现了work中参数接口
        computer.work(camera);
    }
}
```

另外一个例子

```java
package com.npu.interface_;

import com.sun.xml.internal.ws.api.model.wsdl.WSDLOutput;

public class AInterface {
    public static void main(String[] args) {
        InterfaceTest iface = new A();
        InterfaceTest iface2 = new B();
    }
}
interface InterfaceTest {}
class A implements InterfaceTest {}
class B implements InterfaceTest {}
```

接口类型应用变量可以指向实现了接口的对象实例，这个和继承多态类似（向下转型）



2.多态数组

同继承的多态，我们可以使用向下转型，使用运行类型的方法

```java
package com.npu.interface_;

public class AInterface {
    public static void main(String[] args) {
        USBInterface[] usbInterfaces = new USBInterface[3];
        usbInterfaces[0] = new PhoneA();
        usbInterfaces[1] = new PhoneB();
        usbInterfaces[2] = new PhoneA();
        for (int i = 0; i < usbInterfaces.length; i++) {
            usbInterfaces[i].start();
            if (usbInterfaces[i] instanceof PhoneA) {
                ((PhoneA) usbInterfaces[i]).call();
            }
        }
    }
}
interface USBInterface {
    void start();
}

class PhoneA implements USBInterface {
    public void start() {
        System.out.println("Phone A start");
    }

    public void call() {
        System.out.println("Phone A call");
    }
}

class PhoneB implements USBInterface {
    public void start() {
        System.out.println("Phone B start");
    }
}
```

3.接口的多态传递

```java
package com.npu.interfacearr;

public class InterfacePoly {
    IG ig = new Teacher();//接口类型可以指向实现接口的实例对象
    IH ih = new Teacher();//因为IG继承于IH，所以teacher也实现了IG的接口
}

interface IH {
    void teach();
}
interface IG extends IH { }

class Teacher implements IG {
    //teacher也需要实现IG继承的IH接口
    public void teach() {
        System.out.println("Teacher");
    }
}
```



## 内部类

一个类的内部内嵌套了另外的一个类，内部的类称作内部类，是类的五大成员之一：属性，方法， 构造器，代码块，内部类

```java
class A {
	class Inner {
		//内部类	
	}
}

class B {
	//其他类
}
```

内部类的最大特点是可以直接访问私有属性，并且可以体现出类与类之间的包含关系，底层源码有大量的内部类

有四种内部类

第一类：定义在外部类局部上（比如方法内）

1.局部内部类

2.匿名内部类



第二类：定义在外部类的成员位置上

3.成员内部类（没用static修饰）

4.静态内部类（使用static修饰）



### 局部内部类

1.可以直接访问外部类的所有成员

```java
class Outer {
    private int n1 = 100;
    public Outer(int n1) {
        this.n1 = n1;
    }

    {
        System.out.println("代码块");
    }

    public void teach() {
        System.out.println(n1 + 1);
    }

    public void Print() {
        class Inner {//内部类
            public void function() {
                System.out.println(n1);//访问外部类属性
                teach();//访问外部类的方法
            }
        }
        System.out.println(n1);
    }
}
```

2.不能添加访问修饰符，因为它的地位是一个局部变量，但是可以使用final修饰，内部类可以被类继承



3.作用域仅限于定义它的方法或代码块中



4.局部内部类访问外部类成员方式：直接访问



5外部类成员访问内部类方式：创建对象，再进行访问

```java
package com.npu.inner;

public class Main {//其他类
    public static void main(String[] args) {
        Outer outer = new Outer(1);
        outer.Print();
    }
}

class Outer {
    private int n1 = 100;
    public Outer(int n1) {
        this.n1 = n1;
    }

    {
        System.out.println("代码块");
    }

    public void teach() {
        System.out.println(n1 + 1);
    }


    public void Print() {
        class Inner {//内部类
            public void function() {
                System.out.println(n1);
                teach();
            }
        }
        System.out.println(n1);
        Inner inner = new Inner();//先创建对象
        inner.function();//在进行调用
    }
}
```

6.外部其他类不可以访问内部类

7.如果外部类和内部类的成员重名的时候，默认遵循就近原则，如果想访问外部类的成员可以使用 外部类名.this.成员 访问

```java
package com.npu.inner;

public class Main {//其他类
    public static void main(String[] args) {
        Outer outer = new Outer();
        outer.Print();
    }
}

class Outer {
    private int n1 = 100;
    {
        System.out.println("代码块");
    }

    public void Print() {
        class Inner {//内部类
            private int n1 = 200;
            public void function() {
                System.out.println(n1);//200，就近原则
                System.out.println(Outer.this.n1);//100，Outer.this本质是指的是Outer类的对象实例
            }
        }
        Inner inner = new Inner();
        inner.function();
    }
}
```

### 匿名内部类（重要！！）

本质是一个类，是一个内部类，是匿名的，同时还是一个对象



#### 个人理解：

匿名内部类就是一个渣男，使用的时候创建了这样的一个内部类，这个内部类就会跑路，再也没有办法使用，还留下了一个孩子（匿名内部类的实例）。当然，孩子是有用的，可以调用匿名内部类实现的一切方法，通过这样就可以不用额外的创建一个新的类，极大的简化了代码



基本语法

```java
new 类或接口 (参数列表) {
	类体
};
```



代码示例：传统上而言 写一个类实现接口，创建对象，在主方法中创建类的对象完成接口创建Tiger实例与调用

```java
package com.npu.inner;

public class Anonymous {
    public static void main(String[] args) {
        Outer04 outer04 = new Outer04();
        outer04.method();
    }
}

class Outer04 {//外部类
    private int n1 = 10;
    public void method() {
        //基于接口的匿名内部类
        //需求分析：想使用IA接口，并创建对象
        //传统上：写一个类实现接口，创建对象
        IA tiger = new Tiger();
        tiger.cry();
    }
}

interface IA {//接口
    void cry();
}

class Tiger implements IA{//一个类
    public void cry() {
        System.out.println("Tiger 叫");
    }
}
```

#### 使用匿名内部类实现接口

不想创建类，只想单独一次实现接口，并使用方法，可以使用匿名内部类简化（这个匿名内部类不能被使用第二次）

```java
package com.npu.inner;

public class Anonymous {
    public static void main(String[] args) {
        Outer04 outer04 = new Outer04();
        outer04.method();
    }
}

class Outer04 {//外部类
    private int n1 = 10;
    public void method() {
        //基于接口的匿名内部类
        //需求分析：想使用IA接口，并创建对象
        IA tiger = new IA() {
            public void cry() {
                System.out.println("tiger 叫");
            }
        };
        tiger.cry();
    }
}

interface IA {//接口
    void cry();
}
```

在这个例子中，tiger的编译类型是IA接口类型，而tiger的运行类型则是由底层系统分配名称的匿名内部类，这个类实现了接口，并立即创建实例，可以通过实例访问接口方法

可以用tiger.getClass() 验证

运行结构

```
tiger 叫
运行类型为：class com.npu.inner.Outer04$1
```

#### 使用匿名内部类重写一个类方法

```java
package com.npu.inner;

public class Anonymous {
    public static void main(String[] args) {
        Outer04 outer04 = new Outer04();
        outer04.method();
    }
}

class Outer04 {
    private int n1 = 10;
    public void method() {
        Father father = new Father("jack"){
            //jack的参数会被传递给Father的构造器！！
            int age = 19;
            @Override
            public void Print() {
                System.out.println(this.name+ " 年龄是 " + age);
            }
        };
        father.Print();
        System.out.println("运行类型为：" + father.getClass());
    }
}

class Father {
    String name;
    Father(String name) {
        this.name = name;
    }

    public void Print() {
        System.out.println(this.name);
    }
}
```



#### 基于抽象类的匿名内部类

```java
package com.npu.inner;

public class Anonymous {
    public static void main(String[] args) {
        Outer04 outer04 = new Outer04();
        outer04.method();
    }
}

class Outer04 {
    private int n1 = 10;
    public void method() {
        Animal animal = new Animal(){
            public void eat(){
                System.out.println("吃");
            }
        };
        animal.eat();
        System.out.println("运行类型为：" + animal.getClass());
    }
}

abstract class Animal {
    abstract void eat();
}
```

#### 匿名内部类的使用细节

1.内部类创建完的结果是一个实例对象

我们要访问匿名内部类的方法可以通过这个实例对象直接用.调用方法，或者可以先用一个接口，抽象类，类的引用接收，再通过这个引用调用运行类型为匿名内部类的类方法

2.可以直接访问外部类的所有成员，包含私有的

3.不能添加访问修饰符，因为是一个局部变量

4.作用域位于处于的方法或者代码块

5.如果外部类和匿名内部类成员重名的时候，就近访问内部类的成员，要使用外部类的同名成员的时候需要使用 外部类名.this.成员名

外部类名.this指的是调用method()的对象

### 匿名内部类使用场景

1.当作实参直接传递，简洁高效

```java
package com.npu.inner;

public class Test {
    public static void main(String[] args) {
        f1(new IL(){
           @Override
           public void show() {
               System.out.println("鸡哥");
           }
        });
    }
    //静态方法
    public static void f1(IL il){
        il.show();
    }
}

interface IL{
    void show();
}
```

传统上需要先实现一个实现了接口的类，再将类实例化，将实例传入f1，根据运行类型调用方法

一个代码示例

```java
package com.npu.inner;

public class Test {
    public static void main(String[] args) {
        Cellphone cellphone = new Cellphone();

        cellphone.alarmclock(new Bell() {
            @Override
            public void ring() {
                System.out.println("ring1");
            }
        });

        cellphone.alarmclock(new Bell() {
            @Override
            public void ring() {
                System.out.println("ring2");
            }
        });
    }
}

interface Bell {
    void ring();
}

class Cellphone {
    public void alarmclock (Bell bell) {
        bell.ring();
    }
}
```



### 成员内部类

成员内部类定义在外部类的成员位置

1.内部类可以访问外部类的所有成员，包含私有的

例如：

```java
package com.npu.inner;

public class Member {
    public static void main(String[] args) {
        Outer08 outer = new Outer08();
        outer.inner.Print();
    }
}

class Outer08 {
    private int n1 = 200;
    Inner inner = new Inner();
    {
        System.out.println("代码块");
    }
    class Inner {
        public void Print() {
            System.out.println(n1);
        }
    }
}
```

2.成员内部类可以用访问修饰符修饰，因为它的地位是类的成员

3.作用域为整个类体中

4.访问同名成员的时候会遵循就近原则，要访问外部类的成员要使用 外部类名.this.重名成员 来进行访问

5.外部其他类可以有如下的两种方式访问成员内部类



1 使用外部类对象 通过new来创建一个成员内部类对象

```java
package com.npu.inner;

public class Member {//其他类
    public static void main(String[] args) {
        Outer08 outer = new Outer08();
        Outer08.Inner inner = outer.new Inner();
    }
}

class Outer08 {
    private int n1 = 200;

    class Inner {
        public void Print() {
            System.out.println(n1);
        }
    }
}
```

2 再外部类中编写一个方法返回一个Inner的实例（将第一种写为方法）

```java
package com.npu.inner;

public class Member {//其他类
    public static void main(String[] args) {
        Outer08 outer = new Outer08();
        Outer08.Inner inner = outer.getInner();

    }
}

class Outer08 {
    private int n1 = 200;

    class Inner {
        public void Print() {
            System.out.println(n1);
        }
    }

    public Inner getInner() {
        return new Inner();
    }
}
```



### 静态内部类

定义在外部类的成员位置，由static修饰

1.可以直接访问外部类的所有静态成员，包含私有的，但是不可以访问非静态成员

2.成员内部类可以用访问修饰符修饰，因为它的地位是类的成员

3.作用域为整个类体中

```java
package com.npu.inner;

public class Member {//其他类
    public static void main(String[] args) {
        Outer08 outer = new Outer08();
        outer.getInner();

    }
}

class Outer08 {
    private int n1 = 200;
    private static int n2 = 100;

    public static class Inner {
        public void Print() {
            System.out.println(n2);
        }
    }

    public void getInner() {
        Inner inner = new Inner();
        inner.Print();
    }
}
```

4.外部其他类使用静态内部类有以下几种方式

1 用外部类静态创建内部类（不会创建外部类）

```java
package com.npu.inner;

public class Member {//其他类
    public static void main(String[] args) {
        Outer08.Inner inner = new Outer08.Inner();
        inner.Print();
    }
}

class Outer08 {
    private int n1 = 200;
    private static int n2 = 100;

    public static class Inner {
        public void Print() {
            System.out.println(n2);
        }
    }

}
```

2 静态方法返回

```java
package com.npu.inner;

public class Member {//其他类
    public static void main(String[] args) {
        Outer08.Inner inner = Outer08.getInner();
        inner.Print();
    }
}

class Outer08 {
    private int n1 = 200;
    private static int n2 = 100;

    public static class Inner {
        public void Print() {
            System.out.println(n2);
        }
    }

    public static Inner getInner() {
        return new Inner();
    }
}
```

5.在遇到同名成员的时候默认就近访问，要访问外部类成员需要使用 外部类名.成员访问
