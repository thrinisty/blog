---
title: Java笔记
published: 2025-03-11
updated: 2025-03-11
description: '多态，动态绑定机制，多态的应用'
image: ''
tags: [Java]
category: 'Java'
draft: false 
---

# 听课笔记

## 多态

定义：方法或者对象具有多种形态

1.重写或者重载体现出了方法的多态

2.对象体现多态（是多态的核心）

当一个类对于某一个类做一些操作的时候，我们可以对于每一个后向类编写方法，但是如果这些类都继承于同一个类，我们可以通过对于着一些类的父类编写方法来统一操作这一类的各个子类。通过这样做我们可以极大的提高代码的复用性

例如我们有猫类，狗类继承于动物类，我们要通过一个人类来模拟喂食的时候需要在人类的方法中定义两个feed重载方法，这样子有大量的冗余

```java
package com.npu.poly;

public class Poly01 {
    public static void main(String[] args) {
        Master Tom = new Master("汤姆");
        Dog dog =  new Dog("大黄");
        Bone bone = new Bone("棒骨");
        Tom.feed(dog, bone);

        Cat cat = new Cat("小花");
        Fish fish = new Fish("鱼");
        Tom.feed(cat, fish);
    }
}
```

```java
package com.npu.poly;

public class Dog extends Animal {
    public Dog(String name) {
        super(name);
    }
}
```

```java
package com.npu.poly;

public class Cat extends Animal {
    public Cat(String name) {
        super(name);
    }
}
```

```java
package com.npu.poly;

public class Master {
    private String name;
    
    public void setName(String name) {
        this.name = name;
    }

    public void feed(Dog dog, Bone bone) {
        System.out.println("喂" + dog.getName() + "吃" + bone.getName());
    }

    public void feed(Cat cat, Fish fish) {
        System.out.println("喂" + cat.getName() + "吃" + fish.getName());
    }
}
```

将上述的feed方法做更改，删除原先的feed方法

```java
public void feed(Animal animal, Food food) {
        System.out.println("喂" + animal.getName() + "吃" + food.getName());
    }
```

这样子做，在后续加动物的子类，或者加事物的子类，都可以使用这个方法，大大提高了代码的可维护性

## 多态重点

1.一个对象的编译类型和运行类型可以不一致

```java
Animal animal = new Dog();
//animal的编译类型是Animal 运行类型是Dog类
//可以用父类的引用指向子类的一个对象
animal = new Cat();//运行类型更改为Cat
```

2.编译类型在定义对象时就被确定，不要能够改变

3.运行类型可以变化

4.编译类型看定义时 = 左边的内容， 运行时类型看 = 右边的内容

## 多态的细节

多态的前提是两个类存在继承关系

### 向上转型

父类的引用指向子类的对象

语法：父类类型 父类引用名称 = new 子类类型（）；

特点：编译类型是父类，运行类型是子类

可以调用父类的所有成员，不可以调用子类的特有成员，但是如果有和父类的同名方法，最终调用的是子类方法

这是因为在javac编译的时候只会认为对象是父类，但是交给运行器java的时候要从子类开始查找方法，按照继承那一章的步骤同理

![53](../images/53.png)

### 向下转型

语法：子类类型 子类引用名称 = （子类类型） 父类引用

只可以强制转化父类的引用，不能够强制转化父类的对象

要求父类的引用必须要指向当前目标类型的对象  例如：不能将指向狗的动物类转化为猫类

这样将父类引用转化为子类，即可使用子类类型的成员

### 属性细节

属性没有重写之说，属性的值看编译类型

### instanceOf 

### 比较操作运算符

运行类型 是否是 某类型或者该类型的子类型

```java
public static void main(String[] args) {
        Master Tom = new Master("汤姆");
        Dog dog = new Dog("大黄");
        Bone bone = new Bone("棒骨");
        Tom.feed(dog, bone);

        Cat cat = new Cat("小花");
        Fish fish = new Fish("鱼");
        Tom.feed(cat, fish);

        Animal animal = new Animal("123");
        System.out.println(animal instanceof Animal);
        System.out.println(cat instanceof Animal);
    }
}
```

运行结果

```
喂大黄吃棒骨
喂小花吃鱼
true
true
```

## Java的动态绑定机制

以下是一个代码示例

```java
package com.npu.dynamic;

public class Main {
    public static void main(String[] args) {
        A a = new B();
        System.out.println(a.sum());//40用运行时的类型b
        System.out.println(a.sum1());//30用运行时的类型b
    }
}

class A {
    public int i = 10;
    public int sum() {
        return getl() + 10;
    }
    public int getl() {
        return i;
    }
    public int sum1() {
        return i + 10;
    }
}

class B extends A {
    public int i = 20;
    public int sum() {
        return i + 20;
    }
    public int getl() {
        return i;
    }
    public int sum1() {
        return i + 10;
    }
}
```

现在将b子类的sum()方法注释，调用的就是父类的sum方法，但是这里问题出现了，到底是用的父类的getl方法还是子类的getl方法，引出了如下的规则

动态绑定机制：

1.当调用对象方法的时候，方法会和该对象的内存地址/运行类型做绑定

System.out.println(a.sum());

此时调用父类sum方法，sum中调用子类的getl方法，最终结果是30

2.当调用对象属性的时候，没有动态绑定机制，哪里声明，就在那里使用

将子类方法sum1注释，调用了父类的方法，而属性是父类的i，最终的结果是20

```java
package com.npu.dynamic;

public class Main {
    public static void main(String[] args) {
        A a = new B();
        System.out.println(a.sum());//30
        System.out.println(a.sum1());//20
    }
}

class A {
    public int i = 10;
    public int sum() {
        return getl() + 10;
    }
    public int getl() {
        return i;
    }
    public int sum1() {
        return i + 10;
    }
}

class B extends A {
    public int i = 20;
    public int getl() {
        return i;
    }
}
```

## 多态的应用

### 多态数组

数组的定义是父类类型，保存的元素类型是子类类型

以下是一个存储老师和学生的Person类数组

```java
package com.npu.polyarr;

public class Main {
    public static void main(String[] args) {
        Person[] personArr = new Person[2];
        personArr[0] = new Teacher("John", 23);
        personArr[1] = new Student("Jane", 25);
        Student student = (Student) personArr[1];
        Teacher teacher = (Teacher) personArr[0];
        student.say();
        teacher.say();
    }
}

class Person {
    String name;
    int age;

    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }

    public void say() {
        System.out.println(name + " " + age);
    }
}

class Student extends Person {
    public Student(String name, int age) {
        super(name, age);
    }

    public void say() {
        System.out.println("I am student" + name);
    }
}

class Teacher extends Person {
    public Teacher(String name, int age) {
        super(name, age);
    }

    public void say() {
        System.out.println("I am teacher" + name);
    }
}
```

主函数中也可以不做类型转化，因为调用的方法会根据运行类型做转化

```java
public class Main {
    public static void main(String[] args) {
        Person[] personArr = new Person[2];
        personArr[0] = new Teacher("John", 23);
        personArr[1] = new Student("Jane", 25);
        for(int i = 0; i<personArr.length; i++) {
            personArr[i].say();
        }
    }
}
```

但是在调用方法的时候需要向下转换调用方法，转换为子类型的引用再调用子类方法

在这里还运用了类型的判断（运行类型判断）

```java
package com.npu.polyarr;

public class Main {
    public static void main(String[] args) {
        Person[] personArr = new Person[2];
        personArr[0] = new Teacher("John", 23);
        personArr[1] = new Student("Jane", 25);
        for(int i = 0; i<personArr.length; i++) {
            personArr[i].say();
            if(personArr[i] instanceof Teacher) {
                Teacher teacher = (Teacher) personArr[i];
                teacher.teach();
            } else {
                Student student = (Student) personArr[i];
                student.study();
            }
        }
    }
}

class Person {
    String name;
    int age;

    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }

    public void say() {
        System.out.println(name + " " + age);
    }
}

class Student extends Person {
    public Student(String name, int age) {
        super(name, age);
    }

    public void say() {
        System.out.println("I am student " + name);
    }

    public void study() {
        System.out.println("studying");
    }
}

class Teacher extends Person {
    public Teacher(String name, int age) {
        super(name, age);
    }

    public void say() {
        System.out.println("I am teacher " + name);
    }

    public void teach() {
        System.out.println("teaching");
    }
}
```

### 多态参数

1.前文喂动物的时候曾使用过

2.模拟工厂分发工资

```java
package com.npu.polyparameter;

public class Main {
    public static void main(String[] args) {
        Worker worker = new Worker("大壮", 2000);
        Manager manager = new Manager("小明", 2300, 3000);
        Tool tool = new Tool();
        System.out.println(tool.showEmpAnnual(worker));
        System.out.println(tool.showEmpAnnual(manager));
        tool.testWork(worker);
        tool.testWork(manager);
    }
}

class Tool {
    public double showEmpAnnual(Employee emp) {
        if(emp instanceof Worker) {
            Worker worker = (Worker) emp;
            return worker.calculateSalary();
        } else {
            Manager manager = (Manager) emp;
            return manager.calculateSalary();
        }
    }
    public void testWork(Employee emp) {
        if(emp instanceof Worker) {
            Worker worker = (Worker) emp;
            worker.work();
        } else {
            Manager manager = (Manager) emp;
            manager.manage();
        }
    }
}

class Employee {
    String name;
    double salary;

    public Employee(String name, double salary) {
        this.name = name;
        this.salary = salary;
    }

    public double calculateSalary() {
        return salary * 12;
    }
}

class Worker extends Employee {
    public Worker(String name, double salary) {
        super(name, salary);
    }
    public void work() {
        System.out.println("work");
    }

    public double calculateSalary() {
        return super.calculateSalary();
    }
}

class Manager extends Employee {
    double bonus;
    public Manager(String name, double salary, double bonus) {
        super(name, salary);
        this.bonus = bonus;
    }
    public double calculateSalary() {
        return super.calculateSalary() + bonus;
    }
    public void manage() {
        System.out.println("manage");
    }
}
```

以上就是有关于多态的内容，相关的联系还得再多做习题巩固

明天会来到Object类的详解，以及断点调试相关内容
