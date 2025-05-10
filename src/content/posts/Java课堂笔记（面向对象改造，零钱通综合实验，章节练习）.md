---
title: Java笔记
published: 2025-03-13
updated: 2025-03-13
description: '面向对象改造，零钱通综合实验，章节练习'
image: ''
tags: [Java]
category: 'Java'
draft: false 
---

# 听课笔记

## 零钱通综合案例

目的是使用Java开发零钱通项目，可以完成收益入账，消费，查看明细，退出等功能

### 1.显示菜单，可以显示菜单，给出对应提示

```java
package com.smallchange;

import java.util.Scanner;

public class SmallChangeSys {
    public static void main(String[] args) {
        boolean loop = true;
        do {
            System.out.println("==========零钱通==========");
            System.out.println("\t\t1 零钱通明细");
            System.out.println("\t\t2 收益入账");
            System.out.println("\t\t3 消费");
            System.out.println("\t\t4 退出");

            System.out.println("请选择1-4");
            Scanner scanner = new Scanner(System.in);
            switch (scanner.nextInt()) {
                case 1:
                    System.out.println("1 零钱通明细");
                    break;
                case 2:
                    System.out.println("2收益入账");
                    break;
                case 3:
                    System.out.println("3 收益入账");
                    break;
                case 4:
                    System.out.println("4 程序退出");
                    loop = false;
                    break;
            }
        } while (loop);
    }
}
```

### 2.完成零钱通明细

将收益放入数组，适用对象存储，用字符串进行拼接

用字符串接收即可实现，打印字符串即可

```java
case 1:
   	System.out.println(details);
break;
```

### 3.完成收益入账

根据实现的功能定义新的变量

```java
case 2:
	System.out.println("收益入账金额：");
	 money = scanner.nextDouble();
	//校验金额
 	balance += money;
  	date = new Date();//获取到当前的日期
 	 SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd 		HH:mm:ss");
	details +="\n收益入账\t" + money + "\t" + sdf.format(date) + 	"\t当前余额为 " + balance;
break;
```

### 4.完成消费功能

```java
case 3:
    System.out.println("输入消费金额");
   	money = scanner.nextDouble();
   	//金额校验
    System.out.print("消费说明:");
    note = scanner.next();
    balance -= money;
    date = new Date();//获取到当前的日期
   	details +="\n" + note + " 消费\t" + money + "\t" + 		sdf.format(date) + "\t当前余额为 " + balance;
   	break;
```

### 5.退出逻辑

```java
case 4:
	 String choice = "";
	while (true) {
	System.out.println("请确认是否退出");
    choice = scanner.next();
	if ("y".equals(choice) || "n".equals(choice)) {
 		break;
  	}
 	if ("y".equals(choice)) {
		loop = false;
	}
	break;
```

### 代码整体

```java
package com.smallchange;

import java.text.SimpleDateFormat;
import java.util.Date;
import java.util.Scanner;

public class SmallChangeSys {
    public static void main(String[] args) {
        boolean loop = true;
        String details = "------------零钱通明细---------------";
        double money = 0;
        double balance = 0;
        Date date = null;
        SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
        String note = null;

        do {
            System.out.println("==========零钱通==========");
            System.out.println("\t\t1 零钱通明细");
            System.out.println("\t\t2 收益入账");
            System.out.println("\t\t3 消费");
            System.out.println("\t\t4 退出");

            System.out.println("请选择1-4");
            Scanner scanner = new Scanner(System.in);
            switch (scanner.nextInt()) {
                case 1:
                    System.out.println(details);
                    break;
                case 2:
                    System.out.println("收益入账金额：");
                    money = scanner.nextDouble();
                    //校验金额
                    balance += money;
                    date = new Date();//获取到当前的日期
                    if (money <= 0) {
                        System.out.println("金额错误");
                        break;
                    }
                    details += "\n收益入账\t" + money + "\t" + sdf.format(date) + "\t当前余额为 " + balance;
                    break;
                case 3:
                    System.out.println("输入消费金额");
                    money = scanner.nextDouble();
                    if(money >= balance) {
                        System.out.println("余额不足");
                        break;
                    }
                    System.out.print("消费说明:");
                    note = scanner.next();
                    balance -= money;
                    date = new Date();//获取到当前的日期
                    details += "\n" + note + " 消费\t" + money + "\t" + sdf.format(date) + "\t当前余额为 " + balance;
                    break;
                case 4:
                    String choice = "";
                    while (true) {
                        System.out.println("请确认是否退出");
                        choice = scanner.next();
                        if ("y".equals(choice) || "n".equals(choice)) {
                            break;
                        }
                    }
                    if ("y".equals(choice)) {
                        loop = false;
                    }
                    break;
                default:
            }
        } while (loop);
    }
}
```

## 面向对象

以上是面向过程的代码编程，以下是对于代码的改进，体会面向对象的方便之处

将各个操作更改为以类的方法实现，再将方法综合在菜单方法里调用，即可完成封装

main方法构造以及调用

```java
package com.smallchange;

public class SmallChangeSysApp {
    public static void main(String[] args) {
        SmallChangeSysOOP app = new SmallChangeSysOOP();
        app.mainMenu();
    }
}
```

零钱通类

```java
package com.smallchange;

import java.text.SimpleDateFormat;
import java.util.Date;
import java.util.Scanner;

//面向对象编程
//将各个功能对应一个方法
public class SmallChangeSysOOP {
    boolean loop = true;
    String details = "------------零钱通明细---------------";
    double money = 0;
    double balance = 0;
    Date date = null;
    SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
    String note = null;
    Scanner scanner = new Scanner(System.in);

    public void mainMenu() {
        do {
            System.out.println("==========零钱通==========");
            System.out.println("\t\t1 零钱通明细");
            System.out.println("\t\t2 收益入账");
            System.out.println("\t\t3 消费");
            System.out.println("\t\t4 退出");

            System.out.println("请选择1-4");
            Scanner scanner = new Scanner(System.in);
            switch (scanner.nextInt()) {
                case 1:
                    this.detail();
                    break;
                case 2:
                    this.income();
                    break;
                case 3:
                    this.pay();
                    break;
                case 4:
                    this.exit();
                    break;
                default:
            }
        } while (loop);
    }

    public void detail() {
        System.out.println(details);
    }

    public void income(){
        System.out.println("收益入账金额：");
        money = scanner.nextDouble();
        //校验金额
        balance += money;
        date = new Date();//获取到当前的日期
        if (money <= 0) {
            System.out.println("金额错误");
            return;
        }
        details += "\n收益入账\t" + money + "\t" + sdf.format(date) + "\t当前余额为 " + balance;
    }

    public void pay(){
        System.out.println("输入消费金额");
        money = scanner.nextDouble();
        if(money >= balance) {
            System.out.println("余额不足");
            return;
        }
        System.out.print("消费说明:");
        note = scanner.next();
        balance -= money;
        date = new Date();//获取到当前的日期
        details += "\n" + note + " 消费\t" + money + "\t" + sdf.format(date) + "\t当前余额为 " + balance;
    }

    public void exit() {
        String choice = "";
        while (true) {
            System.out.println("请确认是否退出");
            choice = scanner.next();
            if ("y".equals(choice) || "n".equals(choice)) {
                break;
            }
        }
        if ("y".equals(choice)) {
            loop = false;
        }
    }
}
```

## 章节作业

1.定义Person类，对Person数组按照年龄排序

```java
package com.homework;

public class Homework01 {
    public static void main(String[] args) {
        Person[] personArray = new Person[3];
        personArray[0] = new Person("Jerry", 19, "1");
        personArray[1] = new Person("John", 55, "2");
        personArray[2] = new Person("Mary", 33, "3");
        Tool tool = new Tool();
        tool.sort(personArray);
        tool.print(personArray);
    }
}

class Person {
    String name;
    int age;
    String job;

    Person(String name, int age, String job) {
        this.name = name;
        this.age = age;
        this.job = job;
    }
    @Override
    public String toString() {
        return this.name + " " + this.age + " " + this.job;
    }
}

class Tool {
    public void sort(Person[] personArray) {
        for (int i = 0; i < personArray.length - 1; i++) {
            for (int j = 0; j < personArray.length - i - 1; j++) {
                if(personArray[j].age < personArray[j + 1].age) {
                    Person temp = personArray[j];
                    personArray[j] = personArray[j + 1];
                    personArray[j + 1] = temp;
                }
            }
        }
    }

    public void print(Person[] personArray) {
        for (int i = 0; i < personArray.length; i++) {
            System.out.println(personArray[i].toString());
        }
    }
}
```

2.编写老师类，使三个教师类继承老师类，在教师类中重写方法

```java
package com.homework;

public class Homework03 {
    public static void main(String[] args) {
        Professor professor = new Professor("LaoLi", 19, "Math", 1900);
        professor.introduce();
    }
}

class Teacher {
    String name;
    int age;
    String post;
    double salary;

    public Teacher(String name, int age, String post, double salary) {
        this.name = name;
        this.age = age;
        this.post = post;
        this.salary = salary;
    }

    public void introduce() {
        System.out.println("Name: " + name + ", Age: " + age + ", Post: " + post + ", Salary: " + salary);
    }
}

class Professor extends Teacher {
    Professor(String name, int age, String post, double salary) {
        super(name, age, post, salary);
    }
    public void introduce() {
        System.out.println("ProfessorName: " + name + ", Age: " + age + ", Post: " + post + ", Salary: " + salary * 1.3);
    }
}
```

3.Point

```java
package com.homework;

public class Homework09 {
    public static void main(String[] args) {
        LabeledPoint point = new LabeledPoint("Thursday", 12 , 34);
    }
}

class Point {
    int x;
    int y;

    public Point(int x, int y) {
        this.x = x;
        this.y = y;
    }
}

class LabeledPoint extends Point {
    String label;

    public LabeledPoint(String lable, int x, int y) {
        super(x, y);
        this.label = label;
    }
}
```

4.判断医生类是否各个属性相同

```java
package com.homework;

public class Homework10 {
    public static void main(String[] args) {
        Doctor human1 = new Doctor("Li", 19, "A", 'F',1900);
        Doctor human2 = new Doctor("Li", 19, "A", 'F',1900);
        System.out.println(human1.equals(human2));
    }
}

class Doctor {
    String name;
    int age;
    String job;
    char gender;
    double salary;

    public Doctor(String name, int age, String job, char gender, double salary) {
        this.name = name;
        this.age = age;
        this.job = job;
        this.gender = gender;
        this.salary = salary;
    }

    public void setter(String name, int age, String job, char gender, double salary) {
        this.name = name;
        this.age = age;
        this.job = job;
        this.gender = gender;
        this.salary = salary;
    }

    public void getter() {
        System.out.println(this.name + " " + this.age + " " + this.job + " " + this.gender + " " + this.salary);
    }

    public boolean equals(Object obj) {
        if(obj instanceof Doctor) {
            Doctor doctor = (Doctor) obj;
            return this.name.equals(doctor.name) && this.age
                    == doctor.age && this.job.equals(doctor.job) && this.gender
                    == doctor.gender && this.salary == doctor.salary;

        }
        return false;
    }
}
```

5.学生老师综合案例

```java
package com.homework13;

public class Homework13 {
    public static void main(String[] args) {
        Teacher teacher1 = new Teacher("王芳", '女', 39, 10);
        Student student1 = new Student("李明", '男', 19, 222333);
        Person[] personArray = new Person[4];
        personArray[0] = teacher1;
        personArray[1] = student1;
        personArray[2] = new Teacher("王", '男', 59, 10);
        personArray[3] = new Student("李", '女', 19, 333222);
        Tool tool = new Tool();
        tool.sort(personArray);
        for(Person person : personArray) {
            System.out.println(person.toString());
            tool.play(person);
        }

    }
}

class Tool {
    public void sort(Person[] personArray) {
        Person temp = null;
        for (int i = 0; i < personArray.length - 1; i++) {
            for (int j = 0; j < personArray.length - 1 - i; j++) {
                if(personArray[j].getAge() < personArray[j + 1].getAge()) {
                    temp = personArray[j];
                    personArray[j] = personArray[j + 1];
                    personArray[j + 1] = temp;
                }
            }
        }
    }

    public void play(Person person) {
        if(person instanceof Teacher) {
            Teacher teacher = (Teacher) person;
            teacher.play();
        } else if(person instanceof Student) {
            Student student = (Student) person;
            student.play();
        } else {
            System.out.println("Fail to play!!");
        }
    }
}

class Person {
    private String name;
    private char sex;
    private int age;

    public Person(String name, char sex, int age) {
        this.name = name;
        this.sex = sex;
        this.age = age;
    }

    public String toString() {
        return    "姓名：" + this.name + "\n"
                + "年龄：" + this.age + "\n"
                + "性别：" + this.sex + "\n";
    }

    public void play() {
        System.out.println("play");
    }

    public String getName() {
        return name;
    }

    public int getAge() {
        return age;
    }

    public char getSex() {
        return this.sex;
    }

}

class Teacher extends Person {
    private int workAge;

    Teacher(String name, char sex, int age, int workAge) {
        super(name, sex, age);
        this.workAge = workAge;
    }

    public String toString() {
        return "老师的信息：\n" + super.toString() + "工龄： " + this.workAge;
    }

    public void play() {
        System.out.println("teacher play");
    }

    public int getWorkAge() {
        return workAge;
    }
}

class Student extends Person {
    private int id;
    Student(String name, char sex, int age, int id) {
        super(name, sex, age);
        this.id = id;
    }
    public String toString() {
        return "学生的信息：\n" + super.toString() + "学号： " + this.id;
    }

    public void play() {
        System.out.println("student play");
    }
    public int getId() {
        return id;
    }
}

```

因为课程大作业的安排，我打算明天完成房屋出租系统设计之后，先进行Java网络编程部分的学习，以此复习计算机网络相关知识，再顺便水个作业，美哉美哉
