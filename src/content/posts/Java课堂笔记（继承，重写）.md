---
title: Java笔记
published: 2025-03-10
updated: 2025-03-10
description: '继承，重写'
image: ''
tags: [Java]
category: 'Java'
draft: false 
---

# 听课笔记

## 面向对象的三大特征

### 继承

为了解决代码复用性的情况，引出了继承的概念

例如以下的代码有小学生类以及大学生类，除了各自特有的方法，他们的属性，方法大致相同

```java
package com.npu.extend;

public class Pupil {
    public String name;
    public int age;
    private double score;

    Pupil(String name, int age, double score) {
        this.name = name;
        this.age = age;
        this.score = score;
    }

    public void infoPrint() {
        System.out.println("Name: " + name + ", Age: " + age + ", Score: " + score);
    }
}
```



```java
package com.npu.extend;

public class Graduate {
    public String name;
    public int age;
    private double score;

    Graduate(String name, int age, double score) {
        this.name = name;
        this.age = age;
        this.score = score;
    }

    public void infoPrint() {
        System.out.println("Name: " + name + ", Age: " + age + ", Score: " + score);
    }
}
```



```java
package com.npu.extend;

public class Extends01 {
    public static void main(String[] args) {
        Graduate human1 = new Graduate("thrinisy", 18, 100);
        human1.infoPrint();
        Pupil pupil1 = new Pupil("pupil", 11, 100);
        pupil1.infoPrint();
    }
}
```

当多个类存在相同的属性和方法的时候，我们可以从类中抽象出这些相同的属性和方法制作父类，所有的子类中不需要重新定义这一些属性方法，而是通过extends 关键字声明即可

父类又称为超类，基类

子类又称派生类

extends 使用

```java
class Children extends Father {
}
```

将上述的例子稍作更改

将小学生和大学生抽象出一个父类 人类People

```java
package com.npu.extend;

public class People {
    public String name;
    public int age;
    private double score;

    public void infoPrint() {
        System.out.println("Name: " + name + ", Age: " + age + ", Score: " + score);
    }
}
```

大学生

```java
package com.npu.extend;

public class Graduate extends People {
    public void speak () {
        System.out.println("大学生阴暗的爬行~");
    }
}
```

小学生

```java
package com.npu.extend;

public class Pupil extends People {
    public void speak () {
        System.out.println("小学生玩游戏");
    }
}
```

### 继承的细节

1.子类继承了所有的属性和方法，但是私有的属性和方法不能够直接访问，必须要通过共有的方法才可以对private 修饰的属性进行修改

例如我们要修改上述的成绩属性，我们需要在People定义一个公共的方法来进行更改

要调用私有的方法只可以通过一个包含该私有方法的公共方法进行调用

```java
public void setScore(double score) {
        this.score = score;
    }
```

这样才可以在子类中调用该方法进行修改

```java
package com.npu.extend;

public class Extends01 {
    public static void main(String[] args) {
        Graduate graduate = new Graduate();
        graduate.name = "thrinisty";
        graduate.age = 18;
        graduate.setScore(11.3);
        graduate.infoPrint();

        Pupil pupil = new Pupil();
        pupil.name = "james";
        pupil.age = 11;
        pupil.setScore(110.3);
        pupil.infoPrint();
    }
}
```

2.子类必须调用父类的构造器，完成父类的初始化

在创建子类的时候，先调用父类的构造器，再调用子类的构造器（默认使用了super使用父类的构造器）

3.当创建子类对象的时候，不管使用子类的哪个构造器，默认情况下总会调用父类的无参构造器，如果父类没有无参构造器，则必须要在子类的构造器中用super指定用哪个父类的构造器

```java
public Graduate(String name, int age) {
        super(name, age);
    }
```

```java
 People(String name, int age) {
        this.name = name;
        this.age = age;
    }
```

4.super在使用的时候需要放在构造器的第一行

5.super和 this 不同存在于同一个构造器中，因为两个都必须要写在第一行

6.java中的所有类都是Object类的子类![52](../images/52.png)

7.父类构造器的调用不限于直接父类，将向上追溯到Object类

所以当C继承于B继承于A的时候，调用C类构造器先执行的是A的构造器--->B—>C

8.Java的单继承，一个子类只能继承一个父类

9.不能滥用继承关系，子类和父类必须要满足 子类是父类 的关系

### 继承的本质（内存布局）

在访问属性的时候按照查找关系来返回信息，先检查子类有无这个属性，有则返回，无则向上访问直到找到属性

```java
package com.npu.extend.test;

public class Inside {
    public static void main(String[] args) {
        Son son = new Son();
        System.out.println("name " + son.name);
    }
}

class Grandpa {
    String name = "Grandpa";
    String hobby = "GHobby";
}

class Father extends Grandpa {
    String name = "Father";
    int age = 40;
}

class Son extends Father {
    String name = "Son";
}
```

![53](../images/53.png)

### super关键字

super是父类的引用，可以访问父类的方法 属性 构造器

但是不可以访问父类的私有方法以及私有属性

1.当子类父类有重名属性方法时，访问父类的重名属性方法需要使用super，没有重名的话使用super this 直接访问的效果是一样的，但是为了代码的可读性，要访问属性或者方法之前，统一加上 this 或者 super 显示的指出要访问的是本类还是父类的属性方法

2.有同名的属性方法就近找，先找子类再找父类

### 方法的重写

当子类的一个方法和父类的某一个方法重名，返回类型一致，参数一致，我们就说子类的方法覆盖了父类的方法

以下是一个代码示例

```java
package com.npu.extend.overwrite;

public class Override {
    public static void main(String[] args) {
        Cat cat = new Cat();
        Dog dog = new Dog();
        cat.speak();
        dog.speak();
    }
}
class Animal {
    public int age;
    public void speak() {
        System.out.println("speak");
    }
}
class Cat extends Animal {
    public void speak() {
        System.out.println("CAT speak");
    }
}
class Dog extends Animal {
    public void speak() {
        System.out.println("Dog speak");
    }
}
```

### 方法重写细节

1.子类的方法参数，方法名称要和父类的一致

2.子类的返回类型要和父类的方法类型一样或者是父类返回类型的子类

3.子类方法不可以缩小父类方法的访问权限

重写和重载之间的比较

![54](../images/54.png)

明天会来到面向对象中的多态
