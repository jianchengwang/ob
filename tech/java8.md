---
title: java8
slug: java8
author: [jianchengwang]
date: 2019-06-09
excerpt: "Java8从2014年发行到现在已经有五个年头了，之前公司的项目都还是基于Java7，所以对Java8不甚了解。于是乎，花一两个礼拜学习下，做个记录，以下部分文字复制于其他文章，最底下会列出相关链接，感兴趣的可以去了解下。"
draft: false
tags: [java]
---

**Java8** 从2014年发行到现在已经有五个年头了，之前公司的项目都还是基于Java7，所以对 **Java8** 不甚了解。于是乎，花一两个礼拜学习下，做个记录，以下部分文字复制于其他文章，最底下会列出相关链接，感兴趣的可以去了解下。

## JAVA8 的发展

### JDK 5

#### 自动装箱与拆箱

JDK1.5为每一个基本数据类型定义了一个封装类。使java中的基本数据类型也有自己的对象

```java
int -->Integer
double --> Double
long --> Long
char --> Character
float --> Float
boolean --> Boolean
short --> Short
byte -- > Byte
```

- 自动装箱：将基本类型转换成为对象，例如：`int --> Integer`
- 自动拆箱：将对象转换成为基本数据类型，例如：`Integer --> int`

对于 JDK1.5 之前集合总不能存放基本数据类型的问题，现在也能够解决。

#### 枚举

枚举是 JDK1.5 推出的一个比较重要的特性。其关键字为 `enum` 例如：定义代表交通灯的枚举

```java
public enum MyEnum{
    RED,GREEN,YELLOW
}
```

#### 静态导入

- 优点：使用静态导入可以使被导入类的所有静态变量和静态方法在当前类直接可见，使用这些静态成员无需再给出他们的类名。
- 缺点：过度使用会降低代码的可读性

#### 变长参数

在JDK1.5以前，当我们要为一个方法传递多个类型相同的参数时， 我们有两种方法解决

1. 直接传递一个数组过去
2. 有多少个参数就传递多少个参数。

例如：

```java
public void printColor(String red,String green,String yellow){ 
}
```

或者

```java
public void printColor(String[] colors){

}
```

这样编写方法参数虽然能够实现我们想要的效果，但是，这样是不是有点麻烦呢？ 再者，如果参数个数不确定，我们怎么办呢？Java JDK1.5为我们提供的可变参数就能够完美的解决这个问题.

例如：

```java
public void printColor(String... colors){

}
```

如果参数的类型相同，那么可以使用 `类型+三个点` ，后面跟一个参数名称的形式。 这样的好处就是，只要参数类型相同，无论传递几个参数都没有限制 注意：可变参数必须是参数列表的最后一项（该特性对对象和基本数据类型都适用）

#### 泛型

```java
//给集合指定存入类型，上面这个集合在存入数据的时候必须存入String类型的数据，否则编译器会报错
List<String> strs = new ArrayList<String>();
```

**泛型** 意味着编写的代码可以被不同类型的对象所重用。 可见泛型的提出是为了编写重用性更好的代码。 泛型的本质是参数化类型，也就是说所操作的数据类型被指定为一个参数。

比如常见的集合类 `LinkedList`，其实现的接口名后有个特殊的部分 `<>`，而且它的成员的类型 Link 也包含一个 `<>`，这个符号的就是类型参数， 它使得在运行中，创建一个 LinkedList 时可以传入不同的类型，比如 `new LinkedList`，这样它的成员存放的类型也是 `String`。

#### For-Each循环

例如上面这个集合我们可以通过for-each遍历，这样更加简单清晰

```java
for(String s : strs){ 
     System.out.println(s); 
}
```

> 注意：使用for-each遍历集合时，要遍历的集合必须实现了Iterator接口

#### 线程并发库 JUC

线程并发库是 Java1.5 提出的关于多线程处理的高级功能，所在包：`java.util.concurrent` 包括

1. 线程互斥工具类：Lock，ReadWriteLock
2. 线程通信：Condition
3. 线程池：ExecutorService
4. 同步队列：ArrayBlockingQueue
5. 同步集合：ConcurrentHashMap，CopyOnWriteArrayList
6. 线程同步工具：Semaphore

### JDK 6

#### Desktop类和SystemTray类

前者可以用来打开系统默认浏览器浏览指定的URL，打开系统默认邮件客户端给指定的邮箱发邮件， 用默认应用程序打开或编辑文件(比如，用记事本打开以 txt 为后缀名的文件)，
用系统默认的打印机打印文档；后者可以用来在系统托盘区创建一个托盘程序。

#### 使用Compiler API

现在我们可以用JDK1.6 的Compiler API(JSR 199)去动态编译Java源文件， Compiler API结合反射功能就可以实现动态的产生Java代码并编译执行这些代码，有点动态语言的特征。

这个特性对于某些需要用到动态编译的应用程序相当有用，比如JSP Web Server，当我们手动修改JSP后， 是不希望需要重启Web Server才可以看到效果的，这时候我们就可以用Compiler API来实现动态编译JSP文件。 当然，现在的JSP Web Server也是支持JSP热部署的，现在的JSP Web Server通过在运行期间通过Runtime.exec或ProcessBuilder来调用javac来编译代码， 这种方式需要我们产生另一个进程去做编译工作，不够优雅而且容易使代码依赖与特定的操作系统； Compiler API通过一套易用的标准的API提供了更加丰富的方式去做动态编译，而且是跨平台的。

#### 轻量级Http Server API

JDK1.6 提供了一个简单的 Http Server API，据此我们可以构建自己的嵌入式 Http Server， 它支持Http和Https协议，提供了HTTP1.1的部分实现，没有被实现的那部分可以通过扩展已有的 Http Server API来实现， 程序员必须自己实现 HttpHandler 接口，HttpServer 会调用 `HttpHandler` 实现类的回调方法来处理客户端请求， 在这里，我们把一个 Http 请求和它的响应称为一个交换，包装成 `HttpExchange` 类，`HttpServer` 负责将 `HttpExchange` 传给 `HttpHandler` 实现类的回调方法。

#### 用Console开发控制台程序

JDK1.6 中提供了 `java.io.Console` 类专用来访问基于字符的控制台设备。 你的程序如果要与 Windows 下的 cmd 或者 Linux 下的 Terminal 交互，就可以用 `Console` 类代劳。 但我们不总是能得到可用的 Console，一个JVM是否有可用的 Console 依赖于底层平台和 JVM 如何被调用。 如果JVM是在交互式命令行(比如 Windows 的 cmd)中启动的，并且输入输出没有重定向到另外的地方，那么就可以得到一个可用的 Console 实例。

#### 对脚本语言的支持

如：ruby，groovy，javascript。

### JDK 7

#### 数字变量对下滑线的支持

JDK1.7可以在数值类型的变量里添加下滑线，但是有几个地方是不能添加的

1. 数字的开头和结尾
2. 小数点前后
3. F或者L前

例如：

```java
int num = 1234_5678_9; 
float num2 = 222_33F; 
long num3 = 123_000_111L;
```

#### switch对String的支持

```java
String status = "orderState";     
switch (status) {   
    case "ordercancel":   
        System.out.println("订单取消");   
        break;   
    case "orderSuccess":   
        System.out.println("预订成功");   
        break;   
    default:   
        System.out.println("状态未知");   
}  
```

#### try-with-resource

- `try-with-resources` 是一个定义了一个或多个资源的 try 声明，这个资源是指程序处理完它之后需要关闭它的对象。
- `try-with-resources` 确保每一个资源在处理完成后都会被关闭。

可以使用try-with-resources的资源有： 任何实现了 `java.lang.AutoCloseable` 接口 `java.io.Closeable` 接口的对象。

例如：

```java
public static String readFirstLineFromFile(String path) throws IOException {   

    try (BufferedReader br = new BufferedReader(new FileReader(path))) {   
        return br.readLine();   
    }   
}   
```

在 java 7 以及以后的版本里，`BufferedReader` 实现了 `java.lang.AutoCloseable` 接口。 由于 `BufferedReader` 定义在 `try-with-resources` 声明里，无论 `try` 语句正常还是异常的结束， 它都会自动的关掉。而在 java7 以前，你需要使用 `finally` 块来关掉这个对象。

#### 捕获多种异常并用改进后的类型检查来重新抛出异常

```
public static void first(){   
    try {   
        BufferedReader reader = new BufferedReader(new FileReader(""));   
        Connection con = null;   
        Statement stmt = con.createStatement();   
    } catch (IOException | SQLException e) {   
        //捕获多个异常，e就是final类型的   
        e.printStackTrace();   
    }   
} 
```

优点：用一个 `catch` 处理多个异常，比用多个 `catch` 每个处理一个异常生成的字节码要更小更高效。

#### 创建泛型时类型推断

只要编译器可以从上下文中推断出类型参数，你就可以用一对空着的尖括号 `<>` 来代替泛型参数。 这对括号私下被称为菱形(diamond)。 在Java SE 7之前，你声明泛型对象时要这样

```java
List<String> list = new ArrayList<String>();
```

而在Java SE7以后，你可以这样

```java
List<String> list = new ArrayList<>();
```

因为编译器可以从前面(List)推断出推断出类型参数，所以后面的 `ArrayList` 之后可以不用写泛型参数了，只用一对空着的尖括号就行。 当然，你必须带着菱形 `<>`，否则会有警告的。 Java SE7 只支持有限的类型推断：只有构造器的参数化类型在上下文中被显著的声明了，你才可以使用类型推断，否则不行。

```java
List<String> list = new ArrayList<>();l
list.add("A"); 
//这个不行 
list.addAll(new ArrayList<>()); 
// 这个可以 
List<? extends String> list2 = new ArrayList<>(); 
list.addAll(list2);
```

### JDK 8

#### Lambda表达式和函数式接口

Lambda表达式（也称为闭包）是Java 8中最大和最令人期待的语言改变。它允许我们将函数当成参数传递给某个方法， 或者把代码本身当作数据处理：函数式开发者非常熟悉这些概念。很多JVM平台上的语言（Groovy、Scala等）从诞生之日就支持Lambda表达式，但是Java开发者没有选择，只能使用匿名内部类代替Lambda表达式。 Lambda的设计耗费了很多时间和很大的社区力量，最终找到一种折中的实现方案，可以实现简洁而紧凑的语言结构。最简单的Lambda表达式可由逗号分隔的参数列表、->符号和语句块组成。

Lambda的设计者们为了让现有的功能与Lambda表达式良好兼容，考虑了很多方法，于是产生了函数接口这个概念。函数接口指的是只有一个函数的接口，这样的接口可以隐式转换为Lambda表达式。java.lang.Runnable和java.util.concurrent.Callable是函数式接口的最佳例子。在实践中，函数式接口非常脆弱：只要某个开发者在该接口中添加一个函数，则该接口就不再是函数式接口进而导致编译失败。为了克服这种代码层面的脆弱性，并显式说明某个接口是函数式接口，Java 8 提供了一个特殊的注解@FunctionalInterface（Java 库中的所有相关接口都已经带有这个注解了），举个简单的函数式接口的定义

#### 接口的默认方法和静态方法

Java 8使用两个新概念扩展了接口的含义：默认方法和静态方法。默认方法使得接口有点类似traits，不过要实现的目标不一样。默认方法使得开发者可以在 不破坏二进制兼容性的前提下，往现存接口中添加新的方法，即不强制那些实现了该接口的类也同时实现这个新加的方法。 默认方法和抽象方法之间的区别在于抽象方法需要实现，而默认方法不需要。接口提供的默认方法会被接口的实现类继承或者覆写 由于JVM上的默认方法的实现在字节码层面提供了支持，因此效率非常高。默认方法允许在不打破现有继承体系的基础上改进接口。该特性在官方库中的应用是：给java.util.Collection接口添加新方法，如stream()、parallelStream()、forEach()和removeIf()等等。 尽管默认方法有这么多好处，但在实际开发中应该谨慎使用：在复杂的继承体系中，默认方法可能引起歧义和编译错误。如果你想了解更多细节，可以参考官方文档。

#### 更好的类型推断

Java 8 编译器在类型推断方面有很大的提升，在很多场景下编译器可以推导出某个参数的数据类型，从而使得代码更为简洁。

参数 `Value.defaultValue()` 的类型由编译器推导得出，不需要显式指明。在Java 7中这段代码会有编译错误，除非使用 `Value.<String>defaultValue()`。

#### Optional

Java应用中最常见的bug就是空值异常。在Java 8之前，Google Guava引入了 `Optionals` 类来解决 `NullPointerException`， 从而避免源码被各种 `null` 检查污染，以便开发者写出更加整洁的代码。Java 8也将Optional加入了官方库。 `Optional` 仅仅是一个容易存放T类型的值或者null。它提供了一些有用的接口来避免显式的null检查，可以参考Java 8官方文档了解更多细节。

如果Optional实例持有一个非空值，则 `isPresent()` 方法返回true，否则返回false；`orElseGet()` 方法，Optional实例持有null， 则可以接受一个lambda表达式生成的默认值；map()方法可以将现有的 `Optional` 实例的值转换成新的值；orElse()方法与orElseGet()方法类似， 但是在持有null的时候返回传入的默认值。

#### Streams

新增的Stream API（java.util.stream）将生成环境的函数式编程引入了Java库中。 这是目前为止最大的一次对Java库的完善，以便开发者能够写出更加有效、更加简洁和紧凑的代码。

Task 类有一个分数（或伪复杂度）的概念，另外还有两种状态：OPEN 或者 CLOSED。现在假设有一个task集合， 首先看一个问题：在这个task集合中一共有多少个OPEN状态的点？在Java 8之前，要解决这个问题，则需要使用foreach循环遍历task集合； 但是在Java 8中可以利用steams解决：包括一系列元素的列表，并且支持顺序和并行处理。

```java
final Collection<Task> tasks = Arrays.asList(
        new Task(Status.OPEN, 5),
        new Task(Status.OPEN, 13),
        new Task(Status.CLOSED, 8)
);

// Calculate total points of all active tasks using sum()
final long totalPointsOfOpenTasks = tasks
        .stream()
        .filter(task -> task.getStatus() == Status.OPEN)
        .mapToInt(Task::getPoints)
        .sum();

System.out.println("Total points: " + totalPointsOfOpenTasks);
```

这里有很多知识点值得说。首先，tasks集合被转换成steam表示；其次，在steam上的filter操作会过滤掉所有CLOSED的task； 第三，mapToInt操作基于每个task实例的Task::getPoints方法将task流转换成Integer集合；最后，通过sum方法计算总和，得出最后的结果。

#### 新的日期时间 API

Java 8引入了新的Date-Time API(JSR 310)来改进时间、日期的处理。时间和日期的管理一直是最令Java开发者痛苦的问题。 java.util.Date 和后来的 java.util.Calendar 一直没有解决这个问题（甚至令开发者更加迷茫）。

因为上面这些原因，诞生了第三方库Joda-Time，可以替代Java的时间管理API。 Java 8中新的时间和日期管理API深受Joda-Time影响，并吸收了很多Joda-Time的精华。 新的java.time包包含了所有关于日期、时间、时区、Instant（跟日期类似但是精确到纳秒）、duration（持续时间）和时钟操作的类。 新设计的API认真考虑了这些类的不变性（从java.util.Calendar吸取的教训），如果某个实例需要修改，则返回一个新的对象。

第二，关注下LocalDate和LocalTime类。LocalDate仅仅包含ISO-8601日历系统中的日期部分；LocalTime则仅仅包含该日历系统中的时间部分。这两个类的对象都可以使用Clock对象构建得到。 LocalDateTime类包含了LocalDate和LocalTime的信息，但是不包含ISO-8601日历系统中的时区信息。这里有一些关于LocalDate和LocalTime的例子： 如果你需要特定时区的data/time信息，则可以使用ZoneDateTime，它保存有ISO-8601日期系统的日期和时间，而且有时区信息。

#### Nashorn JavaScript引擎

Java 8提供了新的Nashorn JavaScript引擎，使得我们可以在JVM上开发和运行JS应用。 Nashorn JavaScript引擎是javax.script.ScriptEngine的另一个实现版本，这类Script引擎遵循相同的规则，允许Java和JavaScript交互使用，例子代码如下：

```js
var fun1 = function(name) {
    print('Hi there from Javascript, ' + name);
    return "greetings from javascript";
};

var fun2 = function (object) {
    print("JS Class Definition: " + Object.prototype.toString.call(object));
};
```

```java
ScriptEngine engine = new ScriptEngineManager().getEngineByName("nashorn");
        engine.eval(new FileReader("java8-nashorn/src/main/resources/nashorn1.js"));

        Invocable invocable = (Invocable) engine;
        Object result = invocable.invokeFunction("fun1", "Peter Parker");
        System.out.println(result);
        System.out.println(result.getClass());

        invocable.invokeFunction("fun2", new Date());
        invocable.invokeFunction("fun2", LocalDateTime.now());
        invocable.invokeFunction("fun2", new Person());
```

#### Base64

对 Base64 编码的支持已经被加入到Java 8官方库中，这样不需要使用第三方库就可以进行Base64编码，例子代码如下：

```java
final String text = "Lets Learn Java 8!";

final String encoded = Base64
        .getEncoder()
        .encodeToString(text.getBytes(StandardCharsets.UTF_8));
System.out.println(encoded);

final String decoded = new String(
        Base64.getDecoder().decode(encoded),
        StandardCharsets.UTF_8);
System.out.println(decoded);
```

新的Base64API也支持URL和MINE的编码解码。

## Default Methods for Interface

Java 8 允许我们为接口添加非抽象的方法通过 **default**  关键字。当然静态方法的实现也是允许的。

```java
interface Formula {
    double calculate(int a);

    // 默认方法实现
    default double sqrt(int a) {
        return Math.sqrt(a);
    }
    
    // 静态方法实现
    static void hello() { System.out.println("hello world");}
}
```

所以我们实现接口的时候只需要实现抽象方法就可以了，默认方法可以直接使用。下面例子 The formula is implemented as an anonymous object.

```java
Formula formula = new Formula() {
    @Override
    public double calculate(int a) {
        return sqrt(a * 100);
    }
};

formula.calculate(100);     // 100.0
formula.sqrt(16);           // 4.0
```

## Lambda expression

**λ演算**（英语：lambda calculus，λ-calculus）是一套从数学逻辑中发展，以变量绑定和替换的规则，来研究[函数](https://zh.wikipedia.org/wiki/%E5%87%BD%E6%95%B0)如何抽象化定义、函数如何被应用以及[递归](https://zh.wikipedia.org/wiki/%E9%80%92%E5%BD%92)的[形式系统](https://zh.wikipedia.org/wiki/%E5%BD%A2%E5%BC%8F%E7%B3%BB%E7%B5%B1)。它由数学家[阿隆佐·邱奇](https://zh.wikipedia.org/wiki/%E9%98%BF%E9%9A%86%E4%BD%90%C2%B7%E9%82%B1%E5%A5%87)在20世纪30年代首次发表。lambda演算作为一种广泛用途的计算模型，可以清晰地定义什么是一个可计算函数，而任何可计算函数都能以这种形式表达和求值，它能模拟单一磁带[图灵机](https://zh.wikipedia.org/wiki/%E5%9B%BE%E7%81%B5%E6%9C%BA)的计算过程；尽管如此，lambda演算强调的是变换规则的运用，而非实现它们的具体机器。

更多介绍请教wiki爸爸，虽然我看的一脸懵逼 [**wiki**](<https://zh.wikipedia.org/wiki/%CE%9B%E6%BC%94%E7%AE%97>)

简单的说 lambda expression就是一个匿名的函数，通常作为其他函数的参数。

下面通过一个例子简单介绍下

之前版本的我们写

```java
List<String> names = Arrays.asList("peter", "anna", "mike", "xenia");

Collections.sort(names, new Comparator<String>() {
    @Override
    public int compare(String a, String b) {
        return b.compareTo(a);
    }
});
```

取代之前创建匿名内部类的方法，通过 Java8 我们可以用更少的代码来实现：

```java
Collections.sort(names, (String a, String b) -> {
    return b.compareTo(a);
});
```

如果方法只有一行语句，那么花括号 **{}** 是可选的，并且假如这一行语句有返回值，那么 **return** 关键字也是可选的。所以我们的代码更精简了：

```java
Collections.sort(names, (String a, String b) -> b.compareTo(a));
```

Lambda 的参数类型可以由上下文推算而出，所以，参数类型也是可选的，

```java
Collections.sort(names, (a, b) -> b.compareTo(a));
```

下面我们就看看 lambda的相关特性和实现

### Diff with Anonymous Classes

在JVM层面，Lambda表达式和匿名内部类有着明显的差别。

**匿名内部类仍然是一个类，只是不需要程序员显示指定类名，编译器会自动为该类取名**。因此如果有如下形式的代码，编译之后将会产生两个class文件：

```java
public class MainAnonymousClass {
	public static void main(String[] args) {
		new Thread(new Runnable(){
			@Override
			public void run(){
				System.out.println("Anonymous Class Thread run()");
			}
		}).start();;
	}
}
```

编译之后文件分布如下，两个class文件分别是主类和匿名内部类产生的：

[![2-AnonymousClass.png](https://github.com/CarpenterLee/JavaLambdaInternals/raw/master/Figures/2-AnonymousClass.png)](https://github.com/CarpenterLee/JavaLambdaInternals/blob/master/Figures/2-AnonymousClass.png)

进一步分析主类MainAnonymousClass.class的字节码，可发现其创建了匿名内部类的对象：

```java
// javap -c MainAnonymousClass.class
public class MainAnonymousClass {
  ...
  public static void main(java.lang.String[]);
    Code:
       0: new           #2                  // class java/lang/Thread
       3: dup
       4: new           #3                  // class MainAnonymousClass$1 /*创建内部类对象*/
       7: dup
       8: invokespecial #4                  // Method MainAnonymousClass$1."<init>":()V
      11: invokespecial #5                  // Method java/lang/Thread."<init>":(Ljava/lang/Runnable;)V
      14: invokevirtual #6                  // Method java/lang/Thread.start:()V
      17: return
}
```

**Lambda表达式通过invokedynamic指令实现，书写Lambda表达式不会产生新的类**。如果有如下代码，编译之后只有一个class文件：

```java
public class MainLambda {
	public static void main(String[] args) {
		new Thread(
				() -> System.out.println("Lambda Thread run()")
			).start();;
	}
}
```

编译之后的结果：

[![2-Lambda](https://github.com/CarpenterLee/JavaLambdaInternals/raw/master/Figures/2-Lambda.png)](https://github.com/CarpenterLee/JavaLambdaInternals/blob/master/Figures/2-Lambda.png)

通过javap反编译命名，我们更能看出Lambda表达式内部表示的不同：

```java
// javap -c -p MainLambda.class
public class MainLambda {
  ...
  public static void main(java.lang.String[]);
    Code:
       0: new           #2                  // class java/lang/Thread
       3: dup
       4: invokedynamic #3,  0              // InvokeDynamic #0:run:()Ljava/lang/Runnable; /*使用invokedynamic指令调用*/
       9: invokespecial #4                  // Method java/lang/Thread."<init>":(Ljava/lang/Runnable;)V
      12: invokevirtual #5                  // Method java/lang/Thread.start:()V
      15: return

  private static void lambda$main$0();  /*Lambda表达式被封装成主类的私有方法*/
    Code:
       0: getstatic     #6                  // Field java/lang/System.out:Ljava/io/PrintStream;
       3: ldc           #7                  // String Lambda Thread run()
       5: invokevirtual #8                  // Method java/io/PrintStream.println:(Ljava/lang/String;)V
       8: return
}
```

反编译之后我们发现Lambda表达式被封装成了主类的一个私有方法，并通过*invokedynamic*指令进行调用。

既然Lambda表达式不是内部类的简写，那么Lambda内部的`this`引用也就跟内部类对象没什么关系了。在Lambda表达式中`this`的意义跟在表达式外部完全一样。因此下列代码将输出两遍`Hello Hoolee`，而不是两个引用地址。

```java
public class Hello {
	Runnable r1 = () -> { System.out.println(this); };
	Runnable r2 = () -> { System.out.println(toString()); };
	public static void main(String[] args) {
		new Hello().r1.run();
		new Hello().r2.run();
	}
	public String toString() { return "Hello Hoolee"; }
}
```

### lambda Scope

lambda Scope实际上跟匿名内部类差不多，只不过 **local variables** 可以不声明未final，但是实际上是一个隐final。因为java8 中 lambda 代码块相当于闭包，闭包中被引用的局部变量是不能进行更改的。这也是为什么函数式编程可以很容易做到线程安全，很适合并发编程的原因。

下面通过几个小例子说明一下 lambda 的作用域

```java
final int num = 1;
Converter<Integer, String> stringConverter =
        (from) -> String.valueOf(from + num);
stringConverter.convert(2);     // 3

int num = 1;
Converter<Integer, String> stringConverter =
        (from) -> String.valueOf(from + num);
stringConverter.convert(2);     // 3

int num = 1;
// Variable used in lambda expression should be final or effectively final
Converter<Integer, String> stringConverter =
        (from) -> String.valueOf(from + num); // 无法编译
num = 3;
```

在lambda表达式中更改局部变量 num 的值同样是不允许的。

如果是 **static variables** 或是 **fields** 就跟匿名内部类一样了，无论在lambda 表达式内部还是外部都是进行修改

```java
class Lambda4 {
    static int outerStaticNum;
    int outerNum;

    void testScopes() {
        Converter<Integer, String> stringConverter1 = (from) -> {
            outerNum = 23;
            return String.valueOf(from);
        };

        Converter<Integer, String> stringConverter2 = (from) -> {
            outerStaticNum = 72;
            return String.valueOf(from);
        };
    }
}
```

还记得前面我们定义的函数接口 **Formula** 吗，接口公式定义了一个默认方法sqrt，可以从包括匿名对象在内的每个公式实例访问该方法。这不适用于lambda表达式。

不能从lambda表达式中访问 **default interface method**，以下代码无法编译

```java
Formula formula = (a) -> sqrt( a * 100);
```

### Functional Interfaces

**lambda** 表达式如何适应Java的类型系统呢？转换lambda表达式的类型总是函数接口类型。所以，一个函数接口必须**有且只有一个抽象方法声明**。该类型的每个lambda表达式都将匹配到这个抽象方法。

下面有个小例子，Runnable是一个函数接口，它只有一个方法run()。因此，当您将lambda表达式传递给Thread类的构造函数时，编译器会尝试将该表达式转换为等价的可运行代码，如第一个代码示例所示。如果编译器成功，则一切正常运行，如果编译器无法将表达式转换为等效的实现代码，则会产生错误。在上面的例子中，lambda表达式被转换为Runnable类型。

```java
new Thread(new Runnable() {
    @Override
    public void run() {
        System.out.println("howtodoinjava");
    }
}).start();

new Thread(
            () ->   {
                        System.out.println("My Runnable");
                    }
         ).start();
```

为了确保接口满足需求，您应该添加 **@FunctionalInterface** 注解，编译器知道这个注释，并在您试图向接口添加第二个抽象方法声明时抛出编译器错误。当然，注解省略也是可以的。

因为默认方法有一个实现，所以它们不是抽象的。因为默认方法不是抽象的，所以您可以随意将默认方法添加到您的函数接口中。当然静态方法也同理。

如果接口声明一个抽象方法覆盖 **java.lang.Object** 的一个公共方法，这也不计入接口的抽象方法计数，因为该接口的任何实现都有一个来自 java.lang.Object 的实现。Comparator是一个函数接口，尽管它声明了两个抽象方法。为什么?因为其中一个抽象方法equals()它的签名等于Object类中的public方法。

```java
@FunctionalInterface
interface Converter<F, T> {
    T convert(F from);
    void haha(); // 编译错误
    // 默认方法
    default void hello() {
        System.out.println("hello world");
    }
    // 静态方法也是允许的，可以少写工具类
    static void fourWork() {
        System.out.println("today is a nice day");
    }
    @Override
	public String toString();                //Overridden from Object class
	@Override
	public boolean equals(Object obj);        //Overridden from Object class	
}
Converter<String, Integer> converter = (from) -> Integer.valueOf(from);
Integer converted = converter.convert("123");
System.out.println(converted);    // 123
```

功能接口只显示一个功能。例如，使用具有单个方法 compareTo 的可比较接口进行比较。

### Method References

简单的说就是对于 lambda 表达式中只有一个参数，并且箭头右边的逻辑是对入参执行一个函数：

即 `x => f(x)`
则可以简写为`f`

Java8 用 **class::methodName** 来表示 **eta-conversion**

Method references help to point to methods by their names. A method reference is described using `::` symbol. A method reference can be used to point the following types of methods −

- Static methods
- Instance methods
- Constructors using new operator (TreeSet::new)

下面看一些简单例子

```java
// static method -> Math::max = Math.max(x,y)
List<Integer> integers = Arrays.asList(1,12,433,5);
Optional<Integer> max = integers.stream().reduce( Math::max );
max.ifPresent(value -> System.out.println(value));

// instance method from instance -> System.out::println = System.out.println(x)
ArrayList<Integer> numberList = new ArrayList<>(Arrays.asList(1,2,3,4,5));
Consumer<Integer> action = System.out::println;
numberList.forEach(action);

// instance method from class type -> String::compareTo = s1.compareTo(s2)
List<String> strings = Arrays
                .asList("how", "to", "do", "in", "java", "dot", "com");

List<String> sorted = strings
                .stream()
    .sorted((s1, s2) -> s1.compareTo(s2))
    .collect(Collectors.toList());

System.out.println(sorted);

List<String> sortedAlt = strings
                .stream()
    .sorted(String::compareTo)
    .collect(Collectors.toList());

System.out.println(sortedAlt);

// constructor -> ArrayList::new = new ArrayList
List<Integer> integers1 = IntStream
                .range(1, 100)
    .boxed()
    .collect(Collectors.toCollection( ArrayList::new ));
Optional<Integer> max1 = integers.stream().reduce(Math::max);
max.ifPresent(System.out::println);
```

### Built-in Functional Interfaces

Java 8 内置了许多函数接口，可在lambda表达式中广泛使用。

有一些是兼容旧版本的比如 `Comparator` 或者 `Runnable`，

还有一些是吸取了 [Google Guava](https://code.google.com/p/guava-libraries/) library ，比如在  **java.util.Function package** 就定义了很多实用的函数接口。

不过一般我们不需要去死记它们，因为类型推导帮我们做了一切。

比如举个简单的例子，collection 的forEach方法，该方法的签名为`void forEach(Consumer<? super E> action)`，作用是对容器中的每个元素执行`action`指定的动作，其中`Consumer`是个函数接口，里面只有一个待实现方法`void accept(T t)`（后面我们会看到，这个方法叫什么根本不重要，你甚至不需要记忆它的名字）。

需求：*假设有一个字符串列表，需要打印出其中所有长度大于3的字符串.*

Java7及以前我们可以用增强的for循环实现：

```java
// 使用增强for循环迭代
ArrayList<String> list = new ArrayList<>(Arrays.asList("I", "love", "you", "too"));
for(String str : list){
    if(str.length()>3)
        System.out.println(str);
}
```

现在使用`forEach()`方法结合匿名内部类，可以这样实现：

```java
// 使用forEach()结合匿名内部类迭代
ArrayList<String> list = new ArrayList<>(Arrays.asList("I", "love", "you", "too"));
list.forEach(new Consumer<String>(){
    @Override
    public void accept(String str){
        if(str.length()>3)
            System.out.println(str);
    }
});
```

上述代码调用`forEach()`方法，并使用匿名内部类实现`Comsumer`接口。到目前为止我们没看到这种设计有什么好处，但是不要忘记Lambda表达式，使用Lambda表达式实现如下：

```java
// 使用forEach()结合Lambda表达式迭代
ArrayList<String> list = new ArrayList<>(Arrays.asList("I", "love", "you", "too"));
list.forEach(str -> {
        if(str.length()>3)
            System.out.println(str);
    });
```

上述代码给`forEach()`方法传入一个Lambda表达式，我们不需要知道`accept()`方法，也不需要知道`Consumer`接口，类型推导帮我们做了一切。

这里，简单列几个常用的函数接口，

#### Predicate<T>

`Represents a predicate (Boolean-valued function) of one argument.The interface contains various default methods for composing predicates to complex logical terms (and, or, negate)`

```java

public static void main(String args[]) {
      List<Integer> list = Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8, 9);
		
      // Predicate<Integer> predicate = n -> true
      // n is passed as parameter to test method of Predicate interface
      // test method will always return true no matter what value n has.
		
      System.out.println("Print all numbers:");
		
      //pass n as parameter
      eval(list, n->true);
		
      // Predicate<Integer> predicate1 = n -> n%2 == 0
      // n is passed as parameter to test method of Predicate interface
      // test method will return true if n%2 comes to be zero
		
      System.out.println("Print even numbers:");
      eval(list, n-> n%2 == 0 );
		
      // Predicate<Integer> predicate2 = n -> n > 3
      // n is passed as parameter to test method of Predicate interface
      // test method will return true if n is greater than 3.
		
      System.out.println("Print numbers greater than 3:");
      eval(list, n-> n > 3 );
   }
	
   public static void eval(List<Integer> list, Predicate<Integer> predicate) {

      for(Integer n: list) {

         if(predicate.test(n)) {
            System.out.println(n + " ");
         }
      }
   }
```

#### Function<T,R>

`Represents a function that accepts one argument and produces a result.Default methods can be used to chain multiple functions together (compose, andThen).`

```java
Function<String, Integer> toInteger = Integer::valueOf;
Function<String, String> backToString = toInteger.andThen(String::valueOf);

backToString.apply("123");     // "123"
```

#### Supplier<T>

`Suppliers produce a result of a given generic type. Unlike Functions, Suppliers don't accept arguments.`

```java
Supplier<Person> personSupplier = Person::new;
personSupplier.get();   // new Person
```

#### Consumer<T>

`Consumers represents operations to be performed on a single input argument.`

```java
consumer<Person> greeter = (p) -> System.out.println("Hello, " + p.firstName);
greeter.accept(new Person("Luke", "Skywalker"));
```

#### Comparators

`Comparators are well known from older versions of Java. Java 8 adds various default methods to the interface.`

```java
Comparator<Person> comparator = (p1, p2) -> p1.firstName.compareTo(p2.firstName);

Person p1 = new Person("John", "Doe");
Person p2 = new Person("Alice", "Wonderland");

comparator.compare(p1, p2);             // > 0
comparator.reversed().compare(p1, p2);  // < 0
```

## Streams

A **Collection is an in-memory data structure**, 

A **Stream is a conceptually fixed data structure, in which elements are computed on demand**.

This is a form of a **producer-consumer** relationship.

In java, java.util.Stream represents a stream on which one or more operations can be performed. Stream **operations are either intermediate or terminal**. While **terminal operations return a result of a certain type**, **intermediate operations return the stream itself** so you can chain multiple method calls in a row. Streams are created on a source, e.g. a java.util.Collection like lists or sets (maps are not supported). Stream operations can either be executed sequential or parallel.

简单来说，流就相当于我们在线看电影一样，不需要下载整部电影，只关注我们感兴趣的地方。流只是数据源的一个视图，我们可以对这个视图进行过滤，映射，排序，统计等一系列操作，而不会影响到数据源，并且在流上的操作是按需执行的，执行后即失效，所以很适合于函数编程。

### Streams  processing order

衔接操作的一个重要特性就是延迟性。观察下面没有终止操作的例子：

```java
Stream.of("d2", "a2", "b1", "b3", "c")
.filter(s -> {
System.out.println("filter: " + s);
return true;
});
```

执行这段代码时，不向控制台打印任何东西。这是因为衔接操作只在终止操作调用时被执行。

让我们通过添加终止操作`forEach`来扩展这个例子：

```java
Stream.of("d2", "a2", "b1", "b3", "c")
.filter(s -> {
System.out.println("filter: " + s);
return true;
})
.forEach(s -> System.out.println("forEach: " + s));
```

执行这段代码会得到如下输出：

```java
filter:  d2
forEach: d2
filter:  a2
forEach: a2
filter:  b1
forEach: b1
filter:  b3
forEach: b3
filter:  c
forEach: c
```

结果的顺序可能出人意料。原始的方法会在数据流的所有元素上，一个接一个地水平执行所有操作。但是每个元素在调用链上垂直移动。第一个字符串`"d2"`首先经过`filter`然后是`forEach`，执行完后才开始处理第二个字符串`"a2"`。

这种行为可以减少每个元素上所执行的实际操作数量，就像我们在下个例子中看到的那样：

```java
Stream.of("d2", "a2", "b1", "b3", "c")
.map(s -> {
System.out.println("map: " + s);
return s.toUpperCase();
})
.anyMatch(s -> {
System.out.println("anyMatch: " + s);
return s.startsWith("A");
});

// map:      d2
// anyMatch: D2
// map:      a2
// anyMatch: A2
```

只要提供的数据元素满足了谓词，`anyMatch`操作就会返回`true`。对于第二个传递`"A2"`的元素，它的结果为真。由于数据流的链式调用是垂直执行的，`map`这里只需要执行两次。所以`map`会执行尽可能少的次数，而不是把所有元素都映射一遍。

#### 为什么顺序如此重要

下面的例子由两个衔接操作`map`和`filter`，以及一个终止操作`forEach`组成。让我们再来看看这些操作如何执行：

```java
Stream.of("d2", "a2", "b1", "b3", "c")
.map(s -> {
System.out.println("map: " + s);
return s.toUpperCase();
})
.filter(s -> {
System.out.println("filter: " + s);
return s.startsWith("A");
})
.forEach(s -> System.out.println("forEach: " + s));

// map:     d2
// filter:  D2
// map:     a2
// filter:  A2
// forEach: A2
// map:     b1
// filter:  B1
// map:     b3
// filter:  B3
// map:     c
// filter:  C
```

就像你可能猜到的那样，`map`和`filter`会对底层集合的每个字符串调用五次，而`forEach`只会调用一次。

如果我们调整操作顺序，将`filter`移动到调用链的顶端，就可以极大减少操作的执行次数:

```java
Stream.of("d2", "a2", "b1", "b3", "c")
    .filter(s -> {
        System.out.println("filter: " + s);
        return s.startsWith("a");
    })
    .map(s -> {
        System.out.println("map: " + s);
        return s.toUpperCase();
    })
    .forEach(s -> System.out.println("forEach: " + s));

// filter:  d2
// filter:  a2
// map:     a2
// forEach: A2
// filter:  b1
// filter:  b3
// filter:  c
```

现在，`map`只会调用一次，所以操作流水线对于更多的输入元素会执行更快。在整合复杂的方法链时，要记住这一点。

让我们通过添加额外的方法`sorted`来扩展上面的例子：

```java
Stream.of("d2", "a2", "b1", "b3", "c")
    .sorted((s1, s2) -> {
        System.out.printf("sort: %s; %s\n", s1, s2);
        return s1.compareTo(s2);
    })
    .filter(s -> {
        System.out.println("filter: " + s);
        return s.startsWith("a");
    })
    .map(s -> {
        System.out.println("map: " + s);
        return s.toUpperCase();
    })
    .forEach(s -> System.out.println("forEach: " + s));
```

排序是一类特殊的衔接操作。它是有状态的操作，因为你需要在处理中保存状态来对集合中的元素排序。

执行这个例子会得到如下输入：

```bash
sort:    a2; d2
sort:    b1; a2
sort:    b1; d2
sort:    b1; a2
sort:    b3; b1
sort:    b3; d2
sort:    c; b3
sort:    c; d2
filter:  a2
map:     a2
forEach: A2
filter:  b1
filter:  b3
filter:  c
filter:  d2
```

首先，排序操作在整个输入集合上执行。也就是说，`sorted`以水平方式执行。所以这里`sorted`对输入集合中每个元素的多种组合调用了八次。

我们同样可以通过重排调用链来优化性能：

```java
Stream.of("d2", "a2", "b1", "b3", "c")
    .filter(s -> {
        System.out.println("filter: " + s);
        return s.startsWith("a");
    })
    .sorted((s1, s2) -> {
        System.out.printf("sort: %s; %s\n", s1, s2);
        return s1.compareTo(s2);
    })
    .map(s -> {
        System.out.println("map: " + s);
        return s.toUpperCase();
    })
    .forEach(s -> System.out.println("forEach: " + s));

// filter:  d2
// filter:  a2
// filter:  b1
// filter:  b3
// filter:  c
// map:     a2
// forEach: A2
```

这个例子中`sorted`永远不会调用，因为`filter`把输入集合减少至只有一个元素。所以对于更大的输入集合会极大提升性能。

#### 复用数据流

Java8的数据流不能被复用。一旦你调用了任何终止操作，数据流就关闭了：

```java
Stream<String> stream =
Stream.of("d2", "a2", "b1", "b3", "c")
.filter(s -> s.startsWith("a"));

stream.anyMatch(s -> true); // ok
stream.noneMatch(s -> true); // exception
```

在相同数据流上，在`anyMatch`之后调用`noneMatch`会产生下面的异常：

```java
java.lang.IllegalStateException: stream has already been operated upon or closed
   at java.util.stream.AbstractPipeline.evaluate(AbstractPipeline.java:229)
   at java.util.stream.ReferencePipeline.noneMatch(ReferencePipeline.java:459)
   at com.winterbe.java8.Streams5.test7(Streams5.java:38)
   at com.winterbe.java8.Streams5.main(Streams5.java:28)
```

要克服这个限制，我们需要为每个我们想要执行的终止操作创建新的数据流调用链。例如，我们创建一个数据流供应器，来构建新的数据流，并且设置好所有衔接操作：

```java
Supplier<Stream<String>> streamSupplier =
    () -> Stream.of("d2", "a2", "b1", "b3", "c")
    .filter(s -> s.startsWith("a"));

streamSupplier.get().anyMatch(s -> true); // ok
streamSupplier.get().noneMatch(s -> true); // ok 
```

每次对`get()`的调用都构造了一个新的数据流，我们将其保存来调用终止操作。

### Create Stream

```java
// Stream.of(val1, val2, val3….)
Stream<Integer> stream1 = Stream.of(1,2,3,4,5,6,7,8,9);
stream1.forEach(p -> System.out.println(p));

// Stream.of(arrayOfElements)
Stream<Integer> stream2 = Stream.of( new Integer[]{1,2,3,4,5,6,7,8,9} );
stream2.forEach(p -> System.out.println(p));

// List.stream()
List<Integer> list = new ArrayList<Integer>();
for(int i = 1; i< 10; i++){
    list.add(i);
}
Stream<Integer> stream3 = list.stream();
stream3.forEach(p -> System.out.println(p));

// String chars or String tokens
IntStream stream5 = "12345_abcdefg".chars();
stream5.forEach(p -> System.out.println(p));
//OR
Stream<String> stream6 = Stream.of("A$B$C".split("\\$"));
stream6.forEach(p -> System.out.println(p));

// file
try(Stream lines = Files.lines(Paths.get("文件路径名"),Charset.defaultCharset())){
    //可对lines做一些操作
}catch(IOException e){
}

// create iterator stream, 创建迭代或者无限流，大都数情况需要添加limit函数限制
// Stream.generate() or Stream.iterate()
Stream<Date> stream4 = Stream.generate(() -> { return new Date(); }).limit(10);
stream4.forEach(p -> System.out.println(p));
Stream.iterate(0, n -> n + 2)
    .limit(10)
    .forEach(System.out::println);
```

### Convert streams to collections

It’s just collecting the elements from the stream into a collection or array.

```java
  // Convert Stream to List – Stream.collect( Collectors.toList() )
        List<Integer> list1 = new ArrayList<Integer>();
        for(int i = 1; i< 10; i++){
            list1.add(i);
        }
        Stream<Integer> stream1 = list1.stream();
        List<Integer> evenNumbersList = stream1.filter(i -> i%2 == 0).collect(Collectors.toList());
        System.out.print(evenNumbersList);


        // Convert Stream to array – Stream.toArray( EntryType[]::new )
        List<Integer> list2 = new ArrayList<Integer>();
        for(int i = 1; i< 10; i++){
            list2.add(i);
        }
        Stream<Integer> stream2 = list2.stream();
        Integer[] evenNumbersArr = stream2.filter(i -> i%2 == 0).toArray(Integer[]::new);
        System.out.print(evenNumbersArr);
```

### Core stream operations

|             |      | 操作分类      |                          |                  |
| ----------- | ---- | ------------- | ------------------------ | ---------------- |
| 操作        | 类型 | 返回类型      | 使用的类型/函数式接口    | 函数描述符       |
| `filter`    | 中间 | `Stream<T>`   | `Predicate<T>`           | `T -> boolean`   |
| `distinct`  | 中间 | `Stream<T>`   |                          |                  |
| `skip`      | 中间 | `Stream<T>`   | long                     |                  |
| `map`       | 中间 | `Stream<R>`   | `Function<T, R>`         | `T -> R`         |
| `flatMap`   | 中间 | `Stream<R>`   | `Function<T, Stream<R>>` | `T -> Stream<R>` |
| `limit`     | 中间 | `Stream<T>`   | long                     |                  |
| `sorted`    | 中间 | `Stream<T>`   | `Comparator<T>`          | `(T, T) -> int`  |
| `anyMatch`  | 终端 | `boolean`     | `Predicate<T>`           | `T -> boolean`   |
| `noneMatch` | 终端 | `boolean`     | `Predicate<T>`           | `T -> boolean`   |
| `allMatch`  | 终端 | `boolean`     | `Predicate<T>`           | `T -> boolean`   |
| `findAny`   | 终端 | `Optional<T>` |                          |                  |
| `findFirst` | 终端 | `Optional<T>` |                          |                  |
| `forEach`   | 终端 | `void`        | `Consumer<T>`            | `T -> void`      |
| `collect`   | 终端 | `R`           | `Collector<T, A, R>`     |                  |
| `reduce`    | 终端 | `Optional<T>` | `BinaryOperator<T>`      | `(T, T) -> T`    |
| `count`     | 终端 | `long`        |                          |                  |

#### Intermediate operations

**Intermediate operations return the stream itself**, so you can chain multiple method calls in a row.

中间流返回流本身。

这个简单的说就是*pipeline*操作了，类比linux的管道流，

比如我要统计一个目录下所有文件包含hello的字数并且统计出现的次数并且排序，然后筛选出前几个

```shell
grep -l 'hello' /tmp/* | xargs wc -w | sort -nr | head -3
```

更多介绍请戳 [collection-pipeline](<https://martinfowler.com/articles/collection-pipeline/>)

下面我们先定义一个集合然后操作几种常用的流操作。

```java
 List<String> memberNames = new ArrayList<>();
memberNames.add("Amitabh");
memberNames.add("Shekhar");
memberNames.add("Aman");
memberNames.add("Rahul");
memberNames.add("Shahrukh");
memberNames.add("Salman");
memberNames.add("Yana");
memberNames.add("Lokesh");
```



##### Stream.filter()

Filter accepts a predicate to filter all elements of the stream. This operation is intermediate which enables us to call another stream operation (e.g. forEach) on the result.

过滤流获取我们需要的数据

```java
memberNames.stream().filter((s) -> s.startsWith("A"))
    .forEach(System.out::println);

```

##### Stream.distinct()

函数原型为`Stream<T> distinct()`，作用是返回一个去除重复元素之后的`Stream`

```java
Stream<String> stream= Stream.of("I", "love", "you", "too", "too");
stream.distinct()
    .forEach(str -> System.out.println(str));
```

##### Stream.map()

The intermediate operation map converts each element into another object via the given function

映射流的每个元素进行转换等操作

```java
memberNames.stream().filter((s) -> s.startsWith("A"))
    .map(String::toUpperCase)
    .forEach(System.out::println);
```

##### Stream.flatMap

函数原型为`<R> Stream<R> flatMap(Function<? super T,? extends Stream<? extends R>> mapper)`，作用是对每个元素执行`mapper`指定的操作，并用所有`mapper`返回的`Stream`中的元素组成一个新的`Stream`作为最终返回结果。说起来太拗口，通俗的讲`flatMap()`的作用就相当于把原*stream*中的所有元素都"摊平"之后组成的`Stream`，转换前后元素的个数和类型都可能会改变。

```java
Stream<List<Integer>> stream = Stream.of(Arrays.asList(1,2), Arrays.asList(3, 4, 5));
stream.flatMap(list -> list.stream())
    .forEach(i -> System.out.println(i));
```

##### Stream.sorted()

Sorted is an intermediate operation which returns a sorted view of the stream. The elements are sorted in natural order unless you pass a custom Comparator.

对流的元素进行排序，除非传递自定义比较器，否则元素按自然顺序排序。

注意，流排序只是创建已排序的流视图，不改变原本集合的顺序。

```java
 memberNames.stream().sorted()
     .map(String::toUpperCase)
     .forEach(System.out::println);
```

#### Terminal operations

**Terminal operations return a result of a certain type** instead of again a Stream.

终端流返回特定类型的结果，表示流操作完成。

##### Stream.forEach()

This method helps in iterating over all elements of a stream and perform some operation on each of them. The operation is passed as lambda expression parameter.

遍历流的所有元素并对每个元素执行某些操作。作为lambda表达式参数传递。

```java
memberNames.forEach(System.out::println);

```

##### Stream.match()

Various matching operations can be used to check whether a certain predicate matches the stream. All of those operations are terminal and return a boolean result.

返回布尔值，流匹配

```java
  boolean matchedResult = memberNames.stream()
      .anyMatch((s) -> s.startsWith("A"));

    System.out.println(matchedResult);

    matchedResult = memberNames.stream()
        .allMatch((s) -> s.startsWith("A"));

    System.out.println(matchedResult);

    matchedResult = memberNames.stream()
        .noneMatch((s) -> s.startsWith("A"));

    System.out.println(matchedResult);

```

##### Stream.count()

Count is a terminal operation returning the number of elements in the stream as a long.

返回流元素的数量

```java
  long totalMatched = memberNames.stream()
      .filter((s) -> s.startsWith("A"))
      .count();

	System.out.println(totalMatched);
```

##### Stream.reduce()

This terminal operation performs a reduction on the elements of the stream with the given function. The result is an Optional holding the reduced value.

*reduce*操作可以实现从一组元素中生成一个值，`sum()`、`max()`、`min()`、`count()`等都是*reduce*操作，将他们单独设为函数只是因为常用。`reduce()`的方法定义有三种重写形式：

- `Optional<T> reduce(BinaryOperator<T> accumulator)`
- `T reduce(T identity, BinaryOperator<T> accumulator)`
- `<U> U reduce(U identity, BiFunction<U,? super T,U> accumulator, BinaryOperator<U> combiner)`

虽然函数定义越来越长，但语义不曾改变，多的参数只是为了指明初始值（参数*identity*），或者是指定并行执行时多个部分结果的合并方式（参数*combiner*）。`reduce()`最常用的场景就是从一堆值中生成一个值。用这么复杂的函数去求一个最大或最小值，你是不是觉得设计者有病。其实不然，因为“大”和“小”或者“求和"有时会有不同的语义。

需求：*从一组单词中找出最长的单词*。这里“大”的含义就是“长”。

```java
// 找出最长的单词
Stream<String> stream = Stream.of("I", "love", "you", "too");
Optional<String> longest = stream.reduce((s1, s2) -> s1.length()>=s2.length() ? s1 : s2);
//Optional<String> longest = stream.max((s1, s2) -> s1.length()-s2.length());
System.out.println(longest.get());
```

上述代码会选出最长的单词*love*，其中*Optional*是（一个）值的容器，使用它可以避免*null*值的麻烦。当然可以使用`Stream.max(Comparator<? super T> comparator)`方法来达到同等效果，但`reduce()`自有其存在的理由。

[![Stream.reduce_parameter](https://github.com/CarpenterLee/JavaLambdaInternals/raw/master/Figures/Stream.reduce_parameter.png)](https://github.com/CarpenterLee/JavaLambdaInternals/blob/master/Figures/Stream.reduce_parameter.png)

需求：*求出一组单词的长度之和*。这是个“求和”操作，操作对象输入类型是*String*，而结果类型是*Integer*。

```
// 求单词长度之和
Stream<String> stream = Stream.of("I", "love", "you", "too");
Integer lengthSum = stream.reduce(0,　// 初始值　// (1)
        (sum, str) -> sum+str.length(), // 累加器 // (2)
        (a, b) -> a+b);　// 部分和拼接器，并行执行时才会用到 // (3)
// int lengthSum = stream.mapToInt(str -> str.length()).sum();
System.out.println(lengthSum);
```

上述代码标号(2)处将i. 字符串映射成长度，ii. 并和当前累加和相加。这显然是两步操作，使用`reduce()`函数将这两步合二为一，更有助于提升性能。如果想要使用`map()`和`sum()`组合来达到上述目的，也是可以的。

##### Boxed Stream

采用reduce进行数值操作会涉及到基本数值类型和引用数值类型之间的装箱、拆箱操作，因此效率较低。 当流操作为纯数值操作时，使用**数值流**能获得较高的效率。

**将普通流转换成数值流**

StreamAPI提供了三种数值流：IntStream、DoubleStream、LongStream，也提供了将普通流转换成数值流的三种方法：mapToInt、mapToDouble、mapToLong。 如，将Person中的age转换成数值流：

```
IntStream stream = list.stream().mapToInt(Person::getAge);
```

**数值计算**

每种数值流都提供了数值计算函数，如max、min、sum等。如，找出最大的年龄：

```
OptionalInt maxAge = list.stream()
                                .mapToInt(Person::getAge)
                                .max();
```

由于数值流可能为空，并且给空的数值流计算最大值是没有意义的，因此max函数返回OptionalInt，它是Optional的一个子类，能够判断流是否为空，并对流为空的情况作相应的处理。 此外，mapToInt、mapToDouble、mapToLong进行数值操作后的返回结果分别为：OptionalInt、OptionalDouble、OptionalLong

`reduce()`擅长的是生成一个值，如果想要从*Stream*生成一个集合或者*Map*等复杂的对象该怎么办呢？终极武器`collect()`横空出世！

##### Stream.collect()

**Collectors 类的静态工厂方法**

| 工厂方法            | 返回类型               | 用途                                                         | 示例                                                         |
| ------------------- | ---------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| `toList`            | `List<T>`              | 把流中所有项目收集到一个 List                                | `List<Project> projects = projectStream.collect(toList());`  |
| `toSet`             | `Set<T>`               | 把流中所有项目收集到一个 Set，删除重复项                     | `Set<Project> projects = projectStream.collect(toSet());`    |
| `toCollection`      | `Collection<T>`        | 把流中所有项目收集到给定的供应源创建的集合                   | `Collection<Project> projects = projectStream.collect(toCollection(), ArrayList::new);` |
| `counting`          | `Long`                 | 计算流中元素的个数                                           | `long howManyProjects = projectStream.collect(counting());`  |
| `summingInt`        | `Integer`              | 对流中项目的一个整数属性求和                                 | `int totalStars = projectStream.collect(summingInt(Project::getStars));` |
| `averagingInt`      | `Double`               | 计算流中项目 Integer 属性的平均值                            | `double avgStars = projectStream.collect(averagingInt(Project::getStars));` |
| `summarizingInt`    | `IntSummaryStatistics` | 收集关于流中项目 Integer 属性的统计值，例如最大、最小、 总和与平均值 | `IntSummaryStatistics projectStatistics = projectStream.collect(summarizingInt(Project::getStars));` |
| `joining`           | `String`               | 连接对流中每个项目调用 toString 方法所生成的字符串           | `String shortProject = projectStream.map(Project::getName).collect(joining(", "));` |
| `maxBy`             | `Optional<T>`          | 按照给定比较器选出的最大元素的 Optional， 或如果流为空则为 Optional.empty() | `Optional<Project> fattest = projectStream.collect(maxBy(comparingInt(Project::getStars)));` |
| `minBy`             | `Optional<T>`          | 按照给定比较器选出的最小元素的 Optional， 或如果流为空则为 Optional.empty() | `Optional<Project> fattest = projectStream.collect(minBy(comparingInt(Project::getStars)));` |
| `reducing`          | 归约操作产生的类型     | 从一个作为累加器的初始值开始，利用 BinaryOperator 与流中的元素逐个结合，从而将流归约为单个值 | `int totalStars = projectStream.collect(reducing(0, Project::getStars, Integer::sum));` |
| `collectingAndThen` | 转换函数返回的类型     | 包含另一个收集器，对其结果应用转换函数                       | `int howManyProjects = projectStream.collect(collectingAndThen(toList(), List::size));` |
| `groupingBy`        | `Map<K, List<T>>`      | 根据项目的一个属性的值对流中的项目作问组，并将属性值作 为结果 Map 的键 | `Map<String,List<Project>> projectByLanguage = projectStream.collect(groupingBy(Project::getLanguage));` |
| `partitioningBy`    | `Map<Boolean,List<T>>` | 根据对流中每个项目应用断言的结果来对项目进行分区             | `Map<Boolean,List<Project>> vegetarianDishes = projectStream.collect(partitioningBy(Project::isVegetarian));` |



collect() method used to recieve elements from a sream and store them in a collection and metioned in parameter funcion.

collect ( )方法，用于从Stream接收元素，并将它们存储在集合中，并在参数函数中进行赋值。

```java
List<String> memNamesInUppercase = memberNames.stream().sorted()
    .map(String::toUpperCase)
    .collect(Collectors.toList());
System.out.print(memNamesInUppercase);
```

不夸张的讲，如果你发现某个功能在*Stream*接口中没找到，十有八九可以通过`collect()`方法实现。`collect()`是*Stream*接口方法中最灵活的一个。

```java
// 将Stream转换成容器或Map
Stream<String> stream = Stream.of("I", "love", "you", "too");
List<String> list = stream.collect(Collectors.toList()); // (1)
// Set<String> set = stream.collect(Collectors.toSet()); // (2)
// Map<String, Integer> map = stream.collect(Collectors.toMap(Function.identity(), String::length)); // (3)
```

上述代码分别列举了如何将*Stream*转换成*List*、*Set*和*Map*。

`Collectors.toMap() internally uses Map.merge() to add mappings to the map. Map.merge() is spec'd not to allow null values, regardless of whether the underlying Map supports null values. This could probably use some clarification in the Collectors.toMap() specifications.`

前面已经说过*Stream*背后依赖于某种数据源，数据源可以是数组、容器等，但不能是*Map*。反过来从*Stream*生成*Map*是可以的，但我们要想清楚*Map*的*key*和*value*分别代表什么，根本原因是我们要想清楚要干什么。通常在三种情况下`collect()`的结果会是*Map*：

1. 使用`Collectors.toMap()`生成的收集器，用户需要指定如何生成*Map*的*key*和*value*。
2. 使用`Collectors.partitioningBy()`生成的收集器，对元素进行二分区操作时用到。
3. 使用`Collectors.groupingBy()`生成的收集器，对元素做*group*操作时用到。

情况1：使用`toMap()`生成的收集器，这种情况是最直接的，前面例子中已提到，这是和`Collectors.toCollection()`并列的方法。如下代码展示将学生列表转换成由<学生，GPA>组成的*Map*。非常直观，无需多言。

```java
// 使用toMap()统计学生GPA
Map<Student, Double> studentToGPA =
     students.stream().collect(Collectors.toMap(Function.identity(),// 如何生成key
   		student -> computeGPA(student)));// 如何生成value
```

情况2：使用`partitioningBy()`生成的收集器，这种情况适用于将`Stream`中的元素依据某个二值逻辑（满足条件，或不满足）分成互补相交的两部分，比如男女性别、成绩及格与否等。下列代码展示将学生分成成绩及格或不及格的两部分。

```java
// Partition students into passing and failing
Map<Boolean, List<Student>> passingFailing = students.stream()
	.collect(Collectors.partitioningBy(s -> s.getGrade() >= PASS_THRESHOLD));
```

情况3：使用`groupingBy()`生成的收集器，这是比较灵活的一种情况。跟SQL中的*group by*语句类似，这里的*groupingBy()也是按照某个属性对数据进行分组，属性相同的元素会被对应到Map*的同一个*key*上。下列代码展示将员工按照部门进行分组：

```java
// Group employees by department
Map<Department, List<Employee>> byDept = employees.stream()
	.collect(Collectors.groupingBy(Employee::getDepartment));
```

以上只是分组的最基本用法，有些时候仅仅分组是不够的。在SQL中使用*group by*是为了协助其他查询，比如*1. 先将员工按照部门分组，2. 然后统计每个部门员工的人数*。Java类库设计者也考虑到了这种情况，增强版的`groupingBy()`能够满足这种需求。增强版的`groupingBy()`允许我们对元素分组之后再执行某种运算，比如求和、计数、平均值、类型转换等。这种先将元素分组的收集器叫做**上游收集器**，之后执行其他运算的收集器叫做**下游收集器**(*downstream Collector*)。

```java
// 使用下游收集器统计每个部门的人数
Map<Department, Integer> totalByDept = employees.stream()
	.collect(Collectors.groupingBy(Employee::getDepartment,
		Collectors.counting()));// 下游收集器
```

上面代码的逻辑是不是越看越像SQL？高度非结构化。还有更狠的，下游收集器还可以包含更下游的收集器，这绝不是为了炫技而增加的把戏，而是实际场景需要。考虑将员工按照部门分组的场景，如果*我们想得到每个员工的名字（字符串），而不是一个个*Employee*对象*，可通过如下方式做到：

```java
// 按照部门对员工分布组，并只保留员工的名字
Map<Department, List<String>> byDept = employees.stream()
	.collect(Collectors.groupingBy(Employee::getDepartment,
    	Collectors.mapping(Employee::getName,// 下游收集器
        	Collectors.toList())));// 更下游的收集器
```

字符串拼接时使用`Collectors.joining()`生成的收集器，从此告别*for*循环。`Collectors.joining()`方法有三种重写形式，分别对应三种不同的拼接方式。无需多言，代码过目难忘。

```java
// 使用Collectors.joining()拼接字符串
Stream<String> stream = Stream.of("I", "love", "you");
//String joined = stream.collect(Collectors.joining());// "Iloveyou"
//String joined = stream.collect(Collectors.joining(","));// "I,love,you"
String joined = stream.collect(Collectors.joining(",", "{", "}"));// "{I,love,you}"
```

了可以使用*Collectors*工具类已经封装好的收集器，我们还可以自定义收集器，或者直接调用`collect(Supplier<R> supplier, BiConsumer<R,? super T> accumulator, BiConsumer<R,R> combiner)`方法，**收集任何形式你想要的信息**。不过*Collectors*工具类应该能满足我们的绝大部分需求，手动实现之间请先看看文档。

#### Stream short-circuit operations

尽管流操作是在满足谓词的集合中的所有元素上执行的，但是在迭代过程中每当遇到匹配的元素时，通常都希望中断该操作。在外部迭代中，您将使用if-else块。在内部迭代中，有一些特定的方法可以用于此目的。

```java
// anyMatch() -> 一旦条件 满足 predicate ，则返回true。它将不再处理任何元素。
boolean matched = memberNames.stream()
    .anyMatch((s) -> s.startsWith("A"));

System.out.println(matched);

// findFirst() -> 它将从流返回第一个元素，然后不再处理任何元素。
String firstMatchedName = memberNames.stream()
    .filter((s) -> s.startsWith("L"))
    .findFirst().get();

System.out.println(firstMatchedName);
```


#### Streams pipeline

这里我们来阐述一下，Streams 流水线工作原理。

##### 中间操作怎么保存跟记录

首先，我们上面介绍了Streams的很多中间操作，那么这些中间操作是如果保存跟记录呢，很多Stream操作会需要一个回调函数（Lambda表达式），因此一个完整的操作是<*数据来源，操作，回调函数*>构成的三元组。Stream中使用Stage的概念来描述一个完整的操作，并用某种实例化后的*PipelineHelper*来代表Stage，将具有先后顺序的各个Stage连到一起，就构成了整个流水线。跟Stream相关类和接口的继承关系图示。

![Java_stream_pipeline_classes](https://github.com/CarpenterLee/JavaLambdaInternals/raw/master/Figures/Java_stream_pipeline_classes.png)

还有*IntPipeline, LongPipeline, DoublePipeline*没在图中画出，这三个类专门为三种基本类型（不是包装类型）而定制的，跟*ReferencePipeline*是并列关系。图中*Head*用于表示第一个Stage，即调用调用诸如*Collection.stream()方法产生的Stage，很显然这个Stage里不包含任何操作；StatelessOp和StatefulOp*分别表示无状态和有状态的Stage，对应于无状态和有状态的中间操作。

Stream流水线组织结构示意图如下：

[![Stream_pipeline_example](https://github.com/CarpenterLee/JavaLambdaInternals/raw/master/Figures/Stream_pipeline_example.png)](https://github.com/CarpenterLee/JavaLambdaInternals/blob/master/Figures/Stream_pipeline_example.png)

图中通过`Collection.stream()`方法得到*Head*也就是stage0，紧接着调用一系列的中间操作，不断产生新的Stream。**这些Stream对象以双向链表的形式组织在一起，构成整个流水线，由于每个Stage都记录了前一个Stage和本次的操作以及回调函数，依靠这种结构就能建立起对数据源的所有操作**。这就是Stream记录操作的方式。

##### 中间操作怎么叠加

以上只是解决了操作记录的问题，要想让流水线起到应有的作用我们需要一种将所有操作叠加到一起的方案。你可能会觉得这很简单，只需要从流水线的head开始依次执行每一步的操作（包括回调函数）就行了。这听起来似乎是可行的，但是你忽略了前面的Stage并不知道后面Stage到底执行了哪种操作，以及回调函数是哪种形式。换句话说，只有当前Stage本身才知道该如何执行自己包含的动作。这就需要有某种协议来协调相邻Stage之间的调用关系。

这种协议由*Sink*接口完成，*Sink*接口包含的方法如下表所示：

| 方法名                          | 作用                                                         |
| ------------------------------- | ------------------------------------------------------------ |
| void begin(long size)           | 开始遍历元素之前调用该方法，通知Sink做好准备。               |
| void end()                      | 所有元素遍历完成之后调用，通知Sink没有更多的元素了。         |
| boolean cancellationRequested() | 是否可以结束操作，可以让短路操作尽早结束。                   |
| void accept(T t)                | 遍历元素时调用，接受一个待处理元素，并对元素进行处理。Stage把自己包含的操作和回调方法封装到该方法里，前一个Stage只需要调用当前Stage.accept(T t)方法就行了。 |

有了上面的协议，相邻Stage之间调用就很方便了，每个Stage都会将自己的操作封装到一个Sink里，前一个Stage只需调用后一个Stage的`accept()`方法即可，并不需要知道其内部是如何处理的。当然对于有状态的操作，Sink的`begin()`和`end()`方法也是必须实现的。比如Stream.sorted()是一个有状态的中间操作，其对应的Sink.begin()方法可能创建一个乘放结果的容器，而accept()方法负责将元素添加到该容器，最后end()负责对容器进行排序。对于短路操作，`Sink.cancellationRequested()`也是必须实现的，比如Stream.findFirst()是短路操作，只要找到一个元素，cancellationRequested()就应该返回*true*，以便调用者尽快结束查找。Sink的四个接口方法常常相互协作，共同完成计算任务。**实际上Stream API内部实现的的本质，就是如何重载Sink的这四个接口方法**。

有了Sink对操作的包装，Stage之间的调用问题就解决了，执行时只需要从流水线的head开始对数据源依次调用每个Stage对应的Sink.{begin(), accept(), cancellationRequested(), end()}方法就可以了。一种可能的Sink.accept()方法流程是这样的：

```java
void accept(U u){
    1. 使用当前Sink包装的回调函数处理u
    2. 将处理结果传递给流水线下游的Sink
}
```

Sink接口的其他几个方法也是按照这种[处理->转发]的模型实现。下面我们结合具体例子看看Stream的中间操作是如何将自身的操作包装成Sink以及Sink是如何将处理结果转发给下一个Sink的。先看Stream.map()方法：

```java
// Stream.map()，调用该方法将产生一个新的Stream
public final <R> Stream<R> map(Function<? super P_OUT, ? extends R> mapper) {
    ...
    return new StatelessOp<P_OUT, R>(this, StreamShape.REFERENCE,
                                 StreamOpFlag.NOT_SORTED | StreamOpFlag.NOT_DISTINCT) {
        @Override /*opWripSink()方法返回由回调函数包装而成Sink*/
        Sink<P_OUT> opWrapSink(int flags, Sink<R> downstream) {
            return new Sink.ChainedReference<P_OUT, R>(downstream) {
                @Override
                public void accept(P_OUT u) {
                    R r = mapper.apply(u);// 1. 使用当前Sink包装的回调函数mapper处理u
                    downstream.accept(r);// 2. 将处理结果传递给流水线下游的Sink
                }
            };
        }
    };
}
```

上述代码看似复杂，其实逻辑很简单，就是将回调函数*mapper*包装到一个Sink当中。由于Stream.map()是一个无状态的中间操作，所以map()方法返回了一个StatelessOp内部类对象（一个新的Stream），调用这个新Stream的opWripSink()方法将得到一个包装了当前回调函数的Sink。

再来看一个复杂一点的例子。Stream.sorted()方法将对Stream中的元素进行排序，显然这是一个有状态的中间操作，因为读取所有元素之前是没法得到最终顺序的。抛开模板代码直接进入问题本质，sorted()方法是如何将操作封装成Sink的呢？sorted()一种可能封装的Sink代码如下：

```java
// Stream.sort()方法用到的Sink实现
class RefSortingSink<T> extends AbstractRefSortingSink<T> {
    private ArrayList<T> list;// 存放用于排序的元素
    RefSortingSink(Sink<? super T> downstream, Comparator<? super T> comparator) {
        super(downstream, comparator);
    }
    @Override
    public void begin(long size) {
        ...
        // 创建一个存放排序元素的列表
        list = (size >= 0) ? new ArrayList<T>((int) size) : new ArrayList<T>();
    }
    @Override
    public void end() {
        list.sort(comparator);// 只有元素全部接收之后才能开始排序
        downstream.begin(list.size());
        if (!cancellationWasRequested) {// 下游Sink不包含短路操作
            list.forEach(downstream::accept);// 2. 将处理结果传递给流水线下游的Sink
        }
        else {// 下游Sink包含短路操作
            for (T t : list) {// 每次都调用cancellationRequested()询问是否可以结束处理。
                if (downstream.cancellationRequested()) break;
                downstream.accept(t);// 2. 将处理结果传递给流水线下游的Sink
            }
        }
        downstream.end();
        list = null;
    }
    @Override
    public void accept(T t) {
        list.add(t);// 1. 使用当前Sink包装动作处理t，只是简单的将元素添加到中间列表当中
    }
}
```

上述代码完美的展现了Sink的四个接口方法是如何协同工作的：

1. 首先begin()方法告诉Sink参与排序的元素个数，方便确定中间结果容器的的大小；
2. 之后通过accept()方法将元素添加到中间结果当中，最终执行时调用者会不断调用该方法，直到遍历所有元素；一个
3. 最后end()方法告诉Sink所有元素遍历完毕，启动排序步骤，排序完成后将结果传递给下游的Sink；
4. 如果下游的Sink是短路操作，将结果传递给下游时不断询问下游cancellationRequested()是否可以结束处理。

##### 操作叠加后如何执行

Sink完美封装了Stream每一步操作，并给出了[处理->转发]的模式来叠加操作。这一连串的齿轮已经咬合，就差最后一步拨动齿轮启动执行。是什么启动这一连串的操作呢？也许你已经想到了启动的原始动力就是结束操作(Terminal Operation)，一旦调用某个结束操作，就会触发整个流水线的执行。

结束操作之后不能再有别的操作，所以结束操作不会创建新的流水线阶段(Stage)，直观的说就是流水线的链表不会在往后延伸了。结束操作会创建一个包装了自己操作的Sink，这也是流水线中最后一个Sink，这个Sink只需要处理数据而不需要将结果传递给下游的Sink（因为没有下游）。对于Sink的[处理->转发]模型，结束操作的Sink就是调用链的出口。

我们再来考察一下上游的Sink是如何找到下游Sink的。一种可选的方案是在*PipelineHelper*中设置一个Sink字段，在流水线中找到下游Stage并访问Sink字段即可。但Stream类库的设计者没有这么做，而是设置了一个`Sink AbstractPipeline.opWrapSink(int flags, Sink downstream)`方法来得到Sink，该方法的作用是返回一个新的包含了当前Stage代表的操作以及能够将结果传递给downstream的Sink对象。为什么要产生一个新对象而不是返回一个Sink字段？这是因为使用opWrapSink()可以将当前操作与下游Sink（上文中的downstream参数）结合成新Sink。试想只要从流水线的最后一个Stage开始，不断调用上一个Stage的opWrapSink()方法直到最开始（不包括stage0，因为stage0代表数据源，不包含操作），就可以得到一个代表了流水线上所有操作的Sink，用代码表示就是这样：

```
// AbstractPipeline.wrapSink()
// 从下游向上游不断包装Sink。如果最初传入的sink代表结束操作，
// 函数返回时就可以得到一个代表了流水线上所有操作的Sink。
final <P_IN> Sink<P_IN> wrapSink(Sink<E_OUT> sink) {
    ...
    for (AbstractPipeline p=AbstractPipeline.this; p.depth > 0; p=p.previousStage) {
        sink = p.opWrapSink(p.previousStage.combinedFlags, sink);
    }
    return (Sink<P_IN>) sink;
}
```

现在流水线上从开始到结束的所有的操作都被包装到了一个Sink里，执行这个Sink就相当于执行整个流水线，执行Sink的代码如下：

```
// AbstractPipeline.copyInto(), 对spliterator代表的数据执行wrappedSink代表的操作。
final <P_IN> void copyInto(Sink<P_IN> wrappedSink, Spliterator<P_IN> spliterator) {
    ...
    if (!StreamOpFlag.SHORT_CIRCUIT.isKnown(getStreamAndOpFlags())) {
        wrappedSink.begin(spliterator.getExactSizeIfKnown());// 通知开始遍历
        spliterator.forEachRemaining(wrappedSink);// 迭代
        wrappedSink.end();// 通知遍历结束
    }
    ...
}
```

上述代码首先调用wrappedSink.begin()方法告诉Sink数据即将到来，然后调用spliterator.forEachRemaining()方法对数据进行迭代（Spliterator是容器的一种迭代器，[参阅](https://github.com/CarpenterLee/JavaLambdaInternals/blob/master/3-Lambda%20and%20Collections.md#spliterator)），最后调用wrappedSink.end()方法通知Sink数据处理结束。逻辑如此清晰。

##### 执行后的结果怎么保存

最后一个问题是流水线上所有操作都执行后，用户所需要的结果（如果有）在哪里？首先要说明的是不是所有的Stream结束操作都需要返回结果，有些操作只是为了使用其副作用(*Side-effects*)，比如使用`Stream.forEach()`方法将结果打印出来就是常见的使用副作用的场景（事实上，除了打印之外其他场景都应避免使用副作用），对于真正需要返回结果的结束操作结果存在哪里呢？

> 特别说明：副作用不应该被滥用，也许你会觉得在Stream.forEach()里进行元素收集是个不错的选择，就像下面代码中那样，但遗憾的是这样使用的正确性和效率都无法保证，因为Stream可能会并行执行。大多数使用副作用的地方都可以使用[归约操作](https://github.com/CarpenterLee/JavaLambdaInternals/blob/master/5-Streams%20API(II).md)更安全和有效的完成。

```
// 错误的收集方式
ArrayList<String> results = new ArrayList<>();
stream.filter(s -> pattern.matcher(s).matches())
      .forEach(s -> results.add(s));  // Unnecessary use of side-effects!
// 正确的收集方式
List<String>results =
     stream.filter(s -> pattern.matcher(s).matches())
             .collect(Collectors.toList());  // No side-effects!
```

回到流水线执行结果的问题上来，需要返回结果的流水线结果存在哪里呢？这要分不同的情况讨论，下表给出了各种有返回结果的Stream结束操作。

| 返回类型 | 对应的结束操作                    |
| -------- | --------------------------------- |
| boolean  | anyMatch() allMatch() noneMatch() |
| Optional | findFirst() findAny()             |
| 归约结果 | reduce() collect()                |
| 数组     | toArray()                         |

1. 对于表中返回boolean或者Optional的操作（Optional是存放 一个 值的容器）的操作，由于值返回一个值，只需要在对应的Sink中记录这个值，等到执行结束时返回就可以了。
2. 对于归约操作，最终结果放在用户调用时指定的容器中（容器类型通过[收集器](https://github.com/CarpenterLee/JavaLambdaInternals/blob/master/5-Streams%20API(II).md#%E6%94%B6%E9%9B%86%E5%99%A8)指定）。collect(), reduce(), max(), min()都是归约操作，虽然max()和min()也是返回一个Optional，但事实上底层是通过调用[reduce()](https://github.com/CarpenterLee/JavaLambdaInternals/blob/master/5-Streams%20API(II).md#%E5%A4%9A%E9%9D%A2%E6%89%8Breduce)方法实现的。
3. 对于返回是数组的情况，毫无疑问的结果会放在数组当中。这么说当然是对的，但在最终返回数组之前，结果其实是存储在一种叫做*Node*的数据结构中的。Node是一种多叉树结构，元素存储在树的叶子当中，并且一个叶子节点可以存放多个元素。这样做是为了并行执行方便。

### Parallelism in Java Steam

并行流

```java
List<Integer> list = new ArrayList<Integer>();
for(int i = 1; i< 10; i++){
    list.add(i);
}
//Here creating a parallel stream
Stream<Integer> stream = list.parallelStream();
Integer[] evenNumbersArr = stream.filter(i -> i%2 == 0).toArray(Integer[]::new);
System.out.print(evenNumbersArr);
```

## Concurrent

### Thread 和 Runnable

所有的现代操作系统都通过进程和线程来支持并发。进程是通常彼此独立运行的程序的实例，比如，如果你启动了一个Java程序，操作系统产生一个新的进程，与其他程序一起并行执行。 在这些进程的内部，我们使用线程并发执行代码，因此，我们可以最大限度的利用CPU可用的核心（core）。

Java从JDK1.0开始执行线程。在开始一个新的线程之前，你必须指定由这个线程执行的代码，通常称为task。这可以通过实现Runnable——一个定义了一个无返回值无参数的 `run()` 方法的函数接口。

#### 线程池

在执行一个异步任务或并发任务时，往往是通过直接 `new Thread()` 方法来创建新的线程，这样做弊端较多，更好的解决方案是合理地利用线程池，线程池的优势很明显，如下：

1. 降低系统资源消耗，通过重用已存在的线程，降低线程创建和销毁造成的消耗；
2. 提高系统响应速度，当有任务到达时，无需等待新线程的创建便能立即执行；
3. 方便线程并发数的管控，线程若是无限制的创建，不仅会额外消耗大量系统资源，更是占用过多资源而阻塞系统或oom等状况，从而降低系统的稳定性。线程池能有效管控线程，统一分配、调优，提供资源使用率；
4. 更强大的功能，线程池提供了定时、定期以及可控线程数等功能的线程池，使用方便简单。

#### 线程池用法

##### newCachedThreadPool

创建一个可缓存的无界线程池，该方法无参数。当线程池中的线程空闲时间超过60s则会自动回收该线程，当任务超过线程池的线程数则创建新线程。线程池的大小上限为 `Integer.MAX_VALUE`，可看做是无限大。

```
public void cachedThreadPoolDemo(){
    ExecutorService cachedThreadPool = Executors.newCachedThreadPool();
    for (int i = 0; i < 5; i++) {
        final int index = i;

        cachedThreadPool.execute(new Runnable() {

            @Override
            public void run() {
                System.out.println(Thread.currentThread().getName()+", index="+index);
            }
        });

        try {
            Thread.sleep(1000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}
```

运行结果：

```
pool-1-thread-1, index=0
pool-1-thread-1, index=1
pool-1-thread-1, index=2
pool-1-thread-1, index=3
pool-1-thread-1, index=4
```

从运行结果可以看出，整个过程都在同一个线程pool-1-thread-1中运行，后面线程复用前面的线程。

##### newFixedThreadPool

创建一个固定大小的线程池，该方法可指定线程池的固定大小，对于超出的线程会在 `LinkedBlockingQueue` 队列中等待。

```
public void fixedThreadPoolDemo(){
    ExecutorService fixedThreadPool = Executors.newFixedThreadPool(3);
    for (int i = 0; i < 6; i++) {
        final int index = i;

        fixedThreadPool.execute(new Runnable() {

            @Override
            public void run() {
                System.out.println(Thread.currentThread().getName()+", index="+index);
            }
        });

        try {
            Thread.sleep(1000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}
```

运行结果：

```
pool-1-thread-1, index=0
pool-1-thread-2, index=1
pool-1-thread-3, index=2
pool-1-thread-1, index=3
pool-1-thread-2, index=4
pool-1-thread-3, index=5
```

从运行结果可以看出，线程池大小为3，每休眠1s后将任务提交给线程池的各个线程轮番交错地执行。线程池的大小设置，可参数 `Runtime.getRuntime().availableProcessors()`。

##### newSingleThreadExecutor

创建只有一个线程的线程池，该方法无参数，所有任务都保存队列LinkedBlockingQueue中，等待唯一的单线程来执行任务，并保证所有任务按照指定顺序(FIFO或优先级)执行。

```
public void singleThreadExecutorDemo(){
    ExecutorService singleThreadExecutor = Executors.newSingleThreadExecutor();
    for (int i = 0; i < 3; i++) {
        final int index = i;

        singleThreadExecutor.execute(new Runnable() {

            @Override
            public void run() {
                System.out.println(Thread.currentThread().getName()+", index="+index);
            }
        });

        try {
            Thread.sleep(1000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}
```

运行结果：

```
pool-1-thread-1, index=0
pool-1-thread-1, index=1
pool-1-thread-1, index=2
```

从运行结果可以看出，所有任务都是在单一线程运行的。

##### newScheduledThreadPool

创建一个可定时执行或周期执行任务的线程池，该方法可指定线程池的核心线程个数。

```
public void scheduledThreadPoolDemo(){
    ScheduledExecutorService scheduledThreadPool = Executors.newScheduledThreadPool(3);
    //定时执行一次的任务，延迟1s后执行
    scheduledThreadPool.schedule(new Runnable() {

        @Override
        public void run() {
            System.out.println(Thread.currentThread().getName()+", delay 1s");
        }
    }, 1, TimeUnit.SECONDS);

    //周期性地执行任务，延迟2s后，每3s一次地周期性执行任务
    scheduledThreadPool.scheduleAtFixedRate(new Runnable() {

        @Override
        public void run() {
            System.out.println(Thread.currentThread().getName()+", every 3s");
        }
    }, 2, 3, TimeUnit.SECONDS);
}
```

运行结果：

```
pool-1-thread-1, delay 1s
pool-1-thread-1, every 3s
pool-1-thread-2, every 3s
pool-1-thread-2, every 3s
...
```

- `schedule(Runnable command, long delay, TimeUnit unit)`: 延迟一定时间后执行 `Runnable` 任务；
- `schedule(Callable callable, long delay, TimeUnit unit)`: 延迟一定时间后执行 `Callable` 任务；
- `scheduleAtFixedRate(Runnable command, long initialDelay, long period, TimeUnit unit)`: 延迟一定时间后，以间隔period时间的频率周期性地执行任务；
- `scheduleWithFixedDelay(Runnable command, long initialDelay, long delay,TimeUnit unit)`: 与 `scheduleAtFixedRate()` 方法很类似， 但是不同的是scheduleWithFixedDelay()方法的周期时间间隔是以上一个任务执行结束到下一个任务开始执行的间隔， 而scheduleAtFixedRate()方法的周期时间间隔是以上一个任务开始执行到下一个任务开始执行的间隔，也就是这一些任务系列的触发时间都是可预知的。

> ScheduledExecutorService 功能强大，对于定时执行的任务，建议多采用该方法。

**方法对比**

| 工厂方法                  | corePoolSize | maximumPoolSize   | keepAliveTime         | workQueue             |
| ------------------------- | ------------ | ----------------- | --------------------- | --------------------- |
| `newCachedThreadPool`     | 0            | Integer.MAX_VALUE | 60s`SynchronousQueue` |                       |
| `newFixedThreadPool`      | nThreads     | nThreads          | 0                     | `LinkedBlockingQueue` |
| `newSingleThreadExecutor` | 1            | 1                 | 0                     | `LinkedBlockingQueue` |
| `newScheduledThreadPool`  | corePoolSize | Integer.MAX_VALUE | 0                     | `DelayedWorkQueue`    |

其他参数都相同，其中线程工厂的默认类为 `DefaultThreadFactory`，线程饱和的默认策略为 `ThreadPoolExecutor.AbortPolicy`。

### 简单使用 Lock 锁

Java 5 中引入了新的锁机制——java.util.concurrent.locks 中的显式的互斥锁：Lock 接口，它提供了比 `synchronized` 更加广泛的锁定操作。 Lock 接口有 3 个实现它的类：ReentrantLock、ReetrantReadWriteLock.ReadLock 和 ReetrantReadWriteLock.WriteLock，即重入锁、读锁和写锁。 lock 必须被显式地创建、锁定和释放，为了可以使用更多的功能，一般用 ReentrantLock 为其实例化。为了保证锁最终一定会被释放（可能会有异常发生），要把互斥区放在 try 语句块内，并在 finally 语句块中释放锁，尤其当有 return 语句时，return 语句必须放在 try 字句中，以确保 unlock()不会过早发生，从而将数据暴露给第二个任务。因此，采用 lock 加锁和释放锁的一般形式如下：

```java
//默认使用非公平锁，如果要使用公平锁，需要传入参数true
Lock lock = new ReentrantLock();  
lock.lock();  
try {  
    // 更新对象的状态  
    // 捕获异常，必要时恢复到原来的不变约束  
    // 如果有return语句，放在这里  
} finally {  
    //锁必须在finally块中释放
    lock.unlock();
}
```

可重入锁，也叫做递归锁，指的是同一线程外层函数获得锁之后，内层递归函数仍然有获取该锁的代码，但不受影响。 在JAVA环境下 `ReentrantLock` 和 `synchronized` 都是可重入锁。

#### ReentrantReadWriteLock

读写锁：分为读锁和写锁，多个读锁不互斥，读锁与写锁互斥，这是由jvm自己控制的，你只要上好相应的锁即可。 如果你的代码只读数据，可以很多人同时读，但不能同时写，那就上读锁；如果你的代码修改数据，只能有一个人在写，且不能同时读取，那就上写锁。 总之，读的时候上读锁，写的时候上写锁！

`ReentrantReadWriteLock` 会使用两把锁来解决问题，一个读锁，一个写锁

- 线程进入读锁的前提条件
  - 没有其他线程的写锁
  - 没有写请求或者有写请求，但调用线程和持有锁的线程是同一个
- 线程进入写锁的前提条件
  - 没有其他线程的读锁
  - 没有其他线程的写锁

### StampedLock

`StampedLock` 是 java 8 在 `java.util.concurrent.locks` 新增的一个API。

`ReentrantReadWriteLock` 在沒有任何读锁和写锁时，才可以取得写入锁，这可用于实现了悲观读取。 然而，如果读取很多，写入很少的情况下，使用 `ReentrantReadWriteLock` 可能会使写入线程遭遇饥饿问题，也就是写入线程无法竞争到锁定而一直处于等待状态。 `StampedLock` 有三种模式的锁，用于控制读取/写入访问，StampedLock 的状态由版本和模式组成。 锁获取操作返回一个用于展示和访问锁状态的票据（stamp）变量，它用相应的锁状态表示并控制访问，数字0表示没有写锁被授权访问。 在读锁上分为悲观锁和乐观锁，锁释放以及其他相关方法需要使用邮戳（stamps）变量作为参数，如果他们和当前锁状态不符则失败，这三种模式为：

- 写入：方法writeLock可能为了获取独占访问而阻塞当前线程，返回一个stamp变量，能够在unlockWrite方法中使用从而释放锁。也提供了tryWriteLock。 当锁被写模式所占有，没有读或者乐观的读操作能够成功。
- 读取：方法readLock可能为了获取非独占访问而阻塞当前线程，返回一个stamp变量，能够在unlockRead方法中用于释放锁。也提供了tryReadLock。
- 乐观读取：方法 `tryOptimisticRead` 返回一个非 0 邮戳变量，仅在当前锁没有以写入模式被持有。如果在获得stamp变量之后没有被写模式持有，方法validate将返回true。 这种模式可以被看做一种弱版本的读锁，可以被一个写入者在任何时间打断。乐观读取模式仅用于短时间读取操作时经常能够降低竞争和提高吞吐量。

> 悲观锁（Pessimistic Lock），顾名思义，就是很悲观，每次去拿数据的时候都认为别人会修改，所以每次在拿数据的时候都会上锁，这样别人想拿这个数据就会block直到它拿到锁。 悲观锁：假定会发生并发冲突，屏蔽一切可能违反数据完整性的操作。 Java synchronized 就属于悲观锁的一种实现，每次线程要修改数据时都先获得锁，保证同一时刻只有一个线程能操作数据，其他线程则会被block。

> 乐观锁（Optimistic Lock），顾名思义，就是很乐观，每次去拿数据的时候都认为别人不会修改，所以不会上锁，但是在提交更新的时候会判断一下在此期间别人有没有去更新这个数据。 乐观锁适用于读多写少的应用场景，这样可以提高吞吐量。 乐观锁：假设不会发生并发冲突，只在提交操作时检查是否违反数据完整性。

### AtomicInteger

JDK1.5之后的java.util.concurrent.atomic包里，多了一批原子处理类。 AtomicBoolean、AtomicInteger、AtomicLong、AtomicReference。 主要用于在高并发环境下的高效程序处理,来帮助我们简化同步处理.

AtomicInteger，一个提供原子操作的Integer的类。 在Java语言中，++i和i++操作并不是线程安全的，在使用的时候，不可避免的会用到synchronized关键字。 而AtomicInteger则通过一种线程安全的加减操作接口。

```
public final int get()                  //获取当前的值
public final int getAndSet(int newValue)//获取当前的值，并设置新的值
public final int getAndIncrement()      //获取当前的值，并自增
public final int getAndDecrement()      //获取当前的值，并自减
public final int getAndAdd(int delta)   //获取当前的值，并加上预期的值
```

### LongAccumulator

`LongAdder` 是jdk1.8提供的累加器，基于 `Striped64` 实现。 它常用于状态采集、统计等场景。 AtomicLong也可以用于这种场景，但在线程竞争激烈的情况下，LongAdder要比AtomicLong拥有更高的吞吐量，但会耗费更多的内存空间。

`LongAccumulator` 和 `LongAdder` 类似，也基于Striped64实现。但要比LongAdder更加灵活(要传入一个函数接口)， LongAdder相当于是LongAccumulator的一种特例。

### Semaphore

Semaphore（信号量）是用来控制同时访问特定资源的线程数量，它通过协调各个线程，以保证合理的使用公共资源。 很多年以来，我都觉得从字面上很难理解Semaphore所表达的含义，只能把它比作是控制流量的红绿灯，比如XX马路要限制流量，只允许同时有一百辆车在这条路上行使， 其他的都必须在路口等待，所以前一百辆车会看到绿灯，可以开进这条马路，后面的车会看到红灯，不能驶入XX马路， 但是如果前一百辆中有五辆车已经离开了XX马路，那么后面就允许有5辆车驶入马路，这个例子里说的车就是线程，驶入马路就表示线程在执行， 离开马路就表示线程执行完成，看见红灯就表示线程被阻塞，不能执行。

**应用场景**

Semaphore可以用于做流量控制，特别公用资源有限的应用场景，比如数据库连接。 假如有一个需求，要读取几万个文件的数据，因为都是IO密集型任务，我们可以启动几十个线程并发的读取， 但是如果读到内存后，还需要存储到数据库中，而数据库的连接数只有10个， 这时我们必须控制只有十个线程同时获取数据库连接保存数据，否则会报错无法获取数据库连接。 这个时候，我们就可以使用Semaphore来做流控。

## Time API

### ZoneId

Java 8中的时区操作被很大程度上简化了，新的时区类 `java.time.ZoneId` 是原有的 `java.util.TimeZone` 类的替代品。 ZoneId对象可以通过 `ZoneId.of()` 方法创建，也可以通过 `ZoneId.systemDefault()` 获取系统默认时区：

```java
ZoneId shanghaiZoneId = ZoneId.of("Asia/Shanghai");
ZoneId systemZoneId = ZoneId.systemDefault();
```

`of()` 方法接收一个“区域/城市”的字符串作为参数，你可以通过 `getAvailableZoneIds()` 方法获取所有合法的“区域/城市”字符串：

```java
Set<String> zoneIds = ZoneId.getAvailableZoneIds();
```

对于老的时区类 `TimeZone`，Java 8也提供了转化方法：

```java
ZoneId oldToNewZoneId = TimeZone.getDefault().toZoneId();
```

有了 `ZoneId`，我们就可以将一个 `LocalDate`、`LocalTime` 或 `LocalDateTime` 对象转化为 `ZonedDateTime` 对象：

```java
LocalDateTime localDateTime = LocalDateTime.now();
ZonedDateTime zonedDateTime = ZonedDateTime.of(localDateTime, shanghaiZoneId);
```

`ZonedDateTime` 对象由两部分构成，`LocalDateTime` 和 `ZoneId`，其中 `2018-03-03T15:26:56.147` 部分为 `LocalDateTime`，`+08:00[Asia/Shanghai]` 部分为ZoneId。

另一种表示时区的方式是使用 `ZoneOffset`，它是以当前时间和 **世界标准时间（UTC）/格林威治时间（GMT）** 的偏差来计算，例如：

```java
ZoneOffset zoneOffset = ZoneOffset.of("+09:00");
LocalDateTime localDateTime = LocalDateTime.now();
OffsetDateTime offsetDateTime = OffsetDateTime.of(localDateTime, zoneOffset);
```

### Instant

Instant类在Java日期与时间功能中，表示了时间线上一个确切的点，定义为距离初始时间的时间差（初始时间为GMT 1970年1月1日00:00）经测量一天有86400秒，从初始时间开始不断向前移动。

**创建一个Instant实例**

你可以通过Instant类的工厂方法创建一个Instant实例，例如你可以调用instant.now()来创建一个确切的表达当前时间的Instant对象：

```java
Instant now = Instant.now();
```

另外也有一些其它方法能创建Instant，具体请查阅Java官方文档。

**访问Instant的时间**

一个Instant对象里有两个域：距离初始时间的秒钟数、在当前一秒内的第几纳秒，他们的组合表达了当前时间点。你可以通过以下两个方法得到它们的值：

```java
long seconds =  getEpochSecond()
int nanos   =   getNano()
```

**Instant的计算**

Instant类有一些方法，可以用于获得另一Instant的值，例如：

- `plusSeconds()`
- `plusMillis()`
- `plusNanos()`
- `minusSeconds()`
- `minusMillis()`
- `minusNanos()`

我下面将向你展示两个例子，来说明这些方法如何使用：

```java
Instant now     = Instant.now();
Instant later   = now.plusSeconds(3);
Instant earlier = now.minusSeconds(3);
```

第一行获得了一个Instant对象，表示当前时间。第二行创建了一个Instant表示三秒后，第三行创建了一个Instant表示三秒前。

> seconds 表示从 `1970-01-01 00:00:00` 开始到现在的秒数，nanos 表示纳秒部分（nanos的值不会超过999,999,999）

### Clock

Clock类提供了访问当前日期和时间的方法，Clock是时区敏感的，可以用来取代 `System.currentTimeMillis()` 来获取当前的微秒数。 某一个特定的时间点也可以使用Instant类来表示，Instant 类也可以用来创建老的 `java.util.Date` 对象。

```java
Clock clock = Clock.systemDefaultZone();  
long millis = clock.millis();    
Instant instant = clock.instant();  
Date legacyDate = Date.from(instant);   // legacy java.util.Date  
```

### LocalDate

LocalDate类是Java 8中日期时间功能里表示一个本地日期的类，它的日期是无时区属性的。 可以用来表示生日、节假日期等等。这个类用于表示一个确切的日期，而不是这个日期所在的时间（如java.util.Date中的2000.01.01表示的实际是这一天的00:00这个瞬间）。

LocalDate类位于java.time包下，名叫java.time.LocalDate，创建出来的实例也是不可变对象，所以涉及它的计算方法将返回一个新的LocalDate。

**创建一个LocalDate实例**

我们有多种方式可以创建出 `LocalDate` 实例。第一种方法是使用 `now()` 方法获得值为今天当日的 `LocalDate` 对象：

```java
LocalDate localDate = LocalDate.now();
```

另一种方法是使用年月日信息构造出LocalDate对象：

```java
LocalDate localDate2 = LocalDate.of(2018, 3, 3);
```

LocalDate 的 `of()` 方法创建出一个指定年月日的日期，并且没有时区信息。

**访问日期信息**

可以用如下方法访问LocalDate中的日期信息：

```java
int   year       = localDate.getYear();
Month month      = localDate.getMonth();
int   dayOfMonth = localDate.getDayOfMonth();
int   dayOfYear  = localDate.getDayOfYear();
DayOfWeek dayOfWeek = localDate.getDayOfWeek();
```

可以注意到getMonth()与getDayOfWeek()方法返回了一个枚举类型代替一个int。你可以通过枚举类型中的getValue()来获得信息。

**LocalDate计算**

你可以进行一堆简单的日期计算，只要使用如下的方法：

- `plusDays()`
- `plusWeeks()`
- `plusMonths()`
- `plusYears()`
- `minusDays()`
- `minusWeeks()`
- `minusMonths()`
- `minusYears()`

以下举几个使用的例子来帮助理解使用：

```java
LocalDate d  = LocalDate.of(2018, 3, 5);
LocalDate d1 = localDate.plusYears(3);
LocalDate d2 = localDate.minusYears(3);
```

1. 第一行创建出一个新的LocalDate对象d，表示2018.3.5。
2. 第二行创建了值等于d日期3年后的LocalDate对象，第三行也是一样，只是值改为d日期的三年前。

### LocalTime

LocalTime类是Java 8中日期时间功能里表示一整天中某个时间点的类，它的时间是无时区属性的（早上10点等等）。比如你需要描述学校几点开学，这个时间不涉及在什么城市，这个描述是对任何国家城市都适用的，此时使用无时区的LocalTime就足够了。 LocalTime类的对象也是不可变的，所以计算方法会返回一个新的LocalTime实例。

**创建一个LocatTime实例**

有多种方式可以新建LocalTime实例。比如使用当前时间作为值新建对象：

```java
LocalTime localTime = LocalTime.now();
```

另一种方式是使用指定的时分秒和纳秒来新建对象：

```java
LocalTime localTime2 = LocalTime.of(21, 30, 59, 11001);
```

也有另一种版本的 `of()` 方法只需要小时分钟两项，或时分秒三项值作为参数。

**访问LocalTime对象的时间**

你可以通过这些方法访问其时、分、秒、纳秒：

- `getHour()`
- `getMinute()`
- `getSecond()`
- `getNano()`

**LocalTime的计算**

LocalTime类包含一系列方法，能帮你完成时间计算：

- `plusHours()`
- `plusMinutes()`
- `plusSeconds()`
- `plusNanos()`
- `minusHours()`
- `minusMinutes()`
- `minusSeconds()`
- `minusNanos()`

以下举一个例子：

```java
LocalTime localTime2 = LocalTime.of(21, 30, 59, 11001);
LocalTime localTimeLater   = localTime.plusHours(3);
LocalTime localTimeEarlier = localTime.minusHours(3);
```

1. 第一行新建一个LocalTime实例，表示21:30:50的第11001纳秒。
2. 第二行新建了一个LocalTime实例表示这个时间的三小时后，第三行表示三小时前。
3. LocalTime类是Java 8中日期时间功能里表示一整天中某个时间点的类，它的时间是无时区属性的（早上10点等等）。比如你需要描述学校几点开学，这个时间不涉及在什么城市，这个描述是对任何国家城市都适用的，此时使用无时区的LocalTime就足够了。

LocalTime类的对象也是不可变的，所以计算方法会返回一个新的LocalTime实例。

### LocalDateTime

LocalDateTime类是Java 8中日期时间功能里，用于表示当地的日期与时间的类，它的值是无时区属性的。你可以将其视为Java 8中LocalDate与LocalTime两个类的结合。

LocalDateTime类的值是不可变的，所以其计算方法会返回一个新的LocalDateTime实例。

**创建一个LocatDateTime实例**

可以通过LocalDateTime的静态工厂方法来创建LocalDateTime实例。以下举例使用 `now()` 方法创建：

```java
LocalDateTime localDateTime = LocalDateTime.now();
```

另一种方式是使用指定的年月日、时分秒、纳秒来新建对象：

```java
LocalDateTime localDateTime2 = LocalDateTime.of(2018, 11, 26, 13, 55, 36, 123);
```

**访问LocalDateTime对象的时间**

你可以通过这些方法访问其日期时间：

- `getYear()`
- `getMonth()`
- `getDayOfMonth()`
- `getDayOfWeek()`
- `getDayOfYear()`
- `getHour()`
- `getMinute()`
- `getSecond()`
- `getNano()`

这些方法中有一些返回int有一些返回枚举类型，你可以通过枚举类型中的 `getValue()` 方法来获得int值。

**LocalDateTime的计算**

LocalDateTime 类包含一系列方法，能帮你完成时间计算：

- `plusYears()`
- `plusMonths()`
- `plusDays()`
- `plusHours()`
- `plusMinutes()`
- `plusSeconds()`
- `plusNanos()`
- `minusYears()`
- `minusMonths()`
- `minusDays()`
- `minusHours()`
- `minusMinutes()`
- `minusSeconds()`
- `minusNanos()`

以下举一个例子：

```
LocalDateTime localDateTime  = LocalDateTime.now();
LocalDateTime localDateTime1 = localDateTime.plusYears(3);
LocalDateTime localDateTime2 = localDateTime.minusYears(3);
```

1. 第一行新建一个LocalDateTime实例表示当前这个时间。
2. 第二行新建了一个LocalDateTime实例表示三年后。
3. 第三行也新建了一个LocalDateTime实例表示三小时前。

### ZonedDateTime

ZonedDateTime类是Java 8中日期时间功能里，用于表示带时区的日期与时间信息的类。可以用于表示一个真实事件的开始时间，如某火箭升空时间等等。

ZonedDateTime 类的值是不可变的，所以其计算方法会返回一个新的ZonedDateTime 实例。

**创建一个ZonedDateTime实例**

有多种方式可以新建ZonedDateTime实例。比如使用当前时间作为值新建对象：

```java
ZonedDateTime dateTime = ZonedDateTime.now();
```

另一种方式是使用指定的年月日、时分秒、纳秒以及时区ID来新建对象：

```java
ZoneId zoneId = ZoneId.of("UTC+1");
ZonedDateTime dateTime2 = ZonedDateTime.of(2015, 11, 30, 23, 45, 59, 1234, zoneId);
```

**访问ZonedDateTime对象的时间**

你可以通过这些方法访问其日期时间：

- `getYear()`
- `getMonth()`
- `getDayOfMonth()`
- `getDayOfWeek()`
- `getDayOfYear()`
- `getHour()`
- `getMinute()`
- `getSecond()`
- `getNano()`

这些方法中有一些返回int有一些返回枚举类型，但可以通过枚举类型中的getValue()方法来获得int值。

**ZonedDateTime的计算**

ZonedDateTime类包含一系列方法，能帮你完成时间计算：

- `plusYears()`
- `plusMonths()`
- `plusDays()`
- `plusHours()`
- `plusMinutes()`
- `plusSeconds()`
- `plusNanos()`
- `minusYears()`
- `minusMonths()`
- `minusDays()`
- `minusHours()`
- `minusMinutes()`
- `minusSeconds()`
- `minusNanos()`

但注意计算时，若不巧跨越了夏令时（会补一小时或减一小时），可能得不到希望的结果。一个替代的正确做法是使用Period：

```java
ZonedDateTime zoneDateTime = previousDateTime.plus(Period.ofDays(3));
```

**时区**

时区是用ZoneId类表示的，你可以使用ZoneId.now()或ZoneId.of(“xxx”)来实例化：

```
ZoneId zoneId = ZoneId.of("UTC+1");
```

传给 `of()` 方法的参数是时区的ID，如“UTC+1”指距离UTC（格林威治时间）有一小时的时差，你可以使用你想要的时差来表示ZoneId（如+1与-5等等） 你也可以使用另一种方式表示zone id，即使用地区名字，也是可以的：

```java
ZoneId zoneId2 = ZoneId.of("Europe/Copenhagen");
ZoneId zoneId3 = ZoneId.of("Europe/Paris");
```

### DateTimeFormatter

DateTimeFormatter类是Java 8中日期时间功能里，用于解析和格式化日期时间的类，位于 `java.time.format` 包下。

**预定义的DateTimeFormatter实例**

DateTimeFormatter类包含一系列预定义（常量）的实例，可以解析和格式化一些标准时间格式。这将让你免除麻烦的时间格式定义，类中包含如下预定义的实例：

```java
BASIC_ISO_DATE

ISO_LOCAL_DATE
ISO_LOCAL_TIME
ISO_LOCAL_DATE_TIME

ISO_OFFSET_DATE
ISO_OFFSET_TIME
ISO_OFFSET_DATE_TIME

ISO_ZONED_DATE_TIME

ISO_INSTANT

ISO_DATE
ISO_TIME
ISO_DATE_TIME

ISO_ORDINAL_TIME
ISO_WEEK_DATE

RFC_1123_DATE_TIME
```

每个预定义的DateTimeFormatter实例都有不同的日期格式，我就不解释全部的了。具体的可以查阅Java官方文档，但我在这篇的后续中会解释其中几个，以方便理解。

**它的格式化日期**

当你获取一个DateTimeFormatter实例后，就可以用format()方便来将一个日期格式化为某种字符串，例如：

```java
DateTimeFormatter formatter = DateTimeFormatter.BASIC_ISO_DATE;
String formattedDate = formatter.format(LocalDate.now());
System.out.println(formattedDate);
```

这个样例把LocalDate对象格式化了，并输出20150703，这个输出表示现在2018年，3月5日。 再举一个关于ZonedDateTime的例子：

```java
DateTimeFormatter formatter = DateTimeFormatter.BASIC_ISO_DATE;
String formattedZonedDate = formatter.format(ZonedDateTime.now());
System.out.println("formattedZonedDate = " + formattedZonedDate);
```

这个例子会输出：20180305+0800 表示今年2018年，3月5日，位于UTC+8时区。

### Duration

一个Duration对象表示两个Instant间的一段时间，是在Java 8中加入的新功能。

一个Duration实例是不可变的，当创建出对象后就不能改变它的值了。你只能通过Duration的计算方法，来创建出一个新的Durtaion对象。你会在之后的教程中见到的。

**创建Duration实例**

使用 `Duration` 类的工厂方法来创建一个 `Duration` 对象，以下是一个使用 `between()` 的例子：

```java
Instant first = Instant.now();
// wait some time while something happens
Instant second = Instant.now();
Duration duration = Duration.between(first, second);
```

**访问Duration的时间**

一个Duration对象里有两个域：纳秒值（小于一秒的部分），秒钟值（一共有几秒），他们的组合表达了时间长度。注意屯使用System.getCurrentTimeMillis()时不同，Duration不包含毫秒这个属性。 你可以通过以下两个方法得到它们的值：

```
long seconds =  getSeconds()
int nanos   =   getNano()
```

你也可以转换整个时间到其它单位如纳秒、分钟、小时、天：

- `toNanos()`
- `toMillis()`
- `toMinutes()`
- `toHours()`
- `toDays()`

举例而言：`toNanos()` 与 `getNano()` 不同，`toNanos()` 获得的是 `Duration` 整个时间共有多少纳秒， 而 `getNano()` 只是获得这段时间中小于一秒的部分。

你也许会问，为什么没有 `toSeconds()` 方法，因为已经有 `getSeconds()` 这个方法能达到同样的功能了。

**Duration计算**

Duration类包含一系列的计算方法：

- `plusNanos()`
- `plusMillis()`
- `plusSeconds()`
- `plusMinutes()`
- `plusHours()`
- `plusDays()`
- `minusNanos()`
- `minusMillis()`
- `minusSeconds()`
- `minusMinutes()`
- `minusHours()`
- `minusDays()`

这些方法所做的事都是相似的，我在这儿也不展示内部实现细节了，就展示一个加减的例子吧：

```java
Duration start = ... //obtain a start duration
Duration added      = start.plusDays(3);
Duration subtracted = start.minusDays(3);
```

1. 第一行创建了一个Duration对象叫start，具体怎么创建可以参考前面的代码。
2. 第二三行样例创建了两个新的Duration，通过调用start的加减操作，使得added对象表示的时间比start多三天，而substracted则少三天。

所有的计算方法都会返回一个新的Duration，以保证Duration的不可变属性。

```java
long days = duration.toDays();              // 这段时间的总天数
long hours = duration.toHours();            // 这段时间的小时数
long minutes = duration.toMinutes();        // 这段时间的分钟数
long seconds = duration.getSeconds();       // 这段时间的秒数
long milliSeconds = duration.toMillis();    // 这段时间的毫秒数
long nanoSeconds = duration.toNanos();      // 这段时间的纳秒数
```

### 其他操作

**增加和减少日期**

Java 8中的日期/时间类都是不可变的，这是为了保证线程安全。当然，新的日期/时间类也提供了方法用于创建对象的可变版本，比如增加一天或者减少一天：

```java
LocalDate date = LocalDate.of(2017, 1, 5);          // 2017-01-05

LocalDate date1 = date.withYear(2016);              // 修改为 2016-01-05
LocalDate date2 = date.withMonth(2);                // 修改为 2017-02-05
LocalDate date3 = date.withDayOfMonth(1);           // 修改为 2017-01-01

LocalDate date4 = date.plusYears(1);                // 增加一年 2018-01-05
LocalDate date5 = date.minusMonths(2);              // 减少两个月 2016-11-05
LocalDate date6 = date.plus(5, ChronoUnit.DAYS);    // 增加5天 2017-01-10
```

上面例子中对于日期的操作比较简单，但是有些时候我们要面临更复杂的时间操作，比如将时间调到下一个工作日， 或者是下个月的最后一天，这时候我们可以使用 `with()` 方法的另一个重载方法，它接收一个TemporalAdjuster参数， 可以使我们更加灵活的调整日期：

```java
LocalDate date7 = date.with(nextOrSame(DayOfWeek.SUNDAY));      // 返回下一个距离当前时间最近的星期日
LocalDate date9 = date.with(lastInMonth(DayOfWeek.SATURDAY));   // 返回本月最后一个星期六
```

要使上面的代码正确编译，你需要使用静态导入 `TemporalAdjusters` 对象：

```java
import static java.time.temporal.TemporalAdjusters.*;
```

`TemporalAdjusters` 类中包含了很多静态方法可以直接使用，下面的表格列出了一些方法：

| 方法名                        | 描述                                                        |
| ----------------------------- | ----------------------------------------------------------- |
| `dayOfWeekInMonth`            | 返回同一个月中每周的第几天                                  |
| `firstDayOfMonth`             | 返回当月的第一天                                            |
| `firstDayOfNextMonth`         | 返回下月的第一天                                            |
| `firstDayOfNextYear`          | 返回下一年的第一天                                          |
| `firstDayOfYear`              | 返回本年的第一天                                            |
| `firstInMonth`                | 返回同一个月中第一个星期几                                  |
| `lastDayOfMonth`              | 返回当月的最后一天                                          |
| `lastDayOfNextMonth`          | 返回下月的最后一天                                          |
| `lastDayOfNextYear`           | 返回下一年的最后一天                                        |
| `lastDayOfYear`               | 返回本年的最后一天                                          |
| `lastInMonth`                 | 返回同一个月中最后一个星期几                                |
| `next / previous`             | 返回后一个/前一个给定的星期几                               |
| `nextOrSame / previousOrSame` | 返回后一个/前一个给定的星期几，如果这个值满足条件，直接返回 |

如果上面表格中列出的方法不能满足你的需求，你还可以创建自定义的 `TemporalAdjuster` 接口的实现，`TemporalAdjuster` 也是一个函数式接口，所以我们可以使用Lambda表达式：

```java
@FunctionalInterface
public interface TemporalAdjuster {
    Temporal adjustInto(Temporal temporal);
}
```

比如给定一个日期，计算该日期的下一个工作日（不包括星期六和星期天）：

```java
LocalDate date = LocalDate.of(2017, 1, 5);
date.with(temporal -> {
    // 当前日期
    DayOfWeek dayOfWeek = DayOfWeek.of(temporal.get(ChronoField.DAY_OF_WEEK));

    // 正常情况下，每次增加一天
    int dayToAdd = 1;

    // 如果是星期五，增加三天
    if (dayOfWeek == DayOfWeek.FRIDAY) {
        dayToAdd = 3;
    }
    
    // 如果是星期六，增加两天
    if (dayOfWeek == DayOfWeek.SATURDAY) {
        dayToAdd = 2;
    }

    return temporal.plus(dayToAdd, ChronoUnit.DAYS);
});
```

### 其他历法

Java中使用的历法是ISO 8601日历系统，它是世界民用历法，也就是我们所说的公历。平年有365天，闰年是366天。闰年的定义是：非世纪年，能被4整除；世纪年能被400整除。为了计算的一致性，公元1年的前一年被当做公元0年，以此类推。

此外Java 8还提供了4套其他历法（很奇怪为什么没有汉族人使用的农历），每套历法都包含一个日期类，分别是：

- `ThaiBuddhistDate`：泰国佛教历
- `MinguoDate`：中华民国历
- `JapaneseDate`：日本历
- `HijrahDate`：伊斯兰历

每个日期类都继承 `ChronoLocalDate` 类，所以可以在不知道具体历法的情况下也可以操作。不过这些历法一般不常用，除非是有某些特殊需求情况下才会使用。

这些不同的历法也可以用于向公历转换：

```java
LocalDate date = LocalDate.now();
JapaneseDate jpDate = JapaneseDate.from(date);
```

由于它们都继承ChronoLocalDate类，所以在不知道具体历法情况下，可以通过ChronoLocalDate类操作日期：

```java
Chronology jpChronology = Chronology.ofLocale(Locale.JAPANESE);
ChronoLocalDate jpChronoLocalDate = jpChronology.dateNow();
```

我们在开发过程中应该尽量避免使用 `ChronoLocalDate`，尽量用与历法无关的方式操作时间，因为不同的历法计算日期的方式不一样， 比如开发者会在程序中做一些假设，假设一年中有12个月，如果是中国农历中包含了闰月，一年有可能是13个月， 但开发者认为是12个月，多出来的一个月属于明年的。 再比如假设年份是累加的，过了一年就在原来的年份上加一，但日本天皇在换代之后需要重新纪年，所以过了一年年份可能会从1开始计算。

在实际开发过程中建议使用 `LocalDate`，包括存储、操作、业务规则的解读；除非需要将程序的输入或者输出本地化， 这时可以使用 `ChronoLocalDate` 类。

## Nashorn

个人感觉用的不是很多，不感兴趣的可以直接略过。

[原文地址](<https://winterbe.com/posts/2014/04/05/java8-nashorn-tutorial/>)

### 使用 Nashron

Nashorn JavaScript引擎可以在Java代码中编程调用，也可以通过命令行工具`jjs`使用，它在`$JAVA_HOME/bin`中。如果打算使用`jjs`，你可能希望设置符号链接来简化访问：

```bash
$ cd /usr/bin
$ ln -s $JAVA_HOME/bin/jjs jjs
$ jjs
jjs> print('Hello World');
```

这个教程专注于在Java代码中调用Nashron，所以让我们先跳过`jjs`。Java代码中简单的**HelloWorld** 如下所示：

```java
ScriptEngine engine = new ScriptEngineManager().getEngineByName("nashorn");
engine.eval("print('Hello World!');");
```

为了在Java中执行JavaScript，你首先要通过`javax.script`包创建脚本引擎。这个包已经在[Rhino](http://link.zhihu.com/?target=https%3A//developer.mozilla.org/en-US/docs/Mozilla/Projects/Rhino)（来源于Mozilla、Java中的遗留JS引擎）中使用了。

JavaScript代码既可以通过传递JavaScript代码字符串，也可以传递指向你的JS脚本文件的`FileReader`来执行：

```java
ScriptEngine engine = new ScriptEngineManager().getEngineByName("nashorn");
engine.eval(new FileReader("script.js"));
```

Nashorn JavaScript基于[ECMAScript 5.1](http://link.zhihu.com/?target=http%3A//es5.github.io/)，但是它的后续版本会对ES6提供支持：

> *Nashorn的当前策略遵循ECMAScript规范。当我们在JDK8中发布它时，它将基于ECMAScript 5.1。Nashorn未来的主要发布基于ECMAScript 6。*

Nashorn定义了大量对**ECMAScript**标准的语言和API扩展。但是首先让我们看一看Java和JavaScript代码如何交互。

### 在Java中调用JavaScript函数

Nashorn 支持从Java代码中直接调用定义在脚本文件中的JavaScript函数。你可以将Java对象传递为函数参数，并且从函数返回数据来调用Java方法。

下面的JavaScript函数稍后会在Java端调用：

```js
var fun1 = function(name) {
    print('Hi there from Javascript, ' + name);
    return "greetings from javascript";
};
var fun2 = function (object) {
    print("JS Class Definition: " + Object.prototype.toString.call(object));
};
```

为了调用函数，你首先需要将脚本引擎转换为`Invocable`。`Invocable`接口由`NashornScriptEngine`实现，并且定义了`invokeFunction`方法来调用指定名称的JavaScript函数。

```java
ScriptEngine engine = new ScriptEngineManager().getEngineByName("nashorn");
engine.eval(new FileReader("script.js"));
Invocable invocable = (Invocable) engine;
Object result = invocable.invokeFunction("fun1", "Peter Parker");
System.out.println(result);
System.out.println(result.getClass());
// Hi there from Javascript, Peter Parker
// greetings from javascript
// class java.lang.String
```

执行这段代码会在控制台产生三行结果。调用函数`print`将结果输出到`System.out`，所以我们会首先看到JavaScript输出。

现在让我们通过传入任意Java对象来调用第二个函数：

```java
invocable.invokeFunction("fun2", new Date());
// [object java.util.Date]
invocable.invokeFunction("fun2", LocalDateTime.now());
// [object java.time.LocalDateTime]
invocable.invokeFunction("fun2", new Person());
// [object com.winterbe.java8.Person]
```

Java对象在传入时不会在JavaScript端损失任何类型信息。由于脚本在JVM上原生运行，我们可以在Nashron上使用Java API或外部库的全部功能。

### 在JavaScript中调用Java方法

在JavaScript中调用Java方法十分容易。我们首先需要定义一个Java静态方法。

```java
static String fun1(String name) {
    System.out.format("Hi there from Java, %s", name);
    return "greetings from java";
}
```

Java类可以通过`Java.type`API扩展在JavaScript中引用。它就和Java代码中的`import`类似。只要定义了Java类型，我们就可以自然地调用静态方法`fun1()`，然后像`sout`打印信息。由于方法是静态的，我们不需要首先创建实例。

```java
var MyJavaClass = Java.type('my.package.MyJavaClass');
var result = MyJavaClass.fun1('John Doe');
print(result);
// Hi there from Java, John Doe
// greetings from java
```

在使用JavaScript原生类型调用Java方法时，Nashorn 如何处理类型转换？让我们通过简单的例子来弄清楚。

下面的Java方法简单打印了方法参数的实际类型：

```java
static void fun2(Object object) {
    System.out.println(object.getClass());
}
```

为了理解背后如何处理类型转换，我们使用不同的JavaScript类型来调用这个方法：

```java
MyJavaClass.fun2(123);
// class java.lang.Integer
MyJavaClass.fun2(49.99);
// class java.lang.Double
MyJavaClass.fun2(true);
// class java.lang.Boolean
MyJavaClass.fun2("hi there")
// class java.lang.String
MyJavaClass.fun2(new Number(23));
// class jdk.nashorn.internal.objects.NativeNumber
MyJavaClass.fun2(new Date());
// class jdk.nashorn.internal.objects.NativeDate
MyJavaClass.fun2(new RegExp());
// class jdk.nashorn.internal.objects.NativeRegExp
MyJavaClass.fun2({foo: 'bar'});
// class jdk.nashorn.internal.scripts.JO4
```

JavaScript原始类型转换为合适的Java包装类，而JavaScript原生对象会使用内部的适配器类来表示。要记住`jdk.nashorn.internal`中的类可能会有所变化，所以不应该在客户端面向这些类来编程。

> *任何标记为“内部”的东西都可能会从你那里发生改变。*

### ScriptObjectMirror

在向Java传递原生JavaScript对象时，你可以使用`ScriptObjectMirror`类，它实际上是底层JavaScript对象的Java表示。`ScriptObjectMirror`实现了`Map`接口，位于`jdk.nashorn.api`中。这个包中的类可以用于客户端代码。

下面的例子将参数类型从`Object`改为`ScriptObjectMirror`，所以我们可以从传入的JavaScript对象中获得一些信息。

```java
static void fun3(ScriptObjectMirror mirror) {
    System.out.println(mirror.getClassName() + ": " +
        Arrays.toString(mirror.getOwnKeys(true)));
}
```

当向这个方法传递对象（哈希表）时，在Java端可以访问其属性：

```java
MyJavaClass.fun3({
    foo: 'bar',
    bar: 'foo'
});
// Object: [foo, bar]
```

我们也可以在Java中调用JavaScript的成员函数。让我们首先定义JavaScript `Person`类型，带有属性`firstName` 和 `lastName`，以及方法`getFullName`。

```js
function Person(firstName, lastName) {
    this.firstName = firstName;
    this.lastName = lastName;
    this.getFullName = function() {
        return this.firstName + " " + this.lastName;
    }
}
```

JavaScript方法`getFullName`可以通过`callMember()`在`ScriptObjectMirror`上调用。

```java
static void fun4(ScriptObjectMirror person) {
    System.out.println("Full Name is: " + person.callMember("getFullName"));
}
```

当向Java方法传递新的`Person`时，我们会在控制台看到预期的结果：

```js
var person1 = new Person("Peter", "Parker");
MyJavaClass.fun4(person1);
// Full Name is: Peter Parker
```

### 语言扩展

Nashorn定义了多种对ECMAScript标准的语言和API扩展。让我们看一看最新的特性：

#### 类型数组

JavaScript的原生数组是无类型的。Nashron允许你在JavaScript中使用Java的类型数组：

```js
var IntArray = Java.type("int[]");
var array = new IntArray(5);
array[0] = 5;
array[1] = 4;
array[2] = 3;
array[3] = 2;
array[4] = 1;
try {
    array[5] = 23;
} catch (e) {
    print(e.message);  // Array index out of range: 5
}
array[0] = "17";
print(array[0]);  // 17
array[0] = "wrong type";
print(array[0]);  // 0
array[0] = "17.3";
print(array[0]);  // 17
```

`int[]`数组就像真实的Java整数数组那样。但是此外，在我们试图向数组添加非整数时，Nashron在背后执行了一些隐式的转换。字符串会自动转换为整数，这十分便利。

#### 集合和范围遍历

我们可以使用任何Java集合，而避免使用数组瞎折腾。首先需要通过`Java.type`定义Java类型，之后创建新的实例。

```js
var ArrayList = Java.type('java.util.ArrayList');
var list = new ArrayList();
list.add('a');
list.add('b');
list.add('c');
for each (var el in list) print(el);  // a, b, c
```

为了迭代集合和数组，Nashron引入了`for each`语句。它就像Java的范围遍历那样工作。

下面是另一个集合的范围遍历示例，使用`HashMap`：

```js
var map = new java.util.HashMap();
map.put('foo', 'val1');
map.put('bar', 'val2');
for each (var e in map.keySet()) print(e);  // foo, bar
for each (var e in map.values()) print(e);  // val1, val2
```

#### Lambda表达式和数据流

每个人都热爱lambda和数据流 — Nashron也一样！虽然ECMAScript 5.1没有Java8 lmbda表达式的简化箭头语法，我们可以在任何接受lambda表达式的地方使用函数字面值。

```js
var list2 = new java.util.ArrayList();
list2.add("ddd2");
list2.add("aaa2");
list2.add("bbb1");
list2.add("aaa1");
list2.add("bbb3");
list2.add("ccc");
list2.add("bbb2");
list2.add("ddd1");
list2
    .stream()
    .filter(function(el) {
        return el.startsWith("aaa");
    })
    .sorted()
    .forEach(function(el) {
        print(el);
    });
    // aaa1, aaa2
```

#### 类的继承

Java类型可以由`Java.extend`轻易扩展。就像你在下面的例子中看到的那样，你甚至可以在你的脚本中创建多线程的代码：

```js
var Runnable = Java.type('java.lang.Runnable');
var Printer = Java.extend(Runnable, {
    run: function() {
        print('printed from a separate thread');
    }
});
var Thread = Java.type('java.lang.Thread');
new Thread(new Printer()).start();
new Thread(function() {
    print('printed from another thread');
}).start();
// printed from a separate thread
// printed from another thread
```

#### 参数重载

方法和函数可以通过点运算符或方括号运算符来调用：

```js
var System = Java.type('java.lang.System');
System.out.println(10);              // 10
System.out["println"](11.0);         // 11.0
System.out["println(double)"](12);   // 12.0
```

当使用重载参数调用方法时，传递可选参数类型`println(double)`会指定所调用的具体方法。

#### Java Beans

你可以简单地使用属性名称来向Java Beans获取或设置值，不需要显式调用读写器：

```js
var Date = Java.type('java.util.Date');
var date = new Date();
date.year += 1900;
print(date.year);  // 2014
```

#### 函数字面值

对于简单的单行函数，我们可以去掉花括号：

```js
function sqr(x) x * x;
print(sqr(3));    // 9
```

#### 属性绑定

两个不同对象的属性可以绑定到一起：

```js
var o1 = {};
var o2 = { foo: 'bar'};
Object.bindProperties(o1, o2);
print(o1.foo);    // bar
o1.foo = 'BAM';
print(o2.foo);    // BAM
```

#### 字符串去空白

我喜欢去掉空白的字符串：

```js
print("   hehe".trimLeft());            // hehe
print("hehe    ".trimRight() + "he");   // hehehe
```

#### 位置

以防你忘了自己在哪里：

```js
print(__FILE__, __LINE__, __DIR__);
```

#### 导入作用域

有时一次导入多个Java包会很方便。我们可以使用`JavaImporter`类，和`with`语句一起使用。所有被导入包的类文件都可以在`with`语句的局部域中访问到。

```js
var imports = new JavaImporter(java.io, java.lang);
with (imports) {
    var file = new File(__FILE__);
    System.out.println(file.getAbsolutePath());
    // /path/to/my/script.js
}
```

#### 数组转换

一些类似`java.util`的包可以不使用`java.type`或`JavaImporter`直接访问：

```js
var list = new java.util.ArrayList();
list.add("s1");
list.add("s2");
list.add("s3");
```

下面的代码将Java列表转换为JavaScript原生数组：

```js
var jsArray = Java.from(list);
print(jsArray);                                  // s1,s2,s3
print(Object.prototype.toString.call(jsArray));  // [object Array]
```

下面的代码执行相反操作：

```js
var javaArray = Java.to([3, 5, 7, 11], "int[]");
```

#### 访问超类

在JavaScript中访问被覆盖的成员通常比较困难，因为Java的`super`关键字在ECMAScript中并不存在。幸运的是，Nashron有一套补救措施。

首先我们需要在Java代码中定义超类：

```java
class SuperRunner implements Runnable {
    @Override
    public void run() {
        System.out.println("super run");
    }
}
```

下面我在JavaScript中覆盖了`SuperRunner`。要注意创建新的`Runner`实例时的Nashron语法：覆盖成员的语法取自Java的匿名对象。

```js
var SuperRunner = Java.type('com.winterbe.java8.SuperRunner');
var Runner = Java.extend(SuperRunner);
var runner = new Runner() {
    run: function() {
        Java.super(runner).run();
        print('on my run');
    }
}
runner.run();
// super run
// on my run
```

我们通过`Java.super()`扩展调用了被覆盖的`SuperRunner.run()`方法。

### 加载脚本

在JavaScript中加载额外的脚本文件非常方便。我们可以使用`load`函数加载本地或远程脚本。

我在我的Web前端中大量使用[Underscore.js](http://link.zhihu.com/?target=http%3A//underscorejs.org/)，所以让我们在Nashron中复用它：

```js
load('http://cdnjs.cloudflare.com/ajax/libs/underscore.js/1.6.0/underscore-min.js');
var odds = _.filter([1, 2, 3, 4, 5, 6], function (num) {
    return num % 2 == 1;
});
print(odds);  // 1, 3, 5
```

外部脚本会在相同JavaScript上下文中被执行，所以我们可以直接访问underscore 的对象。要记住当变量名称互相冲突时，脚本的加载可能会使你的代码崩溃。

这一问题可以通过把脚本文件加载到新的全局上下文来绕过：

```js
loadWithNewGlobal('script.js');
```

### 命令行脚本

如果你对编写命令行（shell）脚本感兴趣，来试一试[Nake](http://link.zhihu.com/?target=https%3A//github.com/winterbe/nake)吧。Nake是一个Java 8 Nashron的简化构建工具。你只需要在项目特定的`Nakefile`中定义任务，之后通过在命令行键入`nake -- myTask`来执行这些任务。任务编写为JavaScript，并且在Nashron的脚本模式下运行，所以你可以使用你的终端、JDK8 API和任意Java库的全部功能。

对Java开发者来说，编写命令行脚本是前所未有的简单…



## Misc

### Optional

| 方法          | 描述                                                         |
| ------------- | ------------------------------------------------------------ |
| `empty`       | 返回一个空的 Optional 实例                                   |
| `filter`      | 如果值存在并且满足提供的断言， 就返回包含该值的 Optional 对象；否则返回一个空的 Optional 对象 |
| `map`         | 如果值存在，就对该值执行提供的 mapping 函数调用              |
| `flatMap`     | 如果值存在，就对该值执行提供的 mapping 函数调用，返回一个 Optional 类型的值，否则就返 回一个空的 Optional 对象 |
| `get`         | 如果该值存在，将该值用 Optional 封装返回，否则抛出一个 NoSuchElementException 异常 |
| `ifPresent`   | 如果值存在，就执行使用该值的方法调用，否则什么也不做         |
| `isPresent`   | 如果值存在就返回 true，否则返回 false                        |
| `of`          | 将指定值用 Optional 封装之后返回，如果该值为 null，则抛出一个 NullPointerException 异常 |
| `ofNullable`  | 将指定值用 Optional 封装之后返回，如果该值为 null，则返回一个空的 Optional 对象 |
| `orElse`      | 如果有值则将其返回，否则返回一个默认值                       |
| `orElseGet`   | 如果有值则将其返回，否则返回一个由指定的 Supplier 接口生成的值 |
| `orElseThrow` | 如果有值则将其返回，否则抛出一个由指定的 Supplier 接口生成的异常 |

Optional 可以很优雅地避免了 `NullPointerException` ，这里我们简单举个例子，

```java
ptional<String> optional = Optional.of("bam");

optional.isPresent();// true
optional.get();                 // "bam"
optional.orElse("fallback");    // "bam"

optional.ifPresent((s) -> System.out.println(s.charAt(0)));     // "b"
```

### Base64

```java
// Encoding a string to base 64
Base64.Encoder encoder = Base64.getEncoder();
String normalString = "username:password";
String encodedString = encoder.encodeToString(
        normalString.getBytes(StandardCharsets.UTF_8) );
 
// Decoding a base 64 encoded string
String encodedString = "dXNlcm5hbWU6cGFzc3dvcmQ=";
Base64.Decoder decoder = Base64.getDecoder();
byte[] decodedByteArray = decoder.decode(encodedString);
//Verify the decoded string
System.out.println(new String(decodedByteArray));

// Wrap to a base 64 encoded output stream
Path originalPath = Paths.get("c:/temp", "mail.txt");
Path targetPath = Paths.get("c:/temp", "encoded.txt");
Base64.Encoder mimeEncoder = Base64.getMimeEncoder();
try(OutputStream output = Files.newOutputStream(targetPath)){
    //Copy the encoded file content to target file
    Files.copy(originalPath, mimeEncoder.wrap(output));
    //Or simply use the encoded output stream
    OutputStream encodedStrem = mimeEncoder.wrap(output);
}
   
```

### Annotation

[原文链接](https://www.jianshu.com/p/1a46570920da)

Java 8在两个方面对注解机制进行了改进，分别为:

你现在可以定义重复注解

你可以为任何目标添加注解

Java中的注解是一种对程序元素进行配置，提供附加信息的机制。

#### 重复注解

之前版本的Java禁止对同样的注解类型声明多次。由于这个原因，下面的第二句代码是无效的。

```java
@interface Author {
 String name();
}

@Author(name="Raoul") 
@Author(name="Mario") @Author(name="Alan")
 class Book{ }
```

Java程序员经常通过一些惯用法绕过这一限制。例如可以声明一个新的注解，它包含了你希望重复的注解数组。这种方法的形式如下:

```java
@interface Author {
    String name(); 
}

@interface Authors {
    Author[] value();
}

@Authors(
{
@Author(name="Raoul"), 
@Author(name="Mario") , 
@Author(name="Alan")
}
)
class Book{}
```

Book类的嵌套注解相当难看。这就是Java 8想要从根本上移除这一限制的原因，去掉这一限制后，代码的可读性会好很多。

现在，如果你的配置允许重复注解，你可以毫无顾虑地一次声明 多个同一种类型的注解。它目前还不是默认行为，你需要显式地要求进行重复注解。

创建一个重复注解

如果一个注解在设计之初就是可重复的，你可以直接使用它。但是，如果你提供的注解是为用户提供的，那么就需要做一些工作，说明该注解可以重复。下面是你需要执行的两个步骤:

```java
@Repeatable(Authors.class)
@interface Author { 
    String name();
}

@interface Authors {
    Author[] value();
}
```

完成了这样的定义之后，Book类可以通过多个@Author注解进行注释，如下所示:

```java
@Author(name="Raoul") 
@Author(name="Mario") 
@Author(name="Alan") class Book{ }
```

编译时，Book会被认为使用了

```java
@Authors({@Author(name="Raoul"), 
@Author(name =”Mario”), 
@Author(name=”Alan”)})
```

这样的形式进行了注解，所以，你可以把这种新的机 制看成是一种语法糖，它提供了Java程序员之前利用的惯用法类似的功能。为了确保与反射方法 在行为上的一致性，注解会被封装到一个容器中。Java API中的getAnnotation(Class<T> annotation-Class)方法会为注解元素返回类型为T的注解。如果实际情况有多个类型为T的注解，该方法的返回到底是哪一个呢?

类Class提供了一个新的getAnnotationsByType 方法，它可以帮助我们更好地使用重复注解。比如，你可以像下面这样打印输出Book类的所有Author注解:

```java
public static void main(String[] args) {
Author[] authors = Book.class.getAnnotationsByType(Author.class); 
//java8提供的循环及lambda表达式
Arrays.asList(authors).forEach(a ->
 { 
    System.out.println(a.name());
 }
 );
}
```

#### 类型注解

从Java 8开始，注解已经能应用于任何目标。这其中包括new操作符、类型转换、instanceof检查、泛型类型参数，以及implements和throws子句。

这里，我们举了一个例子，这个例子中 类型为String的变量name不能为空，所以我们使用了@NonNull对其进行注解:

```java
@NonNull String name = person.getName();
```

类似地，你可以对列表中的元素类型进行注解:

```java
List<@NonNull Car> cars = new ArrayList<>(); 
```

利用好对类型的注解非常有利于我们对程序进行分析。这两个例子中，通过这一工具我们可以确保getName不返回空，cars列表中的元素总是非空值。这会 极大地帮助你减少代码中不期而至的错误。

Java 8并未提供官方的注解或者一种工具能以开箱即用的方式使用它们。它仅仅提供了一种功能，你使用它可以对不同的类型添加注解。

#### 泛型类型推断

Java 8对泛型参数的推断进行了增强。相信你对Java 8之前版本中的类型推断已经比较熟了。

比如，Java中的方法emptyList方法定义如下:

```java
static <T> List<T> emptyList();
```

emptyList方法使用了类型参数T进行参数化。你可以像下面这样为该类型参数提供一个显式的类型进行函数调用:

```java
List<Car> cars = Collections.<Car>emptyList();
```

不过Java也可以推断泛型参数的类型。上面的代码和下面这段代码是等价的:

```java
List<Car> cars = Collections.emptyList();
```

Java 8出现之前，这种推断机制依赖于程序的上下文(即目标类型)，具有一定的局限性。

Java 8中，目标类型包括向方法传递的参数，因此你不再需要提供显式的泛型参数

### Regex

#### Convert Regex to Predicate

```java
long count = Stream.of("bob@gmail.com", "alice@hotmail.com")
    .filter(Pattern.compile(".*@gmail\\.com").asPredicate())
    .count();
System.out.println(count);
```

#### Using Regex using Pattern.matcher()

```java
Pattern pattern = Pattern.compile("^(.+)@example.com$");

        // Input list
        List<String> emails = Arrays.asList("alex@example.com", "bob@yahoo.com",
                "cat@google.com", "david@example.com");

        for(String email : emails)
        {
            Matcher matcher = pattern.matcher(email);

            if(matcher.matches())
            {
                System.out.println(email);
            }
        }
```

### File

#### Read

#####  Read file line by line – Java 8 Stream

```java
 Path path = Paths.get("c:/temp", "data.txt");
 
//The stream hence file will also be closed here
try(Stream<String> lines = Files.lines(path))
{
    Optional<String> hasPassword = lines.filter(s -> s.contains("password")).findFirst();

    if(hasPassword.isPresent()){
        System.out.println(hasPassword.get());
    }
}

// or
 Path path = Paths.get("c:/temp", "data.txt");
 
// When filteredLines is closed, it closes underlying stream as well as underlying file.
try(Stream<String> filteredLines = Files.lines(path).filter(s -> s.contains("password")))
{
    Optional<String> hasPassword = filteredLines.findFirst();

    if(hasPassword.isPresent()){
        System.out.println(hasPassword.get());
    }
}
```

##### Read file line by line – FileReader

Till java 7, you could read a file using FileReader in various ways. There may be other effective and better variations of this code but that is not the man point of this post. I am giving just as reminder.

```java
File file = new File("c:/temp/data.txt");
 
FileReader fr = new FileReader(file);
BufferedReader br = new BufferedReader(fr);

String line;
while((line = br.readLine()) != null)
{
    if(line.contains("password")){
        System.out.println(line);
    }
}
br.close();
fr.close();
```

#### Write

##### Java 8 write to file using BufferedWriter

[BufferedWriter](https://docs.oracle.com/javase/8/docs/api/java/io/BufferedWriter.html) is used to write text to a character or byte stream. Before printing the characters, it stores the characters in buffer and print in bunches. Without buffering, each invocation of a print() method would cause characters to be converted into bytes that would then be written immediately to the file, which can be very inefficient.

Java program to *write content to file using Java 8* APIs is –

```java
//Get the file reference
Path path = Paths.get("c:/output.txt");
 
//Use try-with-resource to get auto-closeable writer instance
try (BufferedWriter writer = Files.newBufferedWriter(path))
{
    writer.write("Hello World !!");
}
```

##### Write to file using Files.write()

Using [Files.write()](https://docs.oracle.com/javase/8/docs/api/java/nio/file/Files.html#write-java.nio.file.Path-byte:A-java.nio.file.OpenOption...-) method is also pretty much clean code.

```java
String content = "Hello World !!";
 
Files.write(Paths.get("c:/output.txt"), content.getBytes());
```



## 相关链接

[java8-tutorial](<https://github.com/winterbe/java8-tutorial>)

[howtodoinjava java8](<https://howtodoinjava.com/java8/>) 

[JavaLambdaInternals](<https://github.com/CarpenterLee/JavaLambdaInternals>)

[collection-pipeline](<https://martinfowler.com/articles/collection-pipeline/>)

[learn-java8](<https://github.com/biezhi/learn-java8>)

[java8官网笔记教程](<https://zq99299.github.io/java-tutorial/>)

[示例代码](<https://github.com/jianchengwang/todo-java/tree/master/java8>)