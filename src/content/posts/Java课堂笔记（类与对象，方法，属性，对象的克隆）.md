---
title: Java笔记
published: 2025-03-06
updated: 2025-03-06
description: '类与对象，方法，属性，对象的克隆'
image: ''
tags: [Java]
category: 'Java'
draft: false 
---

# 听课笔记

## 杨辉三角例题

```java
public class Test{
	
	public static void main(String[] args){
		int arr[][] = new int[10][];
		for(int i = 0; i < 10; i++) {
			arr[i] =new int[i + 1];
			arr[i][0] = 1;
			arr[i][i] = 1;

			if(i > 1) {
				for(int j = 1;j <= i - 1; j++) {
					arr[i][j] = arr[i - 1][j] + arr[i - 1][j - 1];
				}
			}			
		}
		for(int i = 0; i < arr.length; i++) {
			for(int j = 0; j < arr[i].length; j++) {
				System.out.print(arr[i][j] +"\t");
			}
			System.out.println("");
		}	
	}
}

```

二维数组的声明有三种方式

```java
int[][] y;
int[] y[];
int y[][];
```

## 插入算法经典例题

```java
import java.util.*;

public class Test{	
	public static void main(String[] args){
		int arr[] = {10, 12, 45, 90};
		Scanner myScanner = new Scanner(System.in);
		int num = myScanner.nextInt();
		int table[] = new int[arr.length + 1];
	
		for(int i = 0; i < arr.length; i++) {
			if(num < arr[i]) {
				
				for(int j = 0; j < i; j++) {
					table[j] = arr[j];
				}
				table [i] = num;
				for(int j = i + 1; j < table.length; j++) {
					table[j] = arr[j - 1];
				}
				break;//我们只需要找到一次需要插入的位置即可
			}
		}
		arr = table;
		for(int i = 0; i < arr.length; i++) {
			System.out.print(arr[i] +" ");
		}
	}
}

```

原先的算法似乎有一些复杂，以下是一个改进的版本

```java
import java.util.*;

public class Test{
	public static void main(String[] args){
		int arr[] = {10, 12, 45, 90};
		Scanner myScanner = new Scanner(System.in);
		int num = myScanner.nextInt();
		int table[] = new int[arr.length + 1];
		int index = -1;
		for(int i = 0; i < arr.length; i++) {
			if(arr[i] > num) {
				index = i;
				break;
			}
		}
		for(int i = 0, j = 0; i < table.length; i++){
			if(i != index) {
				table[i] = arr[j];
				j++;
			} else {
				table[i] = num;
			}
		}
		arr = table;
		for(int i = 0; i < arr.length; i++) {
			System.out.print(arr[i] +" ");
		}
	}
}

```

## 综合例题

用数组存储10个随机数

```java
import java.util.*;

public class Test{
	
	public static void main(String[] args) {
		int arr[] = new int[10];
		int sum = 0;
		int max = 0;
		int index = -1;
		for(int i = 0; i < arr.length; i++) {
			arr[i] = (int)(Math.random() * 100) + 1;
		}
		System.out.println("正序打印");
		for(int i = 0; i < arr.length; i++) {
			System.out.print(arr[i] + " ");
			sum += arr[i];
			if(max < arr[i]) {
				max = arr[i];
				index = i;
			}
		}
		System.out.println("\n平均数为" + sum / 10);
		System.out.println("最大值是" + max + " 它的下标为" + index);
		//从大到小排序
		int temp = 0;
		for(int i = 0; i < arr.length - 1; i++) {
			for(int j = 0; j < arr.length - i - 1; j++) {
				if(arr[j] < arr[j + 1]) {
					temp = arr[j];
					arr[j] = arr[j + 1];
					arr[j + 1] = temp;
				}
			}
		}
		for(int i = 0; i < arr.length; i++) {
			System.out.print(arr[i] + " ");
		}

		int target = 30;
		for(int i = 0; i < arr.length; i++) {
			if(arr[i] == target) {
				System.out.println("找到了目标数" + target + "下表为" + i);
				break;
			}
			if(arr[i] < target) {
				System.out.println("没有找到");
				break;
			}
		}
		
	}
}

```

## Java 类与对象

终于来到了java中最为核心的部分内容，不同于c语言中的面向方法，java 的面向对象可以使得编程更加的贴近于现实。

```java
public class Object{
	public static void main(String[] args) {
		String cat1Name = "小白";
		int cat1Age = 3;
		String cat1Color = "蓝色"

		String cat2Name = "小花";
		int cat2Age = 2;
		String cat2Color = "红色"		
	}
}
```

以上的代码存储了猫猫的各个信息，但是这样做会使得，变量离散缺少内在的联系

这个时候会想到数组的结构来存储，但是数组不能存储不同数据类型的成员，全用字符串也没法体现数据含义，在用对于下表读取成员变量时，对应关系不明确

为解决以上问题我们引入的类与对象

一个事物，是由它的行为和属性构成的，

而对象是对于事物的一个抽象，在这样一个抽象之中，会有它的属性和行为

可以将上述的案例中的猫猫定义成为一种数据类型，这种类型中有猫的颜色，年龄，颜色，还可以有方法行为，这就是类的概念。

我们用这样的一个抽象的数据类型可以创建一个或者数个具体的对象，也就是猫的对应的个体，称之为实例。

用类来存放猫猫

```java
import java.util.Scanner;

public class Object{
	public static void main(String[] args) {
		//使用面向对象的方式来存放猫猫
		Cat cat1 = new Cat();
		cat1.name = "小花";
		cat1.age = 2;
		cat1.color = "红色";
		Cat cat2 = new Cat();
		cat2.name = "小白";
		cat2.age = 3;
		cat2.color = "白色";
	}
}

class Cat {
	//定义一个猫猫类
	String name;
	int age;
	String color;
}
```

用这样的方式可以方便的添加属性以及行为，也方便创建全新的猫猫实例

## 对象在内存中的存在形式

在创建一个对象实例的时候，对象的引用存放在 java虚拟机内存中的栈，而它所指向的属性会放在堆中（存放的形式是由对象的属性类型所决定的，基本数据类型的属性会放在堆中，而字符串类型的属性会在堆中存放其引用，而字符串内容会被放置在方法区的常量池中）

额外的在用 new 创建一个对象的时候会将类的信息加载到方法区（属性，方法）![47](../images/47.png)

## 属性 成员变量

从概念而言 成员变量 属性 字段都是用来表示类中的单个数据类型的，使用上是等价的

1.在修饰属性中我们可以加上访问修饰符public protected 默认 private

使用方式 访问修饰符 属性类型 属性名称

2.属性的定义类型可以是任意类型，包含基本类型，引用类型

3.属性如果不赋值，会有一些的默认值，规则同数组一样

默认赋值

```java
public class Object{
	public static void main(String[] args) {
		Person human = new Person();
		System.out.println("姓名 " + human.name + "\n年龄 " + human.age
		+ "\n薪水 " + human.salary + "\n是否通过 " + human.isPass);
	}
}

class Person {
	String name;
	int age;
	double salary;
	boolean isPass;
}
```

```
姓名 null
年龄 0
薪水 0.0
是否通过 false
```

## 补充知识

### 字符串常量池

字符串常量池是常量池的一部分，专门用于存储字符串字面量。它的设计目的是为了减少重复字符串的内存占用。

之前笔记中提到的 int 包装类 数据类型存放在-128 -- 127也会被放在常量池中

```java
Integer x = 10; // 自动装箱，从常量池中获取对象
Integer y = 10; // 自动装箱，从常量池中获取对象

System.out.println(x == y); // true，因为 x 和 y 指向常量池中的同一个对象
```



```java
String s1 = "Hello";  // "Hello" 会被放入字符串常量池
String s2 = "Hello";  // 直接从字符串常量池中获取
String s3 = new String("Hello");  // 在堆中创建一个新的字符串对象

System.out.println(s1 == s2);  // true，因为 s1 和 s2 指向常量池中的同一个对象
System.out.println(s1 == s3);  // false，因为 s3 是堆中的一个新对象
```

#### 字符串常量池的特点：

1. **字面量赋值**：

   - 使用双引号直接赋值的字符串（如 `"Hello"`）会被放入字符串常量池。

2. **`new String()`**：

   - 使用 `new String()` 创建的字符串对象会存储在堆中，而不是字符串常量池。

3. **`intern()` 方法**：

   - 可以将堆中的字符串对象放入字符串常量池，并返回常量池中的引用

   ```java
   String s4 = new String("World").intern();
   String s5 = "World";
   System.out.println(s4 == s5);  // true，因为 s4 通过 intern() 方法放入了常量池
   ```

## Java中的几个存储区域

堆：存放 对象 数组 等

栈：一般存放基本数据类型（局部变量）

方法区：常量池（常量，比如字符串）加载信息

## Java中创建对象的流程

```java
	Person human = new Person();
	temp.name = "thrinisty";
	temp.age = 10;
```

1.加载Person类信息到方法区

2.在堆中进行分配空间，进行默认的初始化

3.将地址赋值给类的引用

4.进行指定初始化,如上述的字符串赋值，和年龄赋值



## Java中的成员方法

```java
public class Object{
	public static void main(String[] args) {
		Person human;
		human = new Person();
		human.speak();//调用方法
	}
}
class Person {
	String name;
	int age;

	public void speak() {
		System.out.println("我是一个好人");
	}
}
```

在这里在 Person 类中定义了一个方法，输出了一段话，并在主函数中调用了这样一个方法

```java
public class Object{
	public static void main(String[] args) {
		Person human;
		human = new Person();
		human.speak();
		human.sum();
		System.out.println(human.add(10, 5));
	}
}
class Person {
	String name;
	int age;

	public void speak() {
		System.out.println("我是一个好人");
	}

	public void sum() {
		int result = 0;
		for(int i = 0; i < 1000; i++) {
			result += i;
		}
		System.out.println(result);
	}

	public int add(int count1, int count2) {
		int result = count1 + count2;
		return result;
	}
}
```

在Java中同样可以传入参数参与方法中的运算，在这里实现了一个加法的方法并将结果返回到了主函数中，在Java中我们在调用方法的时候和C语言类似

1. **保存当前指令地址**：
   - 在Java中，JVM会维护一个调用栈（Call Stack），用于跟踪方法调用。当一个方法被调用时，JVM会将当前的执行状态（包括程序计数器，即下一条指令的地址）保存在调用栈中。
2. **将方法参数压栈**：
   - Java方法的参数会被压入操作数栈（Operand Stack）中。JVM是基于栈的虚拟机，所有的操作（包括方法调用）都是通过操作数栈来完成的。参数会按照方法声明的顺序被压入栈中。
3. **调用方法**：
   - JVM会执行`invokevirtual`、`invokestatic`、`invokeinterface`或`invokespecial`等字节码指令来调用方法。这些指令会根据方法的类型（实例方法、静态方法、接口方法等）来决定如何调用方法。
   - 在调用方法时，JVM会创建一个新的栈帧（Stack Frame）并将其压入调用栈。这个栈帧包含了方法的局部变量表、操作数栈等信息。
4. **执行方法体**：
   - 方法体中的字节码指令会被逐条执行。JVM会根据指令对操作数栈进行操作，计算结果会存储在操作数栈中。
5. **返回结果**：
   - 当方法执行完毕后，JVM会将返回值压入调用者的操作数栈中。返回值的类型取决于方法的返回类型（`void`、`int`、`Object`等）。
6. **退栈**：
   - 方法执行完毕后，JVM会弹出当前方法的栈帧，恢复调用者的栈帧，并继续执行调用者的下一条指令。

其中与c的函数调用有一些区别

- **调用栈管理**：在C语言中，调用栈是由硬件和操作系统直接管理的，而在Java中，调用栈是由JVM管理的。
- **字节码与机器码**：Java方法调用是通过字节码指令（如`invokevirtual`）来完成的，而C语言中的函数调用是通过机器码指令（如`call`）来完成的。
- **内存管理**：Java有自动内存管理（垃圾回收），而C语言需要手动管理内存。

## 方法的妙用

可以提高代码的可读性，可以方便后续的维护，可以供用户方便调用

```java
public class Object{
	public static void main(String[] args) {
		int arr[][] = {{1, 3, 5}, {1, 6, 7}, {0, 3, 5}};
		Tool Mytool = new Tool();	
		Mytool.arrPrint(arr);
	}
}

class Tool {
	public void arrPrint(int table[][]) {
		for(int i = 0; i < table.length; i++) {
			for(int j = 0; j < table[i].length; j++) {
				System.out.print(table[i][j] + " ");
			}
			System.out.println();
		}
	}
}
```

## 方法的定义

```java
public void arrPrint(int table[][]) {
//方法修饰类型 + 返回类型 + 方法名称 + （形参列表）
//  {
//    	方法主体	    
//  }
		for(int i = 0; i < table.length; i++) {
			for(int j = 0; j < table[i].length; j++) {
				System.out.print(table[i][j] + " ");
			}
			System.out.println();
		}
	}
```

当要返回多个值的时候可以使用数组的返回类型

```java
public class Object{
	public static void main(String[] args) {
		Tool Mytool = new Tool();	
		int rst[] = new int[2];
		rst = Mytool.AddFunction(10, 15);
		System.out.println("+:" + rst[0]);
		System.out.println("-:" + rst[1]);
	}
}

class Tool {
	public int[] AddFunction(int n1, int n2) {
		int result[] = new int[2];
		result[0] = n1 + n2;
		result[1] = n1 - n2;
		return result;
	}
}
```

也可以使用自定义的类来进行返回

在这个例子中使用了自定义的 Result 结果类 将方法返回类型更改为Result即可

```java
public class Object{
	public static void main(String[] args) {
		Tool Mytool = new Tool();	
		Result rst = new Result();
		rst = Mytool.AddFunction(15, 10);
		System.out.println("+:" + rst.add);
		System.out.println("-:" + rst.sub);
	}
}

class Tool {
	public Result AddFunction(int n1, int n2) {
		Result result = new Result();
		result.add = n1 + n2;
		result.sub = n1 - n2;
		return result;
	}
}

class Result {
	int add;
	int sub;
}
```

方法不能嵌套定义

## 方法的调用

1.同一个类的方法可以直接调用

```java
public class Object{
	public static void main(String[] args) {
		A target = new A();
		target.print(19);
	}
}

class A {
	public void print(int n) {
		System.out.println(n);
	}
	public void Ok() {
		print(10);
	}
}
```

2.跨类的方法调用需要使用对象名进行调用

```java
public class Object{
	public static void main(String[] args) {
		A target = new A();
		target.print(19);
		target.UseB(1);
	}
}

class A {
	public void print(int n) {
		System.out.println(n);
	}
	public void Ok() {
		print(10);
	}
	public void UseB(int n) {
		B ClassB = new B();
		ClassB.print(n);
	}
}

class B {
	public void print(int n) {
		System.out.println("B类的方法：" + n);
	}
}
```

需要在 A 的方法后创建一个 B 的实例，再运用 B 实例的引用来调用 B 类的方法

## 方法练习题目

判断一个数是是偶数还是奇数

```java
public class Object{
	public static void main(String[] args) {
		AA target = new AA();
		if(target.Judge(40)) {
			System.out.println("是一个偶数");
		} else {
			System.out.println("是一个奇数");
		}		
	}
}

class AA {
	public boolean Judge(int n) {
		if(n % 2 == 0) {
			return true;
		} else {
			return false;
		}
	}
}
```

打印#字符

```java
public class Object{
	public static void main(String[] args) {
		AA target = new AA();
		target.Print(3, 5);	
	}
}

class AA {
	public void Print(int m, int n) {
		for(int i = 0; i < m; i++) {
			for(int j = 0; j < n; j++) {
				System.out.print("#");
			}
			System.out.println();
		}
	}
}
```

## 成员方法的传参机制

1.方法中基本类型参数的传递中不会改变被调用参数的值

```java
public class Object{
	public static void main(String[] args) {
		AA target = new AA();
		int m = 3, n = 5;
		target.Print(m, n);	
		System.out.println("主函数中的m n");
		System.out.println("m:" + m);
		System.out.println("n:" + n);
	}
}

class AA {
	public void Print(int m, int n) {
		int tmp = m;
		m = n;
		n = tmp;
		System.out.println("方法体中的m n");
		System.out.println("m:" + m);
		System.out.println("n:" + n);
	}
}
```

执行结果

```
方法体中的m n
m:5
n:3
主函数中的m n
m:3
n:5
```

可见调用方法内部的参数发生交换，打印结果改变

但是主函数中的实际参数没有发生交换，这是因为调用的方法中的参数并不是对于主函数中m n的引用，并不会对于主方法中的参数产生改变

2.方法中引用类型参数的传递中会改变被调用引用所指向的值

```java
public class Object{
	public static void main(String[] args) {
		int array[] = {1, 3, 6, 6};
		Tool MyTool = new Tool();
		MyTool.ChangeNum(array);
		MyTool.ArrPrint(array);
	}
}

class Tool {
	public void ChangeNum(int arr[]) {
		for(int i = 0; i < arr.length; i++) {
			arr[i] = 0;
		}
	}

	public void ArrPrint(int arr[]) {
		for(int i = 0; i < arr.length; i++) {
			System.out.print(arr[i] + " ");
		}
	}
}
```

结果是

```
0 0 0 0 
```

接下来这个例子非常容易犯错

```java
public class Object{
	public static void main(String[] args) {
		int array[] = {1, 3, 6, 6};
		Tool MyTool = new Tool();
		MyTool.ChangeNum(array);
		System.out.println(array[3]);
	}
}

class Tool {
	public void ChangeNum(int arr[]) {
		for(int i = 0; i < arr.length; i++) {
			arr[i] = i;
		}
		arr = null;
	}
}
```

问是否可以正常编译运行，我开始是这么想的，既然 arr 指向了空 ，那么代表了他没有办法正常的访问成员数据，那么在主函数中的6行就会发生报错

但是结果就是可以运行

```
6
```

这是因为运行的过程中虽然时指向了一个数组，利用方法体中的引用对于数组进行修改，但是原来主函数中的引用却没有消失，方法体中的引用设置空，不影响主方法的引用访问数组

## 对象的克隆

```java
public class Object{
	public static void main(String[] args) {
		Tool MyTool = new Tool();
		Person human = new Person();
		human.name = "Li Haoxuan";
		human.age = 22;
		Person newHuman;
		newHuman = MyTool.CloneClass(human);
		human.name = "Wang Ling";
		System.out.println(newHuman.name);
		//新的对象，改变原来的名字不会改变克隆的新人类
	}
}

class Tool {
	public Person CloneClass(Person human) {
		Person newHuman =new Person();
		newHuman.name = human.name;
		newHuman.age = human.age;
		return newHuman;
	}
}

class Person {
	String name;
	int age;
}
```

将方法中创建的空间以及内容对应的引用返回赋值主函数中的引用就可以完成克隆，满足用主函数的新引用来使用克隆出来的内容
