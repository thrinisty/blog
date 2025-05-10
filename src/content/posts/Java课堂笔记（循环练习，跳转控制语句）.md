---
title: Java笔记
published: 2025-03-04
updated: 2025-03-04
description: '循环练习，跳转控制语句'
image: ''
tags: [Java]
category: 'Java'
draft: false 
---

# 听课笔记

## 多重循环例题

### 打印九九乘法表：

```java

public class Move{
	public static void main(String[] args) {
		for(int i = 1; i < 10; i++) {//九九乘法表有九行 从一开始
			for(int j = 1; j <= i; j++) {//九九乘法表在每一行上的乘法表达式不超过行列数
				System.out.print(i + " * " + j + "=" + i*j + "\t");
			}
			System.out.print("\n");
		}
	}
}
```



### 空心金字塔：

经典的嵌套循环练习题目

```java
import java.util.Scanner;

public class Move{
	public static void main(String[] args) {
		Scanner myScanner = new Scanner(System.in);
		int num = myScanner.nextInt();
		for(int i = 0; i < num; i++) {			//第一层循环 i代表每一层
			for(int j = 0; j < num - i; j++) {	
				System.out.print(" ");			//每一层都有空格开头 需要用输出填充
			}
			for(int j = 0; j <= 2 * i; j++) { 	//每一行需要输出 2 * 层数 + 1 个符号
				if(j == 0 || j == 2 * i || i == num -1) {
					System.out.print('*');
				} else {
					System.out.print(' ');
				}
				
			}
			System.out.print("\n");
		}
	}
}
```

在编程这一道题目的时候，可以先打印出一个实心金字塔，再对代码进行修改处理

题目拓展：打印一个空心的菱形

```java
import java.util.Scanner;

public class Move{
	public static void main(String[] args) {
		Scanner myScanner = new Scanner(System.in);
		int num = myScanner.nextInt();
		for(int i = 0; i < num; i++) {			//第一层循环 i代表每一层
			for(int j = 0; j < num - i - 1; j++) {	
				System.out.print(" ");			//每一层都有空格开头 需要用输出填充
			}
			for(int j = 0; j <= 2 * i; j++) { 	//每一行需要输出 2 * 层数 + 1 个符号
				if(j == 0 || j == 2 * i ) {
					System.out.print('*');
				} else {
					System.out.print(' ');
				}
				
			}
			System.out.print("\n");
		}
		for(int i = 1; i < num; i++) {
			for(int j = 1; j <= i; j++) {
				System.out.print(' ');
			}
			for(int j = 1; j <= num * 2  -2 * i - 1 ; j++) {
				if(j == 1 || j == num * 2  -2 * i - 1 ) {
					System.out.print('*');
				} else {
					System.out.print(' ');
				}					
			}
			System.out.print('\n');
		}
	}
}
```

## Java中的跳转控制语句

### break 

用于终止或跳出循环 以下是一个具体的代码案例 目的是记录生成的随机数50前一共生成的随机数次数

```java
import java.lang.Math;	//引入Math类	

public class Move{
	public static void main(String[] args) {
		int num = 0;
		while(true) {
			num++;
			if(((int)(Math.random() * 100) + 1) == 50) // 利用random() 生成1-100 的随机数
				break;			
		}	
		
		System.out.println(num);
	}
}
```

其中Math 类中的 random 方法

![45](../images/45.png)

#### break语句的使用细节

break 出现在多层嵌套的语句块中 ，可以通过标签 lable 指明需要终止的是那一层语句块

```java

public class Move{
	public static void main(String[] args) {
		lable1:
		for(int i = 0; i < 3; i++) {
			System.out.println("i = " + i);
			lable2:
			for(int j = 0; j < 2; j++) {
				System.out.println("j = " + j);
				if(j == 1) break lable1;
			}
		}		
	}
}
```

1.break 语句可以指定退出那一层

2.break 默认退出最近的一层

3.在实际开发中尽量不使用break + lable的组合，会降低代码的可读性

例题1：

输出1+2+3+...+100中到哪一个数字的时候和大于 20

```java

public class Move{
	public static void main(String[] args) {
		int sum = 0;
		for(int i = 1; i <= 100; i++) {
			sum += i;
			if(sum > 20) {
				System.out.println("最后的数字是：" + i);
				break;
			}
		}		
	}
}
```

例题2：登陆验证，限制登录次数

```java
import java.util.Scanner;

public class Move{
	public static void main(String[] args) {
		Scanner myScanner = new Scanner(System.in);
		for(int i = 3; i >= 1; i--) {
			System.out.println("输入姓名");		
			String userName = myScanner.next();
			System.out.println("输入密码");		
			String password = myScanner.next();
			if("丁真".equals(userName) && "123456".equals(password)){
				System.out.println("登录成功");	
				break;
			}else {
				System.out.println("登陆失败 剩余" + i + "次机会");	
			}
		}
		System.out.println("登录结束");		
	}
}
```

注意！！再java中比较字符串的时候需要使用到 String 方法的 equals()，不可以使用 == 来进行比较

在比较字符串的时候，建议将常量字符串先写在前面，这样做可以避免空指针的情况引起报错

```java
// if(userName.equals("丁真") && password.equals("123456"))
if("丁真".equals(userName) && "123456".equals(password))
```

### Java 中的字符串比较

在 Java 中，字符串比较需要使用 `String` 类的 `equals` 方法，而不能直接使用 `==` 运算符，原因是 **`==` 和 `equals` 的作用完全不同**：

------

1. #### `==` 的作用

- `==` 是 Java 中的**比较运算符**，用于比较两个对象的**引用（内存地址）**是否相同。
- 对于字符串（`String` 类型），`==` 比较的是两个字符串对象是否指向**同一个内存地址**，而不是比较字符串的内容是否相同。



```java
String str1 = new String("hello");
String str2 = new String("hello");

System.out.println(str1 == str2); // false，因为 str1 和 str2 是不同的对象
```

在上面的例子中，`str1` 和 `str2` 是两个不同的对象，尽管它们的内容都是 `"hello"`，但它们的**内存地址不同**，因此 `==` 返回 `false`。

------

2. #### `equals` 的作用

- `equals` 是 `String` 类中**重写**的方法，用于比较两个字符串的**内容**是否相同。
- 它会逐个字符比较两个字符串的值，而不是比较它们的内存地址。



```java
String str1 = new String("hello");
String str2 = new String("hello");

System.out.println(str1.equals(str2)); // true，因为内容相同
```

尽管 `str1` 和 `str2` 是不同的对象，但它们的内容相同，因此 `equals` 返回 `true`。

| 比较方式 | 作用范围 | 比较内容       | 适用场景                     |
| :------- | :------- | :------------- | :--------------------------- |
| `==`     | 对象引用 | 内存地址       | 判断两个对象是否是同一个实例 |
| `equals` | 对象内容 | 字符串的实际值 | 判断两个字符串的内容是否相同 |



#### 引申的相关问题

1.为什么==在比较数值的时候可以使用，不是比较的是地址嘛？

对于基本数据类型而言 == 比较的就是之的内容 ，而对于引用的内容而言比较的是是否是引用的同一个内容

2.那如果我将基本数据类型创建实例，存储的内容不再栈上，还可以使用==进行比较吗？

**基本数据类型**（如 `int`、`double`、`char` 等）是直接存储在栈内存中的，它们没有对象实例的概念。因此，你无法直接为基本数据类型创建实例。

但是，Java 提供了**包装类**（Wrapper Classes）来将基本数据类型封装为对象。例如：

- `int` 的包装类是 `Integer`
- `double` 的包装类是 `Double`
- `char` 的包装类是 `Character`

当你使用包装类时，存储的内容是在**堆内存**中，而不是栈内存中。这时，`==` 的行为会发生变化。

Java 对包装类（如 `Integer`、`Character` 等）做了一些优化：

- 对于较小的值（例如 `Integer` 在 -128 到 127 之间），Java 会使用**常量池**来缓存对象。
- 如果使用自动装箱（例如 `Integer a = 10;`），Java 会优先从常量池中获取对象，而不是创建新的对象。

```java
Integer x = 10; // 自动装箱，从常量池中获取对象
Integer y = 10; // 自动装箱，从常量池中获取对象

System.out.println(x == y); // true，因为 x 和 y 指向常量池中的同一个对象
```

在这个例子中，`x` 和 `y` 都指向常量池中的同一个对象，因此 `==` 返回 `true`。

但如果值超出了常量池的范围，`==` 就会返回 `false`：

```java
Integer m = 200; // 超出常量池范围，创建新对象
Integer n = 200; // 超出常量池范围，创建新对象

System.out.println(m == n); // false，因为 m 和 n 是不同的对象
```

| 情况                       | `==` 的行为                    | 推荐方法      |
| :------------------------- | :----------------------------- | :------------ |
| 基本数据类型               | 比较**值**                     | 直接使用 `==` |
| 包装类对象                 | 比较**引用（内存地址）**       | 使用 `equals` |
| 包装类对象（常量池范围内） | 可能比较**引用**（常量池优化） | 使用 `equals` |

### continue

和c语言中的类似，退出当前运行的代码块，执行循环中的下一次循环

也可以使用标签做 break类似的相关操作，默认执行最近的循环与语句

### return 

用于跳出所在的方法，在main 函数中使用退出程序

## 控制语句练习题目

1.求最大可以通过路口的次数

```java
import java.util.Scanner;

public class Move{
	public static void main(String[] args) {
		double money = 100000;
		int number = 0;
		while(true) {			
			if(money > 50000) {
				money *= 0.95;
			} else if(money <= 50000 && money >= 1000) {
				money -= 1000;
			} else {
				break;
			}
			number++;
		}

		System.out.println("最多可以通过 " + number + " 次路口"); 
	}
}
```

2.判断一个年份是否为闰年

```java
import java.util.Scanner;

public class Move{
	public static void main(String[] args) {
		Scanner myScanner = new Scanner(System.in);
		System.out.println("请输入一个年份");
		int year = myScanner.nextInt();

		if((year % 4 == 0 && year % 100 != 0)||(year % 400 == 0)) {
			System.out.println(year + "年份是一个闰年");
		} else {
			System.out.println(year + "年份不是是一个闰年");
		}
	}
}
```

3.判断一个水仙花数

```java
import java.util.Scanner;

public class Move{
	public static void main(String[] args) {
		Scanner myScanner = new Scanner(System.in);
		System.out.println("请输入一个三位数的水仙花数");
		int number = myScanner.nextInt();
		int sum = 0;
		int num1 = number / 100;
		int num2 = number % 100 / 10;
		int num3 = number % 10; 

		sum = num1 * num1 * num1 + num2 * num2 * num2 + num3 * num3 * num3;
		if(number == sum) {
			System.out.println(number + "是一个水仙花数");
		} else {
			System.out.println(number + "不是一个水仙花数");
		}
	}
}
```

这一份代码还可以进行一个代码优化，优化为支持判断所有位数的水仙花数

```java
import java.util.Scanner;

public class Move{
	public static void main(String[] args) {
		Scanner myScanner = new Scanner(System.in);
		System.out.println("请输入一个数");
		int number = myScanner.nextInt();
		int sum = 0;
		int flag = number;

		while(flag != 0) {			//当没有位数的时候停止
			int num = flag % 10;	//分离出当前数字的个位
			sum += (num * num * num);
			flag /= 10;	     	    //去除当前数字的个位
		}

		if(number == sum) {
			System.out.println(number + "是一个水仙花数");
		} else {
			System.out.println(number + "不是一个水仙花数");
		}
	}
}
```

4.输出1-100间不可以被 5 整除的数字，用5个一行来显示

```java
import java.util.Scanner;

public class Test{	
	public static void main(String[] args) {
		int count = 0;
		for(int i = 1; i <= 100; i++) {
			if(i % 5 != 0){
				System.out.print(i + " ");
				count++;
				if(count % 5 == 0){
					System.out.print("\n");
				}
			}
		} 
	}
}
```

5.运用 for 循环打印出字母表

```java
import java.util.Scanner;

public class Test{
	
	public static void main(String[] args) {
		for(char i = 'a'; i <= 'z'; i++) {			
			System.out.println((i));			
		} 
		for(char i = 'Z'; i >= 'A'; i--) {			
			System.out.println((i));			
		} 
	}
}
```

在这里考察的是char 也可以做自增操作，char的本质是 Unicode编码(UTF-8)

6.1 - 1/2 + 1/3 - 1/4 +1/5 - ...... - 1/100

```java
import java.util.Scanner;

public class Test{
	public static void main(String[] args) {
		double sum = 0;
		for(int i = 1; i <= 100; i++) {
			double temp = 0;
			if(i % 2 == 0) {
				temp = -1.0 / i;
			} else {
				temp = 1.0 / i;
			}
			sum += temp;
		}
		System.out.println("运算结果是" + sum);
	}
}

```

7.1 + (1+2) + (1+2+3) + ...... + （1+ ... +100）

```java
import java.util.Scanner;

public class Test{
	public static void main(String[] args) {
		int sum = 0, temp = 0;
		for(int i = 1; i <= 100; i++) {
			temp = 0;
			for(int j = 1; j <= i; j++) {
				temp += j; 
			}
			sum += temp;
		}
		System.out.println(sum);
	}
}

```

以上的代码可以正确完成功能，但是我发现了相加的每一项其实都是在前一项的基础上加上了它所对应的项数，通过一个temp变量保存前一个项的值即可让算法的复杂度降低，通过一个循环即可计算出结果，以下是代码的改进部分

```java
import java.util.Scanner;

public class Test{
	public static void main(String[] args) {
		int sum = 0, temp = 0;
		for(int i = 1; i <= 100; i++) {
			temp += i;
			sum +=temp;
		}
		System.out.println(sum);
	}
}
```
