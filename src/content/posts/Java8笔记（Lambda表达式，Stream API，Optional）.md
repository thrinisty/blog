---
title: Java8笔记
published: 2025-04-20
updated: 2025-04-20
description: 'Lambda表达式，函数式接口，Stream API，Optional'
image: ''
tags: [Java]
category: 'Java'
draft: false 
---

# Java8笔记

拖了一天，本来打算从昨天就开始Java8特性的学习。但是有门事多题难分少的编译原理实验课把自己搞得心烦，晚上就没有精力学习了。

第二天还要体测，38度的高温，就隔这室外干站着，等半天跑1000米，跑前本来想着4分30以内及格就好，压着脚步跑，结果才4分37，还贼累，险些没给自己跑死。

到了期中以后了，感觉时间也是慢慢的变得紧张了起来，还是尽量多学一会把，刚回宿舍躺了一小会，算是复活了，总而言之开始今天的学习吧



## Java8

为什么要学习Java8？

Java8是当今企业中开发最主流的稳定版本，这个版本中出现了很多的新特性，虽然考点不多，但是可以提升Java后端开发程序员的编程效率，值得学习

相关知识有Lambda表达式、Stream API、Optional

计划加上今天晚上，明天，后天，一共三天完成相关知识的学习



## Lambda表达式

Lambda是一个匿名函数，我们可以把Lambda表达式理解为是一段可以传递的代码，使用它可以写出更为简洁灵活的代码，代码风格更加紧凑



### 代码示例

我们用一串代码举例子

多线程接口Runnable，实现一个类，重写run方法，start调用

```java
public class Test01 {
    public static void main(String[] args) {
        Cat cat = new Cat();
        Thread thread = new Thread(cat);
        thread.start();
    }
}

class Cat implements Runnable {
    @Override
    public void run() {
        System.out.println("cat");
    }
}
```

我们就是用一次这个猫猫类，我们可以稍微用匿名内部类简写一点

```java
public class Test01 {
    public static void main(String[] args) {
        Runnable runnable = new Runnable() {
            @Override
            public void run() {
                System.out.println("cat");
            }
        };
        Thread thread = new Thread(runnable);
        thread.start();
    }
}
```

接下来我们使用以下Lambda表达式来进行改造

```java
public class Test01 {
    public static void main(String[] args) {
        Runnable runnable = () -> System.out.println("cat");
        Thread thread = new Thread(runnable);
        thread.start();
    }
}
```



再举一个例子，比较类的实现

```java
public class Test01 {
    public static void main(String[] args) {
        Comparator<Integer> comparator = new Comparator<Integer>() {
            @Override
            public int compare(Integer o1, Integer o2) {
                return Integer.compare(o1, o2);
            }
        };
        System.out.println(comparator.compare(1,3));
    }
}
```

Lambda改造

```java
public class Test01 {
    public static void main(String[] args) {
        Comparator<Integer> comparator = (Integer o1, Integer o2) -> Integer.compare(o1, o2);
        System.out.println(comparator.compare(1,3));
    }
}
```

方法引用

```java
public class Test01 {
    public static void main(String[] args) {
        Comparator<Integer> comparator = Integer :: compare;
        System.out.println(comparator.compare(1,3));
    }
}
```

可以直接便捷的实现一个类的实例，如此方便，很人性化不是吗

其中 -> 就是Lambda操作符，在实际操作中这么写非常的快速，而且很帅，很装



### 具体使用

**使用说明**

```java
(形参列表) -> 重写的抽象方法体
```

-> 左边Lambda形参列表（是接口中抽象方法的形参列表）

-> 右边是Lambda体（是重写的抽象方法的方法体）



Lambda表达式在Java中的本质是接口的实例（万事万物皆对象）

Lambda表达式的使用，分为六种情况

1.无参，无返回结果

```java
() -> {重写方法体}
```

```java
Runnable runnable = () -> System.out.println("cat");
```



2.需要一个参数，没有返回值

```java
(类型 名称) -> {重写方法体}
```

```java
Consumer<String> con = (String s) -> {System.out.println(s);}
```



3.数据类型可以省略，因为可以由编译器推断得出

```java
(名称) -> {重写方法体}
```

```java
Consumer<String> con = (s) -> {System.out.println(s);}
```



4.若Lambda只需要一个参数，参数小括号也可以省略

```
名称 -> {重写方法体}
```

```java
Consumer<String> con = s -> {System.out.println(s);}
```



5.Lambda需要两个或以上的参数，执行多条语句，有返回值

```java
(类型 名称, 类型 名称, ...) -> {重写方法语句; return ...;}
```

```java
Comparator<Integer> comparator = (Integer o1, Integer o2) -> {
    System.out.println(o1);
    System.out.println(o2);
    return Integer.compare(o1, o2);}
```



6.当Lambda只有一条语句的时候，大括号可以省略，对return语句return也不用写

```
(类型 名称, 类型 名称, ...) -> [return] 重写方法体;
```

```java
Comparator<Integer> comparator = (Integer o1, Integer o2) -> Integer.compare(o1, o2);
```

Lambda表达式的本质实际上是接口的实例，依赖于函数式接口的实现



## 函数式接口

如果一个接口中只有一个方法声明，那么这个接口就是函数式接口，可以使用Lambda表达式来进行代码优化（简化）

接口一般用注解@FunctionalInterface来标示，只允许声明一个方法

```java
public class Test01 {
    public static void main(String[] args) {
        Test test = (String o) -> {
            System.out.println(o);
        };
        test.write("Jerry");
    }
}

@FunctionalInterface
interface Test{
    void write(String str);
}
```

所以匿名内部类实例的创建，都可以用Lambda表达式来创建，在java.utils.function包下定义了Java8的函数式接口



### 内置核心函数式接口

| 函数式接口              | 参数类型 | 返回类型 | 描述                  |
| ----------------------- | -------- | -------- | --------------------- |
| 消费型接口Consumer<T>   | T        | void     | 方法void accept(T t)  |
| 供给型接口Supplier<T>   | 无       | T        | 方法T get()           |
| 函数型接口Function<T,R> | T        | R        | 方法R apply(T t)      |
| 断定型接口Predicate<T>  | T        | boolean  | 方法boolean test(T t) |

#### Consumer<T>

```java
public class Test01 {
    public static void main(String[] args) {
        Consumer<String> comsumer = (s) -> System.out.println(s);
        comsumer.accept("Jerry");
    }
}
```

#### Supplier<T>

```java
public class Test01 {
    public static void main(String[] args) {
        Supplier<Integer> supplier = () -> new Integer(13);
        System.out.println(supplier.get());
    }
}
```

#### Function<T,R>

```java
public class Test01 {
    public static void main(String[] args) {
        Function<Character, Integer> function = (s) -> new Integer(s);
        System.out.println(function.apply('a'));
    }
}
```

#### Predicate<T>

```java
public class Test01 {
    public static void main(String[] args) {
        Predicate<String> predicate = (s) -> {
            if ("hello".equals(s)) {
                return true;
            }
            return false;
        };
        System.out.println(predicate.test("he"));
    }
}
```



### 方法引用

当要传递给Lambda体的操作以及有实现的方法了，就可以使用方法引用

要求：实现接口的抽象方法的参数列表和返回值的类型，必须和方法引用的参数列表和返回值保持一致

格式：使用"::"将类与方法名隔开



使用场景

当接口中的抽象方法的形参列表和返回值与方法引用的方法的形参列表和返回值类型相同

#### 情况一

对象::实例方法

Consumer 中的 void accept(T t)

PrintStream 中的 void println(T t)

```java
public class Test02 {
    public static void main(String[] args) {
        Consumer<String> consumer = str -> System.out.println(str);
        consumer.accept("Hello world");
    }
}
```

类对象为System.out 实例方法是println

```java
public class Test02 {
    public static void main(String[] args) {
        Consumer<String> consumer = System.out :: println;
        consumer.accept("Hello world");
    }
}
```



#### 情况二

类::静态方法

```java
public class Test02 {
    public static void main(String[] args) {
        Comparator<Integer> comparator = (o1, o2) -> Integer.compare(o1, o2);
        System.out.println(comparator.compare(1,3));
    }
}
```

```java
public class Test01 {
    public static void main(String[] args) {
        Comparator<Integer> comparator = Integer :: compare;
        System.out.println(comparator.compare(1,3));
    }
}
```



#### 情况三

类::实例方法

前两种使用方式要求：接口中的抽象方法的形参列表和返回值与方法引用的方法的形参列表和返回值类型相同

而第三种方式可以不严格要求如上的条件

```java
public class Test02 {
    public static void main(String[] args) {
        Comparator<String> comparator = (s1, s2) -> s1.compareTo(s2);
        System.out.println(comparator.compare("adf", "dgb"));
    }
}
```

改造为

```java
public class Test02 {
    public static void main(String[] args) {
        Comparator<String> comparator = String :: compareTo;
        System.out.println(comparator.compare("adf", "dgb"));
    }
}
```





### 构造器引用

和方法引用类似，函数式接口的抽象方法的形参类型和构造器的形参列表一致

通过构造器引用可以简化接口返回对应类对象的使用

```java
public class Test02 {
    public static void main(String[] args) {
        Supplier<Employee> emp = () -> new Employee();
        System.out.println(emp.get());
    }
}

class Employee {
    @Override
    public String toString() {
        return "Employee";
    }
}
```

```java
public class Test02 {
    public static void main(String[] args) {
        Supplier<Employee> emp = Employee :: new;
        System.out.println(emp.get());
    }
}
```

再举一个例子

```java
public class Test02 {
    public static void main(String[] args) {
        Function<Integer, Employee> emp = (id) -> new Employee(id);
        System.out.println(emp.apply(123));
    }
}

class Employee {
    private int id;

    public Employee(int id) {
        this.id = id;
    }

    @Override
    public String toString() {
        return "Employee " + id;
    }
}
```

```java
public class Test02 {
    public static void main(String[] args) {
        Function<Integer, Employee> emp = Employee :: new;
        System.out.println(emp.apply(123));
    }
}
```



### 数组引用

```java
public class Test02 {
    public static void main(String[] args) {
        Function<Integer, String[]> emp = (id) -> new String[id];
        String[] arr = emp.apply(10);
        System.out.println(arr.length);
    }
}
```

```java
public class Test02 {
    public static void main(String[] args) {
        Function<Integer, String[]> emp = String[] :: new;
        String[] arr = emp.apply(10);
        System.out.println(arr.length);
    }
}
```



## Stream API

### 前置知识

Stream API (java.util.stream)把真正的函数式编程风格引入到Java中，可以用Stream API写出高效、干净、简洁的代码

Stream是Java8中处理集合的关键抽象概念，可以指定你希望对集合进行的操作，可以执行非常复杂的查找，过滤和映射数据操作，类似于SQL对数据库的操作（过滤，排序，映射，规约）

Stream和Collection集合的区别：Collection是一种静态的内存数据结构，而Stream是有关计算的，前者主要面向内存（存储在内存），后者主要面向CPU（通过CPU计算）



### 注意要点

1.Stream自己不会存储元素

2.Stream不会改变源对象，而会返回一个持有结果的Stream

3.Stream操作是延迟执行的，意味着他们会等到需要结果的时候才会执行

大体上分为三个步骤 1.创建流 2.中间操作 3.终止操作



### Stream创建

#### 获取集合流对象

Java8中的Collection接口被扩展，提供两个获取流的方法

default Stream<E> stream() : 返回一个顺序流

default Stream<E> parallelStream() : 返回一个并行流



我们现在有如下的ArrayList集合对象

```java
public class Stream1 {
    public static void main(String[] args) {
        List<Employee> list = new ArrayList<>();
        Employee person1 = new Employee("黎明", 18);
        Employee person2 = new Employee("丽丽", 11);
        Employee person3 = new Employee("黎明", 18);
        list.add(person1);
        list.add(person2);
        list.add(person3);
        System.out.println(list.size());
    }
}

class Employee {
    String name;
    int age;

    public Employee(String name, int age) {
        this.name = name;
        this.age = age;
    }
}
```

通过两个方法获取流

```java
Stream<Employee> stream = list.stream();
//获取顺序流（按照顺序取）
Stream<Employee> employeeStream = list.parallelStream();
//获取并行流（按照多个线程取值）
```



#### 获取数组流对象

以下是一个数组

```java
public class Stream1 {
    public static void main(String[] args) {
        Employee[] employee = new Employee[3];
        Employee person1 = new Employee("黎明", 18);
        Employee person2 = new Employee("丽丽", 11);
        Employee person3 = new Employee("黎明", 18);
        employee[0] = person1;
        employee[1] = person2;
        employee[2] = person3;
    }
}

class Employee {
    String name;
    int age;

    public Employee(String name, int age) {
        this.name = name;
        this.age = age;
    }
}
```

```java
Stream<Employee> stream = Arrays.stream(employee);
//通过Arrays的静态方法调用stream创建流实例
```



#### 静态方法创建流

用于直接用数据创建流

```java
Stream<Employee> stream = Stream.of(person1, person2, person3);
```

无限流

迭代

```java
Stream<Integer> limit = Stream.iterate(0, t -> t + 2).limit(10);
```

```java
Stream.iterate(0, t -> t + 2).limit(10).forEach(System.out :: println);
```

生成

```java
Stream<Double> limit = Stream.generate(Math::random).limit(10);
```

```java
Stream.generate(Math :: random).limit(10).forEach(System.out :: println);
```



### Stream中间操作

#### 筛选与切片

##### filter 筛选流

```java
Stream<Employee> stream = list.stream();
Stream<Employee> employeeStream = stream.filter(e -> e.age > 11);
employeeStream.forEach(System.out :: println);
```

用filter传入一个判断的Predicate函数式接口，筛选出年龄大于11的Employee对象

再用终结操作forEach输出结果，这也是一个方法引用



##### limit 截断流

只保留几个数据

```java
Stream<Employee> stream = list.stream();
Stream<Employee> employeeStream = stream.limit(2);
employeeStream.forEach(System.out :: println);
```

注意这里一旦执行了forEach流就结束，不可以再使用中间操作



##### skip 跳过元素

跳过前面几个数据

```java
Stream<Employee> stream = list.stream();
Stream<Employee> employeeStream = stream.skip(1);
employeeStream.forEach(System.out :: println);
```



##### distinct 筛选

和数据库筛选一样，去重

```java
Stream<Employee> stream = list.stream();
Stream<Employee> employeeStream = stream.distinct();
employeeStream.forEach(System.out :: println);
```



#### 映射

##### map(Function f)

接收一个函数作为参数将元素转化为其他形式提取信息（类比于add）

将List中字符串大写

```java
List<String> list = Arrays.asList("aa", "bb", "cc", "dd");
Stream<String> stream = list.stream();
stream.map(str -> str.toUpperCase()).forEach(System.out :: println);
```

过滤出名字长度大于2的名字（先用map取出name，再用filter过滤）

```java
Stream<Employee> stream = list.stream();
stream.map(e -> e.name).filter(str -> str.length() > 2).forEach(System.out :: println);
```



##### flatMap(Function f)

将流中的每一个值都换成另一个流，再将所有的流连接成一个流（类比于addALL）

和上面的map相比将每个元素取出，转换后再连接

在对于集合里面嵌套集合的时候使用方便



#### 排序

##### sorted()

产生一个新流，其中按自然顺序排序（对于实现Comparable接口的对象可以使用）

```java
Stream<Employee> stream = list.stream();
stream.sorted().forEach(System.out :: println);
```

按照类中的接口方法排序

```java
@Override
public int compareTo(Object o) {
    Employee e = (Employee) o;
    return this.age - e.age;
}
```



##### sorted(Comparator com)

产生一个新流，其中按比较器顺序排序

```java
Stream<Employee> stream = list.stream();
stream.sorted(
        (e1, e2) -> {
            return e1.age - e2.age;
        }
).forEach(System.out :: println);
```



### Stream终止操作

#### 匹配查找

allMatch

检查是否匹配所有元素

```java
Stream<Employee> stream = list.stream();
boolean b = stream.allMatch(e -> e.age > 15);
System.out.println(b);
```

anyMatch

检查一个匹配

```java
Stream<Employee> stream = list.stream();
boolean b = stream.allMatch(e -> e.age > 15);
System.out.println(b);
```

noneMatch

检查是否没有匹配元素，例如是否有员工姓“尚”

```java
Stream<Employee> stream = list.stream();
boolean b = stream.noneMatch(e -> e.name.startsWith("尚"));
System.out.println(b);
```

findFirst

返回第一个元素

```java
Stream<Employee> stream = list.stream();
Optional<Employee> first = stream.findFirst();
System.out.println(first);
```

findAny

返回任意一个元素

```java
Stream<Employee> stream = list.stream();
Optional<Employee> first = stream.findAny();
System.out.println(first);
```

count

求个数

```java
Stream<Employee> stream = list.stream();
long count = stream.count();
System.out.println(count);
```

min

返回元素最小值（要求传入Comparator）

```java
Stream<Employee> stream = list.stream();
Optional<Employee> min = stream.min(
        (e1, e2) -> {
            return e1.age - e2.age;
        }
);
System.out.println(min);
```

max

返回元素最大值（要求传入Comparator）

```java
Optional<Employee> max = stream.max(
        (e1, e2) -> {
            return e1.age - e2.age;
        }
);
System.out.println(max);
```

forEach

内部迭代

传入Consumer c，以下是经典的输出遍历

```java
Stream<Employee> stream = list.stream();
stream.forEach(System.out :: println);
```



#### 规约

reduce(T identity, BinaryOperator)

可以将流中的元素反复结合起来，得到一个值，返回这个值

计算1-10的和（其中0为初始值）

```java
public class Stream1 {
    public static void main(String[] args) {
        List<Integer> list = Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8, 9, 10);
        Integer reduce = list.stream().reduce(0, (i1, i2) -> i1 + i2);
        //Integer reduce = list.stream().reduce(0, Integer::sum);
        System.out.println(reduce);
    }
}
```

reduce(BinaryOperator)

计算所有员工年龄总和

```java
Stream<Employee> stream = list.stream();
Optional<Integer> reduce = stream.map(e -> e.age).reduce(Integer::sum);
System.out.println(reduce);
```



#### 收集

collect(Collector c)

将流转换为其他形式，接收一个Collector接口的实现，用于给Stream中元素做汇总的方法

放入List中

```java
List<Employee> collect = stream.sorted(
        (e1, e2) -> {
            return e1.age - e2.age;
        }
).collect(Collectors.toList());
```

放入Set中

```java
Set<Employee> collect = stream.sorted(
        (e1, e2) -> {
            return e1.age - e2.age;
        }
).collect(Collectors.toSet());
```



## Optional类

### 基本介绍

之前在使用Stream API的时候使用到了Optional类来存储流输出的对象

Optional<T>是一个容器类，他可以保存类型T的值，代表这个值存在，或者保存null，表示这个值不存在

原本的null表示一个值不存在，现在Optional可以更好地表示这个概念，并避免空指针异常

Optional类的Javadoc描述如下：这是一个可以为null的容器对象，如果值存在则isPresent()方法返回true，调用get()方法会返回该对象

```java
public final class Optional<T> {
    private static final Optional<?> EMPTY = new Optional<>();
    private final T value;
```



### 相关方法

#### 创建方法

Optional.of(T t)

创建一个Optional实例，t必须非空



Optional.empty()

创建一个空的Optional实例



Optional.ofNullable(T t)

创建一个Optional实例，t可以为空



#### 判断包含

boolean isPresent()

判断是否包含对象



void isPresent(Consumer<? super T> consumer)

如果包含对象，就执行Consumer接口的实现代码，并且将该值作为参数传递给它



#### 获取对象

T get()

如果调用对象包含值，则返回该值，否则抛出异常



T orElse(T other)

如果有值则将其返回，否则返回指定的other对象



T orElseGet(Supplier<? extends T> other)

如果有值则返回，否则调用Supplier接口实现提供的对象



T orElse Throw(Supplier<? extends X exceptionSupplier>)

如果有值则将其返回，否则抛出由Supplier接口实现提供的异常



### 代码示例

在没有Optional类的时候，我们需要通过if判断语句判断对象不为空，再去调用方法，防止空指针异常，我们在学了Optional类后我们就可以用Optional去包装类

```java
Optional<Employee> optional = Optional.of(new Employee("李", 10));
optional.orElse(new Employee("王", 10)).toString();
```

如果没有数据，就用新创建的Employee，调用相关方法

Optional其实在实际使用上没有多少的内容，主要是有一些底层框架上会使用到Optional来返回结果，懂得如何操作数据对象，看得懂相关操作即可。
