---
title: Java笔记
published: 2025-03-03
updated: 2025-03-03
description: '进制，位运算，流程控制'
image: ''
tags: [Java]
category: 'Java'
draft: false 
---

# 听课笔记

## Java中的进制

二进制  	0b 开头		 如 0b101， 0B1111

十进制  						 如 10， 100

八进制  	0 开头表示	如 077，0734

十六进制  0x开头表示	如 0xFFFF， 0Xab03， 0xaaaa， 0XEEFE

## Java中的位运算

~2 按位取反

2&3 按位与

2|3  按位或

3^6 按位异或

## Java 中的位运算符

算术左移，<<，符号位不变低位补 0

算数右移，>>，符号位不变用符号位补高位

逻辑右移，>>>，低位溢出，高位补 0

## Java中的分支控制

### if && else

随便写了一个，较为简陋

```java
import java.util.Scanner;

public class Test{
	
	public static void main(String[] args) {
		Scanner myscanner = new Scanner(System.in);
		System.out.println("请输入成绩：");
		int grade = myscanner.nextInt();

		if(grade > 90) {
			System.out.println("A");
		} else if(grade > 60 && grade <= 90) {
			System.out.println("B");
		} else {
			System.out.println("C");
		}
	}
}
```

**分支的嵌套注意点：在使用分支嵌套的情况下不要超过三层的分支**

嵌套分支举例

```java
import java.util.Scanner;

public class Test{
	
	public static void main(String[] args) {
		Scanner myscanner = new Scanner(System.in);
		System.out.println("请输入成绩：");
		int grade = myscanner.nextInt();

		System.out.println("请输入性别：");
		int sex = myscanner.next().charAt(0);	
         /*
         注意在java中似乎没有nextChar()的用法 需要使用next接收一个字符串
         再将字符串中运用charAt(0)提取出第一个字符
		*/
		if(grade > 60) {
			System.out.println("恭喜过关");
			if(sex == '男') {
				System.out.println("进入男子组决赛");
			} else {
				System.out.println("进入女子组决赛");
			}
		} else {
			System.out.println("淘汰");
		}
	}
}
```

### switch

与C语言同理在这里不多做说明以下是一个代码示例

```java
import java.util.Scanner;

public class Test{
	
	public static void main(String[] args) {
		Scanner myscanner = new Scanner(System.in);
		System.out.println("请输入成绩：");
		int grade = myscanner.nextInt();
		switch(grade){
			case 1:
				System.out.println("A");
			break;		
             //break 退出 如果不假如break会依次执行后续的分支，在这里删除break则会输出A B
			case 2:
				System.out.println("B");
			break;		
			default:	//默认的分支
				System.out.println("C");
			break;
		}
	}
}
```

switch 中值得注意的细节

1.表达式的类型应该和 case 后的常量一致，或者可以发生自动转换，例如输入的是字符，而case 后常量为 int

2.case 语句后的值一定是常量，不能是变量

利用 击穿 的特性我们也可以简化一些代码，如下所示

```java
import java.util.Scanner;

public class Test{
	
	public static void main(String[] args) {
		Scanner myscanner = new Scanner(System.in);
		System.out.println("请输入月份：");
		int grade = myscanner.nextInt();
		switch(grade){
             //当满足以下三条任意的条件中的一条执行break前的指令
			case 3:
			case 4:
			case 5:
				System.out.println("春季");
			break;
			case 6:
			case 7:
			case 8:
				System.out.println("夏季");
			break;
			case 9:
			case 10:
			case 11:
				System.out.println("秋季");
			break;
			default:
				System.out.println("冬季");
			break;
		}
	}
}
```

### 在编程时候if 和 switch 的取舍

1.如果判断类型数值不多的情况下，并且符合byte short int char enum String 六种类型时优先使用switch

2.区间判断，boolean 类型判断时 if 使用更加广泛

## Java中的循环控制

### for 循环控制

与c语言基本一模一样，在这里不多介绍，以下是代码样例

```java
import java.util.Scanner;

public class Input{
	public static void main(String[] args){
		Scanner myScanner = new Scanner(System.in);
		System.out.println("请输入名字：");
		String name = myScanner.next();
		
		for(int i = 0; i < 10; i++){
			System.out.println(name);
		}
		
	}
}
```

### while 循环控制

在这里因为java中循环条件需要boolean类型的表达式，而在java中不能自动完成 int 到 boolean类型的转换

c程序编写中常见的 flag-- 计数不再适用 

```java
import java.util.Scanner;

public class Input{
	public static void main(String[] args){
		Scanner myScanner = new Scanner(System.in);
		System.out.println("请输入数字：");
		int flag = myScanner.nextInt();
		
		while(flag > 0) {
			System.out.println(flag);
			flag--;
		}
        /*
        while(flag--) {
			System.out.println(flag);
		}
        */
	}
}
```

当然可以写成一下这种写法

```java
import java.util.Scanner;

public class Input{
	public static void main(String[] args){
		Scanner myScanner = new Scanner(System.in);
		System.out.println("请输入数字：");
		int flag = myScanner.nextInt();
		
		while((flag--) != 0) {
			System.out.println(10);
		}
	}
}
```

### do while 循环语句

一定会执行一次，在执行完后再次判断是否执行语句块，如下是一个暴力的催债程序:joy:

先打一拳！打的时候再把问题问遍！！

```java
import java.util.Scanner;

public class Input{
	public static void main(String[] args){
		Scanner myScanner = new Scanner(System.in);
		boolean flag = false;
		int i = 1;
		System.out.println("还不还钱？");
		do {
			System.out.println("打第" + i + "拳");
			i++;
			flag = myScanner.nextBoolean();
		}while(!flag); 
		System.out.println("还钱了，结束打人");
	}
}
```

## Java中的多重循环

学习到了第三天终于来到了算法难点部分，在这里也希望自己可以通过课程学习来巩固自己算法的基础。
