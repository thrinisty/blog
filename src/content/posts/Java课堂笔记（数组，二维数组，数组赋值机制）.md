---
title: Java笔记
published: 2025-03-05
updated: 2025-03-05
description: '数组，二维数组，数组赋值机制'
image: ''
tags: [Java]
category: 'Java'
draft: false 
---

# 听课笔记

## Java中的数组

array 数组，在要处理大量数据的时候，引用数组的意义就非常重要了

数组的定义：数组可以存放多个同一类型的数据，数组也是一种数据类型，是引用类型

使用场景：例如有很多的 hens 的时候 可以使用数组大量存储hens的数据，还可以通过for 循环来进行进行数据的批量处理

### 数组的静态初始化

```java
import java.util.Scanner;

public class Move{
	public static void main(String[] args) {
		double hens[] = {3, 5, 1, 3.4, 2, 50};
        /*
        等价于 double hens[] = new double[6];
        hens[0] = 3;
        ......
        hens[5] = 50;
        */
		double sum = 0;
		for(int i = 0; i < 6; i++) {
			sum += hens[i];
		}
		System.out.println(sum);
	}
}
```

## 数组的使用方式

### 数组的动态初始化

数据类型 数组名称[ ] = new 数据类型 [ 大小 ]

```java
int a[] = new int[5];
```

```java
import java.util.Scanner;

public class Move{
	public static void main(String[] args) {
		Scanner myscanner = new Scanner(System.in);
		double sum = 0;
		double grade[] = new double[5];
		for(int i = 0; i < 5; i++) {
			System.out.println("请输入第" + (i+1) +"个成绩");
			grade[i] = myscanner.nextInt();
			sum += grade[i];
		}
		System.out.println("总和是" + sum);
	}
}
```

也可以先声明，在创建数组的实例

```java
int a[];		//这个时候数组指向空
a = new int[5];	//在这里分配了数组对应的空间，再将数组指向了这个空间
```

数组使用的注意事项

1.数组元素可以是基本类型和引用类型，java不支持数组数据类型混用

2.数组创建后如果没有赋值，会默认赋初始值

```java
/**
 *int short byte long 0
 *double float 0.0
 *char \u0000
 *boolean false
 *String null
*/
```

3.使用数组的步骤，1.声明数组 2.分配空间 3.给数组的各个空间赋值 4.使用数组

4.数组属于引用类型，数组型数据是对象（object）

运用 java 数组存储二十六个字母

```java

public class Test{
	
	public static void main(String[] args){
			
		char table[] = new char[26];
		for(int i = 0; i < 26; i++) {
			table[i] = (char)('a' + i);
		}	
		for(int i = 0; i < 26; i++) {
			System.out.println(table[i]);
		}
	}
}

```

注意在这里用到了java中数据类型转换的知识，'a' + i 是字符型加整形，会自动类型转化为int型，不能直接赋值给 char 型的变量，需要强制类型转换

习题练习

找到一个数组中最大的拿一个元素，并输出

```java

public class Test{	
	public static void main(String[] args){
		int table[] = {2, 3, 5, 1, 4} ;
		int max = 0;
		for(int i = 0; i < 5; i++) {
			max = max > table[i] ? max : table[i];
		}
		System.out.println(max);
	}
}
```

## 数组赋值机制

在基本数据类型赋值的时候，会赋给具体的数据，且互不影响

而在数组再默认情况下赋值是引用的传递，赋给另一个数组的是数组各个元素所对应的地址

代码举例

```java
public class Test{
	
	public static void main(String[] args){
		int i = 1;
		int j = i;
		j = 3;

		System.out.println(i);//改变j的值不会影响到i的值
		
		int table_1[] = {2, 3, 5, 1, 4} ;
		int table_2[] = new int[5];
		table_2 = table_1;
		table_2[0] = 1;

		System.out.println(table_1[0]);//改变table2 会影响到table1指向的内存空间
	}
}

```

如果需要一个全新的数据空间相互独立的数组，我们则需要一个数组拷贝

可以通过for循环赋值

```java

public class Test{
	
	public static void main(String[] args){
		int table_1[] = {2, 3, 5, 1, 4} ;
		int table_3[] = new int[table_1.length];
		for(int i = 0; i < table_1.length; i++) {
			table_3[i] = table_1[i];
		}
		table_3[0] = 1;

		System.out.println(table_1[0]);//改变table3 不会影响到table1指向的内存空间
	}
}

```

## 数组操作例题

1.将数组的内容反向保存

方式1：通过遍历数组的一半利用下标对于数组的各个元素收尾互换

```java
public class Test{
	
	public static void main(String[] args){
		int table[] = {2, 3, 5, 1, 4, 6};
		int temp = 0;
		for(int i = 0; i < table.length / 2; i++) {
			temp = table[i];
			table[i] = table[table.length - i - 1];
			table[table.length - i - 1] = temp;
		}

		for(int i = 0; i < table.length; i++) {
			System.out.println(table[i]);
		}
	}
}
```

方式2：创建一个新的数组，遍历原数组将原数组中的元素反向存入新的数组

```java

public class Test{
	
	public static void main(String[] args){
		int table[] = {2, 3, 5, 1, 4, 6};
		int temp = 0;
		int table_2[] = new int[6];
		for(int i = 0; i < table_2.length; i++) {
			table_2[i] = table[table_2.length - i - 1]; 
		}

		table = table_2; //在这里将table指向了新的数组，原来的空间因为没有变量引用被销毁
		for(int i = 0; i < table.length; i++) {
			System.out.println(table[i]);
		}
	}
}
```

## 数组的扩容与缩减

Java 中数组的长度是固定的，但是可以通过创建新数组的方式来将原数组的引用指向新数组的地址，以此来完成数组的扩容

接下来用一段代码来完成数组的扩容

```java
import java.util.*;

public class Test{
	
	public static void main(String[] args){
		Scanner myScanner = new Scanner(System.in);

		int table[] = {2, 3, 5, 1, 4, 6};
		while(true) {
			char flag = 'n';
			System.out.println("请输入是否要添加一个元素，需要填 y ");
			flag = myScanner.next().charAt(0);
			if(flag == 'y') {
				int num = myScanner.nextInt();
				int arr[] = new int[table.length + 1];
				for(int i = 0; i < table.length; i++) {
					arr[i] = table[i];
				}
				arr[table.length] = num;
				table = arr;
			} else {
				break;
			}
		}

		for(int i = 0; i < table.length; i++) {
			System.out.println(table[i]);
		}	
	}
}
```

同理我们也可以用类似的方式完成数组长度的缩减

```java
import java.util.*;

public class Test{
	
	public static void main(String[] args){
		Scanner myScanner = new Scanner(System.in);

		int table[] = {2, 3, 5, 1, 4, 6};
		while(true) {
			char flag = 'n';
			System.out.println("请输入是否要删除一个元素，需要填 y ");
			flag = myScanner.next().charAt(0);
			if(flag == 'y') {
				if(table.length == 1) {
					System.out.println("只剩下一个元素，无法删除");
					break;
				}
				int arr[] = new int[table.length - 1];
				for(int i = 0; i < table.length - 1; i++) {
					arr[i] = table[i];
				}
				table = arr;
			} else {
				break;
			}
		}

		for(int i = 0; i < table.length; i++) {
			System.out.println(table[i]);
		}	
	}
}
```

但是这样做太过于麻烦，可以使用后续的链表数据结构完成预期的功能

## 冒泡排序

经典的排序算法，在这里不过多介绍

```java
import java.util.*;

public class Test{	
	public static void main(String[] args){
		
		Scanner myScanner = new Scanner(System.in);
		System.out.println("请输入数组的大小");
		int number = myScanner.nextInt();
		int table[] = new int[number];		
		System.out.println("请输入数组的各个元素");
		for(int i = 0; i < number; i++) {
			table[i] = myScanner.nextInt();
		}
		for(int i = 0; i < table.length; i++) {
			System.out.print(table[i] + " ");
		}
		int temp = 0;
		for(int i = 0; i < table.length - 1; i++) {
			for(int j = 0; j < table.length - i - 1; j++) {
				if(table[j] > table[j + 1]) {
					temp = table[j];
					table[j] = table[j + 1];
					table[j + 1] = temp;
				}
			}
		}
		System.out.print('\n');
		for(int i = 0; i < table.length; i++) {
			System.out.print(table[i] + " ");
		}	
	}
}
```

数组中的查找

```java
import java.util.*;
public class Test{	
	public static void main(String[] args){
		Scanner myScanner = new Scanner(System.in);
		String table[] = {"朱雀", "白虎", "玄武", "青龙"};
		System.out.println("输入你想找到的元素");
		String target = myScanner.next();
		for(int i = 0; i < table.length; i++) {
			if(table[i].equals(target)) {
				System.out.println("找到了" + target + "位置在第" + i + "个元素上");
				return;
			}
		}
		System.out.println("没有找到目标的元素" + target);
	}
}
```

## Java中的二维数组

二维数组数数组的扩展，相比于数组，二维数组多了一个维度，可以模拟更加复杂的现实情况，在编程中有广泛的应用

以下是一个二维数组静态初始化的例子

```java
public class Test{	
	public static void main(String[] args){
		int arr[][] = { {1, 1, 5, 6, 6}, 
				  	   {3, 5, 5, 6, 6}, 
				  	   {3, 5, 5, 6, 2}, 
				  	   {3, 5, 5, 6, 1}};

		for(int i = 0; i < arr.length; i++) {
			for(int j = 0; j < arr[i].length; j++) {
				System.out.print(arr[i][j]);
			}
			System.out.println("");
		}
	}
}
```

在第二个循环条件中 判断的是 对应的一维数组的长度

这里有一个比较有意思的点，这里涉及到了一个存储方式的问题，以前我在c语言中没有搞明白，为什么列优先的遍历二维数组的方式会比行优先的方式要慢很多

在这一段代码中可以直观地看出二维数组的存储方式是通过先行再列的方式存储在内存之中，在内存中我们存储的方式是一维的，依次访问可以通过一次内存中的遍历得到想要的所有元素

而列优先的话，会导致内存的访问不连续，增加了缓存未命中的概率（这里涉及到了计算机组成原理中的 cache，缓存会从内存中加载数据用以程序的计算，而使用cache的速度是要远远快于使用内存中的数据的，假设cache目前存储的是{1, 1, 5, 6, 6 }第一个一维数组的元素，运用行优先的方式，可以一次加载cache 中的内容，而列优先的话还需要从第二个一维数组中取元素，而第二个数组不再cache中，读内存需要大量的时间浪费），从而导致了速度缓慢



## 二维数组的内存布局

有点类似于操作系统中的三级虚拟内存，二维数组的引用指向于一个存放一维数组引用的数组，数组的各个元素指向对应的一维数组，一维数组中才会存放需要的具体数据

![46](../images/46.png)

## 二维数组的使用

类型 [ ] [ ] = new 类型 [ size ] [ size ];

```java
int arr[][] = new int[3][2]; //定义一个二维数组

int arr[][]; //也可以先定义引用，再创建二维数组的空间
arr =  new int[3][2];
```

java中支持二维数组中的每个一维数组元素数量不同（列数不确定）

```java
import java.util.*;

public class Test{
	
	public static void main(String[] args){
		int arr[][];		//声明一个二维数组的引用
		arr = new int[3][]; //先给二维数组的引用数组创建实例 没有创建存放数据包的一维数组

		for(int i = 0; i < arr.length; i++) {
			arr[i] = new int[i + 1];//在这里才对于一维数组中的引用，创建了每一个一维数组的空间
			for(int j = 0; j < arr[i].length; j++) {
				arr[i][j] = i + 1;
			}
		}
		
		for(int i = 0; i < arr.length; i++) {
			for(int j = 0; j < arr[i].length; j++) {
				System.out.print(arr[i][j]);
			}
			System.out.println("");
		}
	}
}
```

课有点多，加上晚上要健身，今天先到这里了，明天开始会复习二维数组和相关例题，以及学习java中的类和对象
