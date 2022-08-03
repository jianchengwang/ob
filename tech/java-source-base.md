---
title: java-source-base
createdAt: 2021-02-08
categories: 
- java
- javase
tags: 
- java source
---

java 基础源码剖析，平时一般就遇到问题才会debug看源码调试，所以源码基础比较弱，只知其然不知所以然，这里简单做个记录，部分文字来自互联网，最下面会列出相关链接，持续更新中。。

**Tips: 这里jdk的版本是1.8**

<!--more-->

## Lang

### Integer

包装类，

```java
    private final int value;
```

##### 构造方法

```java
//构造一个新分配的 Integer 对象，它表示指定的 int 值。
public Integer(int value) {
    this.value = value;
}

//构造一个新分配的 Integer 对象，它表示 String 参数所指示的 int 值。
public Integer(String s) throws NumberFormatException {
    this.value = parseInt(s, 10);
}
```

##### valueOf(int i)方法

前面说到Integer中私有属性value的时候提到

```java
Integer i = new Integer(10);
i = 5;
```

其中`i=5`操作时，编译器会转成`i = Integer.valueOf(5);`执行。那么这里就解释一下valueOf(int i)方法是如何给变量赋值的。

```java
public static Integer valueOf(int i) {
    if (i >= IntegerCache.low && i <= IntegerCache.high)
        return IntegerCache.cache[i + (-IntegerCache.low)];
    return new Integer(i);
}
```

以上是valueOf方法的实现细节。通常情况下，IntegerCache.low=-128，IntegerCache.high=127（除非显示声明java.lang.Integer.IntegerCache.high的值），Integer中有一段动态代码块，该部分内容会在Integer类被加载的时候就执行。

```java
static {
        // high value may be configured by property
        int h = 127;
        String integerCacheHighPropValue =
            sun.misc.VM.getSavedProperty("java.lang.Integer.IntegerCache.high");
        if (integerCacheHighPropValue != null) {
            try {
                int i = parseInt(integerCacheHighPropValue);
                i = Math.max(i, 127);
                // Maximum array size is Integer.MAX_VALUE
                h = Math.min(i, Integer.MAX_VALUE - (-low) -1);
            } catch( NumberFormatException nfe) {
                // If the property cannot be parsed into an int, ignore it.
            }
        }
        high = h;

        cache = new Integer[(high - low) + 1];
        int j = low;
        for(int k = 0; k < cache.length; k++)
            cache[k] = new Integer(j++);

        // range [-128, 127] must be interned (JLS7 5.1.7)
        assert IntegerCache.high >= 127;
    }

```

也就是说，当Integer被加载时，就新建了-128到127的所有数字并存放在Integer数组cache中。

再回到valueOf代码，可以得出结论。**当调用valueOf方法（包括后面会提到的重载的参数类型包含String的valueOf方法）时，如果参数的值在-127到128之间，则直接从缓存中返回一个已经存在的对象。如果参数的值不在这个范围内，则new一个Integer对象返回。**

所以，当把一个int变量转成Integer的时候（或者新建一个Integer的时候），建议使用valueOf方法来代替构造函数。或者直接使用`Integer i = 100;`编译器会转成`Integer s = Integer.valueOf(100);`

### String

底层`final char[]`实现，jdk9之后使用`byte[]`提升性能，这也说明了string为何是不可变的，

```java
/** The value is used for character storage. */
private final char value[];

/** Cache the hash code for the string */
private int hash; // Default to 0
```

#### 构造方法

##### 参数为string类型

```java

    /**
     * Initializes a newly created {@code String} object so that it represents
     * the same sequence of characters as the argument; in other words, the
     * newly created string is a copy of the argument string. Unless an
     * explicit copy of {@code original} is needed, use of this constructor is
     * unnecessary since Strings are immutable.
     *
     * @param  original
     *         A {@code String}
     */
    public String(String original) {
        this.value = original.value;
        this.hash = original.hash;
    }
```

这里将直接将源 String 中的 value 和 hash 两个属性直接赋值给目标 String。因为 String 一旦定义之后是不可以改变的，所以也就不用担心改变源 String 的值会影响到目标 String 的值。

##### 参数为字符数组

```java

    /**
     * Allocates a new {@code String} so that it represents the sequence of
     * characters currently contained in the character array argument. The
     * contents of the character array are copied; subsequent modification of
     * the character array does not affect the newly created string.
     *
     * @param  value
     *         The initial value of the string
     */
    public String(char value[]) {
        this.value = Arrays.copyOf(value, value.length);
    }
```

当我们使用字符数组创建 String 的时候，会用到 Arrays.copyOf 方法或 Arrays.copyOfRange 方法。这两个方法是将原有的字符数组中的内容逐一的复制到 String 中的字符数组中。会创建一个新的字符串对象，随后修改的字符数组不影响新创建的字符串

##### 参数为字节数组

```java

    /**
     * Constructs a new {@code String} by decoding the specified array of
     * bytes using the specified {@linkplain java.nio.charset.Charset charset}.
     * The length of the new {@code String} is a function of the charset, and
     * hence may not be equal to the length of the byte array.
     *
     * <p> This method always replaces malformed-input and unmappable-character
     * sequences with this charset's default replacement string.  The {@link
     * java.nio.charset.CharsetDecoder} class should be used when more control
     * over the decoding process is required.
     *
     * @param  bytes
     *         The bytes to be decoded into characters
     *
     * @param  charset
     *         The {@linkplain java.nio.charset.Charset charset} to be used to
     *         decode the {@code bytes}
     *
     * @since  1.6
     */
    public String(byte bytes[], Charset charset) {
        this(bytes, 0, bytes.length, charset);
    }
```

char[] 字符数组是以 unicode 码来存储的，String 和 char 为内存形式。

byte 是网络传输或存储的序列化形式，所以在很多传输和存储的过程中需要将 byte[] 数组和 String 进行相互转化。

所以 String 提供了一系列重载的构造方法来将一个字符数组转化成 String，提到 byte[] 和 String 之间的相互转换就不得不关注编码问题，

我们在使用 byte[] 构造 String 的时候，如果没有指明解码使用的字符集的话，那么 StringCoding 的 decode 方法首先调用系统的默认编码格式，如果没有指定编码格式则默认使用 ISO-8859-1 编码格式进行编码操作

```java
        String csn = (charsetName == null) ? "ISO-8859-1" : charsetName;
```

##### 参数为StringBuffer，StringBuilder

```java

    /**
     * Allocates a new string that contains the sequence of characters
     * currently contained in the string buffer argument. The contents of the
     * string buffer are copied; subsequent modification of the string buffer
     * does not affect the newly created string.
     *
     * @param  buffer
     *         A {@code StringBuffer}
     */
    public String(StringBuffer buffer) {
        synchronized(buffer) {
            this.value = Arrays.copyOf(buffer.getValue(), buffer.length());
        }
    }

```

```java

    /**
     * Allocates a new string that contains the sequence of characters
     * currently contained in the string builder argument. The contents of the
     * string builder are copied; subsequent modification of the string builder
     * does not affect the newly created string.
     *
     * <p> This constructor is provided to ease migration to {@code
     * StringBuilder}. Obtaining a string from a string builder via the {@code
     * toString} method is likely to run faster and is generally preferred.
     *
     * @param   builder
     *          A {@code StringBuilder}
     *
     * @since  1.5
     */
    public String(StringBuilder builder) {
        this.value = Arrays.copyOf(builder.getValue(), builder.length());
    }
```

这个比较少使用，主要使用`StringBuffer.toString`方法

##### 特殊的protected构造方法

```java
   /*
    * Package private constructor which shares value array for speed.
    * this constructor is always expected to be called with share==true.
    * a separate constructor is needed because we already have a public
    * String(char[]) constructor that makes a copy of the given char[].
    */
    String(char[] value, boolean share) {
        // assert share : "unshared not supported";
        this.value = value;
    }
```

第一个区别：该方法多了一个参数：boolean share，其实这个参数在方法体中根本没被使用。注释说目前不支持 false，只使用 true。那可以断定，加入这个 share 的只是为了区分于 String(char[] value) 方法，不加这个参数就没办法定义这个函数，只有参数是不同才能进行重载。

第二个区别：具体的方法实现不同。我们前面提到过 String(char[] value) 方法在创建 String 的时候会用到 Arrays 的 copyOf 方法将 value 中的内容逐一复制到 String 当中，而这个 String(char[] value, boolean share) 方法则是直接将 value 的引用赋值给 String 的 value。那么也就是说，这个方法构造出来的 String 和参数传过来的 char[] value 共享同一个数组。

这个特殊的方法因为共享数组，所以性能比较好，

不过可能产生内存泄露，所以现在用的地方也不是很多，以我现在的版本，jdk1.8来看，下面有使用这个构造方法，

```java
Integer

​	toString

Long

​	toString

String

​	concat

​	replace

​	valueOf

StringBuffer

​	toString
```

#### 其他方法

##### getBytes

```java

    /**
     * Encodes this {@code String} into a sequence of bytes using the given
     * {@linkplain java.nio.charset.Charset charset}, storing the result into a
     * new byte array.
     *
     * <p> This method always replaces malformed-input and unmappable-character
     * sequences with this charset's default replacement byte array.  The
     * {@link java.nio.charset.CharsetEncoder} class should be used when more
     * control over the encoding process is required.
     *
     * @param  charset
     *         The {@linkplain java.nio.charset.Charset} to be used to encode
     *         the {@code String}
     *
     * @return  The resultant byte array
     *
     * @since  1.6
     */
    public byte[] getBytes(Charset charset) {
        if (charset == null) throw new NullPointerException();
        return StringCoding.encode(charset, value, 0, value.length);
    }
```

这个最好指定编码，不然不同系统的操作结果会不一致

##### equals

```java

    /**
     * Compares this string to the specified object.  The result is {@code
     * true} if and only if the argument is not {@code null} and is a {@code
     * String} object that represents the same sequence of characters as this
     * object.
     *
     * @param  anObject
     *         The object to compare this {@code String} against
     *
     * @return  {@code true} if the given object represents a {@code String}
     *          equivalent to this string, {@code false} otherwise
     *
     * @see  #compareTo(String)
     * @see  #equalsIgnoreCase(String)
     */
    public boolean equals(Object anObject) {
        if (this == anObject) {
            return true;
        }
        if (anObject instanceof String) {
            String anotherString = (String)anObject;
            int n = value.length;
            if (n == anotherString.value.length) {
                char v1[] = value;
                char v2[] = anotherString.value;
                int i = 0;
                while (n-- != 0) {
                    if (v1[i] != v2[i])
                        return false;
                    i++;
                }
                return true;
            }
        }
        return false;
    }
```

通过源码的代码，我们可以了解它比较的流程：字符串相同：地址相同；地址不同，但是内容相同 这是一种提高效率的方法，也就是将比较快速的部分（地址，比较对象类型）放在前面比较，速度慢的部分（比较字符数组）放在后面执行。

##### contentEquals

```java

    /**
     * Compares this string to the specified {@code CharSequence}.  The
     * result is {@code true} if and only if this {@code String} represents the
     * same sequence of char values as the specified sequence. Note that if the
     * {@code CharSequence} is a {@code StringBuffer} then the method
     * synchronizes on it.
     *
     * @param  cs
     *         The sequence to compare this {@code String} against
     *
     * @return  {@code true} if this {@code String} represents the same
     *          sequence of char values as the specified sequence, {@code
     *          false} otherwise
     *
     * @since  1.5
     */
    public boolean contentEquals(CharSequence cs) {
        // Argument is a StringBuffer, StringBuilder
        if (cs instanceof AbstractStringBuilder) {
            if (cs instanceof StringBuffer) {
                synchronized(cs) {
                   return nonSyncContentEquals((AbstractStringBuilder)cs);
                }
            } else {
                return nonSyncContentEquals((AbstractStringBuilder)cs);
            }
        }
        // Argument is a String
        if (cs instanceof String) {
            return equals(cs);
        }
        // Argument is a generic CharSequence
        char v1[] = value;
        int n = v1.length;
        if (n != cs.length()) {
            return false;
        }
        for (int i = 0; i < n; i++) {
            if (v1[i] != cs.charAt(i)) {
                return false;
            }
        }
        return true;
    }
```

**StringBuffer 需要考虑线程安全问题，加锁之后再调用**

##### compareTo

```java
public int compareTo(String anotherString) {
        int len1 = value.length;
        int len2 = anotherString.value.length;
        // 获取到两个字符串长度最短的那个字符长度
        int lim = Math.min(len1, len2);
        char v1[] = value;
        char v2[] = anotherString.value;

        int k = 0;
        // 对比每一个字符
        while (k < lim) {
            char c1 = v1[k];
            char c2 = v2[k];
            // 有字符不相等就返回差值
            if (c1 != c2) {
                return c1 - c2;
            }
            k++;
        }
        return len1 - len2;
    }
```

还有一个和compareTo() 比较类似的方法compareToIgnoreCase()，用于忽略大小写后比较两个字符串

可以看出compareTo() 方法和equals() 方法都是用于比较两个字符串，但是他们有两点不同

（1）equals() 可以接收一个 Object 类型的参数，而 compareTo() 只能接收一个 String 类型的参数

（2）equals() 返回值为 Boolean，而 compareTo() 的返回值则为 int

它们都可以用于两个字符串的比较，当 equals() 方法返回 true 时，或者是 compareTo() 方法返回 0 时，则表示两个字符串完全相同

##### hashCode

```java

    /**
     * Returns a hash code for this string. The hash code for a
     * {@code String} object is computed as
     * <blockquote><pre>
     * s[0]*31^(n-1) + s[1]*31^(n-2) + ... + s[n-1]
     * </pre></blockquote>
     * using {@code int} arithmetic, where {@code s[i]} is the
     * <i>i</i>th character of the string, {@code n} is the length of
     * the string, and {@code ^} indicates exponentiation.
     * (The hash value of the empty string is zero.)
     *
     * @return  a hash code value for this object.
     */
    public int hashCode() {
        int h = hash;
        if (h == 0 && value.length > 0) {
            char val[] = value;

            for (int i = 0; i < value.length; i++) {
                h = 31 * h + val[i];
            }
            hash = h;
        }
        return h;
    }
```

hashCode 的实现其实就是使用数学公式:

` s[0]*31^(n-1) + s[1]*31^(n-2) + ... + s[n-1]`

> 为什么要使用这个公式，就是在存储数据计算 hash 地址的时候，我们希望尽量减少有同样的 hash 地址。如果使用相同 hash 地址的数据过多，那么这些数据所组成的 hash 链就更长，从而降低了查询效率。 所以在选择系数的时候要选择尽量长的系数并且让乘法尽量不要溢出的系数，因为如果计算出来的 hash 地址越大，所谓的“冲突”就越少，查找起来效率也会提高。

选择31作为因子的原因: [why-does-javas-hashcode-in-string-use-31-as-a-multiplier](https://stackoverflow.com/questions/299304/why-does-javas-hashcode-in-string-use-31-as-a-multiplier)

排名第一答案引用 Joshua Bloch's [Effective Java](https://rads.stackoverflow.com/amzn/click/com/0321356683) 的原话

> The value 31 was chosen because it is an odd prime. If it were even and the multiplication overflowed, information would be lost, as multiplication by 2 is equivalent to shifting. The advantage of using a prime is less clear, but it is traditional. A nice property of 31 is that the multiplication can be replaced by a shift and a subtraction for better performance: `31 * i == (i << 5) - i`. Modern VMs do this sort of optimization automatically.

简单翻译一下：

> 选择数字31是因为它是一个奇质数，如果选择一个偶数会在乘法运算中产生溢出，导致数值信息丢失，因为乘二相当于移位运算。选择质数的优势并不是特别的明显，但这是一个传统。同时，数字31有一个很好的特性，即乘法运算可以被移位和减法运算取代，来获取更好的性能：`31 * i == (i << 5) - i`，现代的 Java 虚拟机可以自动的完成这个优化。

排名第二的答案设这样说的：

> As Goodrich and Tamassia point out, If you take over 50,000 English words (formed as the union of the word lists provided in two variants of Unix), using the constants 31, 33, 37, 39, and 41 will produce less than 7 collisions in each case. Knowing this, it should come as no surprise that many Java implementations choose one of these constants.

这段话也翻译一下：

> 正如 Goodrich 和 Tamassia 指出的那样，如果你对超过 50,000 个英文单词（由两个不同版本的 Unix 字典合并而成）进行 hash code 运算，并使用常数 31, 33, 37, 39 和 41 作为乘子，每个常数算出的哈希值冲突数都小于7个，所以在上面几个常数中，常数 31 被 Java 实现所选用也就不足为奇了。

上面的两个答案完美的解释了 Java 源码中选用数字 31 的原因。

对这个感兴趣的也可以看看大佬的博客[科普：为什么 String hashCode 方法选择数字31作为乘子](https://segmentfault.com/a/1190000010799123)

#### intern

```java

    /**
     * Returns a canonical representation for the string object.
     * <p>
     * A pool of strings, initially empty, is maintained privately by the
     * class {@code String}.
     * <p>
     * When the intern method is invoked, if the pool already contains a
     * string equal to this {@code String} object as determined by
     * the {@link #equals(Object)} method, then the string from the pool is
     * returned. Otherwise, this {@code String} object is added to the
     * pool and a reference to this {@code String} object is returned.
     * <p>
     * It follows that for any two strings {@code s} and {@code t},
     * {@code s.intern() == t.intern()} is {@code true}
     * if and only if {@code s.equals(t)} is {@code true}.
     * <p>
     * All literal strings and string-valued constant expressions are
     * interned. String literals are defined in section 3.10.5 of the
     * <cite>The Java&trade; Language Specification</cite>.
     *
     * @return  a string that has the same contents as this string, but is
     *          guaranteed to be from a pool of unique strings.
     */
    public native String intern();
```

intern方法是Native调用，它的作用是在方法区中的常量池里寻找等值的对象，如果没有找到则在常量池中存放当前字符串的引用并返回该引用，否则直接返回常量池中已存在的String对象引用

##### 字符串字面量

字符串字面量是在 Java™语言规范的[3.10.5. String 字面量](https://docs.oracle.com/javase/specs/jls/se8/html/jls-3.html#jls-3.10.5)中定义的 关于字面量通俗点解释就是，使用双引号`""`创建的字符串，在堆中创建了对象后其引用插入到字符串常量池中（jdk1.7后），可以全局使用，遇到相同内容的字面量，就不需要再次创建。举个例子：

```
//这就是创建了一个aaa字符串字面量
String a = "aaa";
//简单来说，这就是创建了一个Stirng对象和一个aaa字符串字面量，后面会详细讨论
String a = new String("aaa")
```

##### 字符常量池 String Table

这个字符串常量池的位置也是随着jdk版本的不同而位置不同。在jdk6中，常量池的位置在永久代（方法区）中，此时常量池中存储的是**对象**。在jdk7中，常量池的位置在堆中，此时，常量池存储的就是**引用**了。在jdk8中，永久代（方法区）被元空间取代了。

##### 字面量是何时进入常量池

详情参照 [new String(“字面量”) 中 “字面量” 是何时进入字符串常量池的?](https://www.zhihu.com/question/55994121)

简单来说：

- HotSpot VM的实现来说，加载类的时候，那些字符串字面量会进入到当前类的运行时常量池，不会进入全局的字符串常量池 ;

- 在字面量赋值的时候，会翻译成字节码ldc指令，ldc指令触发**lazy resolution**动作

  > - 到当前类的运行时常量池（runtime constant pool，HotSpot VM里是ConstantPool + ConstantPoolCache）去查找该index对应的项
  > - 如果该项尚未resolve则resolve之，并返回resolve后的内容。
  > - 在遇到String类型常量时，resolve的过程如果发现StringTable已经有了内容匹配的java.lang.String的引用，则直接返回这个引用;
  > - 如果StringTable里尚未有内容匹配的String实例的引用，则会在Java堆里创建一个对应内容的String对象，然后在StringTable记录下这个引用，并返回这个引用出去。

##### String“+”符号的实现

在我们使用中经常会用到+符号来拼接字符串，但是这个+符号在String中的实现还是有讲究的。如果是相加含有String对象，则底部是使用StringBuilder实现的拼接的

```
String str1 ="str1";
String str2 ="str2";
String str3 = str1 + str2;
复制代码
```

如果相加的参数只有字面量或者常量或基础类型变量，则会直接编译为拼接后的字符串。

```
String str1 =1+"str2"+"str3"；
复制代码
```

**这里有个小细节**
 如果使用字面量拼接的话，java常量池里是不会保存拼接的参数的，而是直接编译成拼接后的字符串保存，我们看看这段代码：

```
        String str1 = new String("aa"+"bb");
        //String str3 = "aa";
        String str2 = new StringBuilder("a").append("a").toString();
        System.out.println(str2==str2.intern());
复制代码
```

这段代码的输出是`true`。可以得知，在str1变量的创建中，虽然我们用了字面量“aa”，但是我们常量池里并没有aa，所以`str2==str.intern()`才会返回`true`。如果我们去掉str3的注释，重新运行，就会输出`false`。

### Enum

##### 反编译

```java
public enum Color {
    Red,
    Blue
}

```

```java
/usr/bin/javap -p todo.core.source.java.lang.enums.Color
Compiled from "Color.java"
public final class todo.core.source.java.lang.enums.Color extends java.lang.Enum<todo.core.source.java.lang.enums.Color> {
  public static final todo.core.source.java.lang.enums.Color Red;
  public static final todo.core.source.java.lang.enums.Color Blue;
  private static final todo.core.source.java.lang.enums.Color[] $VALUES;
  public static todo.core.source.java.lang.enums.Color[] values();
  public static todo.core.source.java.lang.enums.Color valueOf(java.lang.String);
  private todo.core.source.java.lang.enums.Color();
  static {};
}
```

可以看到枚举类反编译的结果是一个 `final class Color extend java.lang.Enum`

声明的枚举值反编译结果是一个`static final Color Red `

可以看出，enmu关键字是java提供给我们的一个语法糖，

编译器不让我们继承Enum，但是当我们使用enum关键字定义一个枚举的时候，他会帮我们在编译后默认继承java.lang.Enum类，而不像其他的类一样默认继承Object类。且采用enum声明后，该类会被编译器加上final声明，故该类是无法继承的。 

PS：由于JVM类初始化是线程安全的，所以可以采用枚举类实现一个线程安全的单例模式。

##### 变量

```java

/**
 * This is the common base class of all Java language enumeration types.
 *
 * More information about enums, including descriptions of the
 * implicitly declared methods synthesized by the compiler, can be
 * found in section 8.9 of
 * <cite>The Java&trade; Language Specification</cite>.
 *
 * <p> Note that when using an enumeration type as the type of a set
 * or as the type of the keys in a map, specialized and efficient
 * {@linkplain java.util.EnumSet set} and {@linkplain
 * java.util.EnumMap map} implementations are available.
 *
 * @param <E> The enum type subclass
 * @author  Josh Bloch
 * @author  Neal Gafter
 * @see     Class#getEnumConstants()
 * @see     java.util.EnumSet
 * @see     java.util.EnumMap
 * @since   1.5
 */
public abstract class Enum<E extends Enum<E>>
        implements Comparable<E>, Serializable {
```

```java
    /**
     * The name of this enum constant, as declared in the enum declaration.
     * Most programmers should use the {@link #toString} method rather than
     * accessing this field.
     */
    private final String name;


    /**
     * The ordinal of this enumeration constant (its position
     * in the enum declaration, where the initial constant is assigned
     * an ordinal of zero).
     *
     * Most programmers will have no use for this field.  It is designed
     * for use by sophisticated enum-based data structures, such as
     * {@link java.util.EnumSet} and {@link java.util.EnumMap}.
     */
    private final int ordinal;
```

在Enum中，有两个成员变量，一个是名字(name)，一个是序号(ordinal)。

 序号是一个枚举常量，表示在枚举中的位置，从0开始，依次递增。

##### 构造方法

```java

    /**
     * Sole constructor.  Programmers cannot invoke this constructor.
     * It is for use by code emitted by the compiler in response to
     * enum type declarations.
     *
     * @param name - The name of this enum constant, which is the identifier
     *               used to declare it.
     * @param ordinal - The ordinal of this enumeration constant (its position
     *         in the enum declaration, where the initial constant is assigned
     *         an ordinal of zero).
     */
    protected Enum(String name, int ordinal) {
        this.name = name;
        this.ordinal = ordinal;
    }
```

前面我们说过，Enum是一个抽象类，不能被实例化，但是他也有构造函数，从前面我们反编译出来的代码中，我们也发现了Enum的构造函数，在Enum中只有一个保护类型的构造函数

##### 单例模式

枚举很适合作为单例模式，主要有如下原因，

###### 代码简单

```java
public enum EasySingleton{
    INSTANCE;
}
```

###### 线程安全

当一个Java类第一次被真正使用到的时候静态资源被初始化、Java类的加载和初始化过程都是线程安全的。所以，**创建一个enum类型是线程安全的**。

###### 自己处理序列化

我们知道，以前的所有的单例模式都有一个比较大的问题，就是一旦实现了Serializable接口之后，就不再是单例得了，因为，每次调用 readObject()方法返回的都是一个新创建出来的对象，有一种解决办法就是使用readResolve()方法来避免此事发生。但是，**为了保证枚举类型像Java规范中所说的那样，每一个枚举类型极其定义的枚举变量在JVM中都是唯一的，在枚举类型的序列化和反序列化上，Java做了特殊的规定。**原文如下：

> Enum constants are serialized differently than ordinary serializable or externalizable objects. The serialized form of an enum constant consists solely of its name; field values of the constant are not present in the form. To serialize an enum constant, ObjectOutputStream writes the value returned by the enum constant’s name method. To deserialize an enum constant, ObjectInputStream reads the constant name from the stream; the deserialized constant is then obtained by calling the java.lang.Enum.valueOf method, passing the constant’s enum type along with the received constant name as arguments. Like other serializable or externalizable objects, enum constants can function as the targets of back references appearing subsequently in the serialization stream. The process by which enum constants are serialized cannot be customized: any class-specific writeObject, readObject, readObjectNoData, writeReplace, and readResolve methods defined by enum types are ignored during serialization and deserialization. Similarly, any serialPersistentFields or serialVersionUID field declarations are also ignored–all enum types have a fixedserialVersionUID of 0L. Documenting serializable fields and data for enum types is unnecessary, since there is no variation in the type of data sent.

大概意思就是说，在序列化的时候Java仅仅是将枚举对象的name属性输出到结果中，反序列化的时候则是通过java.lang.Enum的valueOf方法来根据名字查找枚举对象。同时，编译器是不允许任何对这种序列化机制的定制的，因此禁用了writeObject、readObject、readObjectNoData、writeReplace和readResolve等方法。 我们看一下这个`valueOf`方法：

```java

    /**
     * Returns the enum constant of the specified enum type with the
     * specified name.  The name must match exactly an identifier used
     * to declare an enum constant in this type.  (Extraneous whitespace
     * characters are not permitted.)
     *
     * <p>Note that for a particular enum type {@code T}, the
     * implicitly declared {@code public static T valueOf(String)}
     * method on that enum may be used instead of this method to map
     * from a name to the corresponding enum constant.  All the
     * constants of an enum type can be obtained by calling the
     * implicit {@code public static T[] values()} method of that
     * type.
     *
     * @param <T> The enum type whose constant is to be returned
     * @param enumType the {@code Class} object of the enum type from which
     *      to return a constant
     * @param name the name of the constant to return
     * @return the enum constant of the specified enum type with the
     *      specified name
     * @throws IllegalArgumentException if the specified enum type has
     *         no constant with the specified name, or the specified
     *         class object does not represent an enum type
     * @throws NullPointerException if {@code enumType} or {@code name}
     *         is null
     * @since 1.5
     */
    public static <T extends Enum<T>> T valueOf(Class<T> enumType,
                                                String name) {
        T result = enumType.enumConstantDirectory().get(name);
        if (result != null)
            return result;
        if (name == null)
            throw new NullPointerException("Name is null");
        throw new IllegalArgumentException(
            "No enum constant " + enumType.getCanonicalName() + "." + name);
    }
```

从代码中可以看到，代码会尝试从调用`enumType`这个`Class`对象的`enumConstantDirectory()`方法返回的`map`中获取名字为`name`的枚举对象，如果不存在就会抛出异常。再进一步跟到`enumConstantDirectory()`方法，就会发现到最后会以反射的方式调用`enumType`这个类型的`values()`静态方法，也就是上面我们看到的编译器为我们创建的那个方法，然后用返回结果填充`enumType`这个`Class`对象中的`enumConstantDirectory`属性。

所以，**JVM对序列化有保证。**

综上，建议使用枚举实现单例模式，

## Set

### HashMap

#### 底层结构

在 JDK 1.7 中 HashMap 是以数组加链表的形式组成的，JDK 1.8 之后新增了红黑树的组成结构，当链表大于 8 并且哈希表长度大于 64 时，链表结构会转换成红黑树结构

下面看一下定义的常量或者变量

```java
// 数组默认的初始化长度16
static final int DEFAULT_INITIAL_CAPACITY = 1 << 4;

// 数组最大容量，2的30次幂，即1073741824
static final int MAXIMUM_CAPACITY = 1 << 30;

// 默认加载因子值
static final float DEFAULT_LOAD_FACTOR = 0.75f;

// 链表转换为红黑树的长度阈值
static final int TREEIFY_THRESHOLD = 8;

// 红黑树转换为链表的长度阈值
static final int UNTREEIFY_THRESHOLD = 6;

// 链表转换为红黑树时，数组容量必须大于等于64
static final int MIN_TREEIFY_CAPACITY = 64;

// HashMap里键值对个数
transient int size;

// 扩容阈值，计算方法为 数组容量*加载因子
int threshold;

// HashMap使用数组存放数据，数组元素类型为Node<K,V>
transient Node<K,V>[] table;

// 加载因子
final float loadFactor;

// 用于快速失败，由于HashMap非线程安全，在对HashMap进行迭代时，如果期间其他线程的参与导致HashMap的结构发生变化了（比如put，remove等操作），直接抛出ConcurrentModificationException异常
transient int modCount;
```

数组中的元素我们称之为哈希桶，它的定义如下：

```java

    /**
     * Basic hash bin node, used for most entries.  (See below for
     * TreeNode subclass, and in LinkedHashMap for its Entry subclass.)
     */
    static class Node<K,V> implements Map.Entry<K,V> {
        final int hash;
        final K key;
        V value;
        Node<K,V> next;

        Node(int hash, K key, V value, Node<K,V> next) {
            this.hash = hash;
            this.key = key;
            this.value = value;
            this.next = next;
        }

        public final K getKey()        { return key; }
        public final V getValue()      { return value; }
        public final String toString() { return key + "=" + value; }

        public final int hashCode() {
            return Objects.hashCode(key) ^ Objects.hashCode(value);
        }

        public final V setValue(V newValue) {
            V oldValue = value;
            value = newValue;
            return oldValue;
        }

        public final boolean equals(Object o) {
            if (o == this)
                return true;
            if (o instanceof Map.Entry) {
                Map.Entry<?,?> e = (Map.Entry<?,?>)o;
                if (Objects.equals(key, e.getKey()) &&
                    Objects.equals(value, e.getValue()))
                    return true;
            }
            return false;
        }
    }
```

可以看出每个哈希桶中包含了四个字段：hash、key、value、next，其中 next 表示链表的下一个节点

JDK 1.8 之所以添加红黑树是因为一旦链表过长，会严重影响 HashMap 的性能，而红黑树具有快速增删改查的特点，这样就可以有效的解决链表过长时操作比较慢的问题

红黑树的源码如下

```java
static final class TreeNode<K,V> extends LinkedHashMap.Entry<K,V> {
        TreeNode<K,V> parent;  // 父节点
        TreeNode<K,V> left; // 左子树
        TreeNode<K,V> right; // 右子树
        TreeNode<K,V> prev;    // needed to unlink next upon deletion
        boolean red; // 颜色属性
        TreeNode(int hash, K key, V val, Node<K,V> next) {
            super(hash, key, val, next);
        }

        /**
         * 返回当前节点的根节点
         */
        final TreeNode<K,V> root() {
            for (TreeNode<K,V> r = this, p;;) {
                if ((p = r.parent) == null)
                    return r;
                r = p;
            }
        }
```

#### put源码

```java
public V put(K key, V value) {
    return putVal(hash(key), key, value, false, true);
}
```

put方法通过hash函数计算key对应的哈希值，hash函数源码如下：

```java
static final int hash(Object key) {
    int h;
    return (key == null) ? 0 : (h = key.hashCode()) ^ (h >>> 16);
}
```

如果key为null，返回0，不为null，则通过`(h = key.hashCode()) ^ (h >>> 16)`公式计算得到哈希值。

该公式通过hashCode的高16位异或低16位得到哈希值，主要从性能、哈希碰撞角度考虑，减少系统开销，不会造成因为高位没有参与下标计算从而引起的碰撞。

得到key对应的哈希值后，再调用`putVal(hash(key), key, value, false, true)`方法插入元素：

```java
final V putVal(int hash, K key, V value, boolean onlyIfAbsent,
               boolean evict) {
    Node<K,V>[] tab; Node<K,V> p; int n, i;
    // 如果数组(哈希表)为null或者长度为0，则进行数组初始化操作
    if ((tab = table) == null || (n = tab.length) == 0)
        n = (tab = resize()).length;
    // 根据key的哈希值计算出数据插入数组的下标位置，公式为(n-1)&hash
    if ((p = tab[i = (n - 1) & hash]) == null)
    	// 如果该下标位置还没有元素，则直接创建Node对象，并插入
        tab[i] = newNode(hash, key, value, null);
    else {
        Node<K,V> e; K k;
        // 如果目标位置key已经存在，则直接覆盖
        if (p.hash == hash &&
            ((k = p.key) == key || (key != null && key.equals(k))))
            e = p;
        // 如果目标位置key不存在，并且节点为红黑树，则插入红黑树中
        else if (p instanceof TreeNode)
            e = ((TreeNode<K,V>)p).putTreeVal(this, tab, hash, key, value);
        else {
        	// 否则为链表结构，遍历链表，尾部插入
            for (int binCount = 0; ; ++binCount) {
                if ((e = p.next) == null) {
                    p.next = newNode(hash, key, value, null);
                    // 如果链表长度大于等于TREEIFY_THRESHOLD，则考虑转换为红黑树
                    if (binCount >= TREEIFY_THRESHOLD - 1) // -1 for 1st
                        treeifyBin(tab, hash); // 转换为红黑树操作，内部还会判断数组长度是否小于MIN_TREEIFY_CAPACITY，如果是的话不转换
                    break;
                }
                // 如果链表中已经存在该key的话，直接覆盖替换
                if (e.hash == hash &&
                    ((k = e.key) == key || (key != null && key.equals(k))))
                    break;
                p = e;
            }
        }
        if (e != null) { // existing mapping for key
        	// 返回被替换的值
            V oldValue = e.value;
            if (!onlyIfAbsent || oldValue == null)
                e.value = value;
            afterNodeAccess(e);
            return oldValue;
        }
    }
    // 模数递增
    ++modCount;
    // 当键值对个数大于等于扩容阈值的时候，进行扩容操作
    if (++size > threshold)
        resize();
    afterNodeInsertion(evict);
    return null;
}
```

put操作过程总结：

1. 判断HashMap数组是否为空，是的话初始化数组（由此可见，在创建HashMap对象的时候并不会直接初始化数组）；

2. 通过`(n-1) & hash`计算key在数组中的存放索引；

3. 目标索引位置为空的话，直接创建Node存储；

4. 目标索引位置不为空的话，分下面三种情况：

   4.1. key相同，覆盖旧值；

   4.2. 该节点类型是红黑树的话，执行红黑树插入操作；

   4.3. 该节点类型是链表的话，遍历到最后一个元素尾插入，如果期间有遇到key相同的，则直接覆盖。如果链表长度大于等于TREEIFY_THRESHOLD，并且数组容量大于等于MIN_TREEIFY_CAPACITY，则将链表转换为红黑树结构；

5. 判断HashMap元素个数是否大于等于threshold，是的话，进行扩容操作。

#### get源码

```java
public V get(Object key) {
    Node<K,V> e;
    // 对 key 进行哈希操作
    return (e = getNode(hash(key), key)) == null ? null : e.value;
}
final Node<K,V> getNode(int hash, Object key) {
    Node<K,V>[] tab; Node<K,V> first, e; int n; K k;
    // 非空判断
    if ((tab = table) != null && (n = tab.length) > 0 &&
        (first = tab[(n - 1) & hash]) != null) {
        // 判断第一个元素是否是要查询的元素
        if (first.hash == hash && // always check first node
            ((k = first.key) == key || (key != null && key.equals(k))))
            return first;
        // 下一个节点非空判断
        if ((e = first.next) != null) {
            // 如果第一节点是树结构，则使用 getTreeNode 直接获取相应的数据
            if (first instanceof TreeNode)
                return ((TreeNode<K,V>)first).getTreeNode(hash, key);
            do { // 非树结构，循环节点判断
                // hash 相等并且 key 相同，则返回此节点
                if (e.hash == hash &&
                    ((k = e.key) == key || (key != null && key.equals(k))))
                    return e;
            } while ((e = e.next) != null);
        }
    }
    return null;
}
```

#### resize源码

由前面的put源码分析我们知道，数组的初始化和扩容都是通过调用resize方法完成的，

在Java中，数组是无法自动扩容的，所以扩容的方法是使用一个新的数组代替已有的容量小的数组，**简单说就是换一个更大的数组重新映射**

当HashMap中的元素个数超过数组大小 x 加载因子时，就会进行数组扩容，上文中我们知道加载因子默认值为0.75，也就是说，默认情况下，数组大小为16，那么当HashMap中元素个数超过16 x 0.75=12的时候，就把数组的大小扩展为2 x 16=32，

即扩大一倍，然后重新计算每个元素在数组中的位置，而这是一个非常消耗性能的操作，所以如果我们已经预知HashMap中元素的个数，设置HashMap元素的个数能够有效的提高HashMap的性能

比如说，我们有1000个元素new HashMap(1000)，但是理论上来讲new HashMap(1024)更合适，即使指定了1000，HashMap也自动会将其设置为1024

但是new HashMap(1024)还不是更合适的，因为0.75 x 1000 < 1000，也就是说为了让0.75 x size > 1000，我们必须这样new HashMap(2048)才最合适，既考虑了&的问题，也避免了resize的问题

所以现在来关注下resize方法的源码：

```java
final Node<K,V>[] resize() {
    // 扩容前的数组
    Node<K,V>[] oldTab = table;
    // 扩容前的数组的大小和阈值
    int oldCap = (oldTab == null) ? 0 : oldTab.length;
    int oldThr = threshold;
    // 预定义新数组的大小和阈值
    int newCap, newThr = 0;
    if (oldCap > 0) {
        // 超过最大值就不再扩容了
        if (oldCap >= MAXIMUM_CAPACITY) {
            threshold = Integer.MAX_VALUE;
            return oldTab;
        }
        // 扩大容量为当前容量的两倍，但不能超过 MAXIMUM_CAPACITY
        else if ((newCap = oldCap << 1) < MAXIMUM_CAPACITY &&
                 oldCap >= DEFAULT_INITIAL_CAPACITY)
            newThr = oldThr << 1; // double threshold
    }
    // 当前数组没有数据，使用初始化的值
    else if (oldThr > 0) // initial capacity was placed in threshold
        newCap = oldThr;
    else {               // zero initial threshold signifies using defaults
        // 如果初始化的值为 0，则使用默认的初始化容量，默认值为16
        newCap = DEFAULT_INITIAL_CAPACITY;
        newThr = (int)(DEFAULT_LOAD_FACTOR * DEFAULT_INITIAL_CAPACITY);
    }
    // 如果新的容量等于 0
    if (newThr == 0) {
        float ft = (float)newCap * loadFactor;
        newThr = (newCap < MAXIMUM_CAPACITY && ft < (float)MAXIMUM_CAPACITY ?
                  (int)ft : Integer.MAX_VALUE);
    }
    threshold = newThr; 
    @SuppressWarnings({"rawtypes","unchecked"})
    Node<K,V>[] newTab = (Node<K,V>[])new Node[newCap];
    // 开始扩容，将新的容量赋值给 table
    table = newTab;
    // 原数据不为空，将原数据复制到新 table 中
    if (oldTab != null) {
        // 根据容量循环数组，复制非空元素到新 table
        for (int j = 0; j < oldCap; ++j) {
            Node<K,V> e;
            if ((e = oldTab[j]) != null) {
                oldTab[j] = null;
                // 如果链表只有一个，则进行直接赋值
                if (e.next == null)
                    newTab[e.hash & (newCap - 1)] = e;
                else if (e instanceof TreeNode)
                    // 红黑树相关的操作
                    ((TreeNode<K,V>)e).split(this, newTab, j, oldCap);
                else { // preserve order
                    // 链表复制，JDK 1.8 扩容优化部分
                    Node<K,V> loHead = null, loTail = null;
                    Node<K,V> hiHead = null, hiTail = null;
                    Node<K,V> next;
                    do {
                        next = e.next;
                        // 原索引
                        if ((e.hash & oldCap) == 0) {
                          	// 如果原元素位置没有发生变化
                            if (loTail == null)
                                loHead = e;
                            else
                                loTail.next = e;
                            loTail = e;

                        }
                        // 原索引 + oldCap
                        else {
                            if (hiTail == null)
                                hiHead = e;
                            else
                                hiTail.next = e;
                            hiTail = e;
                        }
                    } while ((e = next) != null);
                    // 将原索引放到哈希桶中
                    if (loTail != null) {
                        loTail.next = null;
                        newTab[j] = loHead;
                    }
                    // 将原索引 + oldCap 放到哈希桶中
                    if (hiTail != null) {
                        hiTail.next = null;
                        newTab[j + oldCap] = hiHead;
                    }
                }
            }
        }
    }
    return newTab;
}
```

更多的可以查看美团的技术博客 https://tech.meituan.com/2016/06/24/java-hashmap.html

这边对链表扩容复制拆分分析一下，主要参考 [深入理解HashMap(四): 关键源码逐行分析之resize扩容](https://segmentfault.com/a/1190000015812438)

```java
Node<K,V> loHead = null, loTail = null;
Node<K,V> hiHead = null, hiTail = null;
```

上面这段定义了四个Node的引用, 从变量命名上,我们初步猜测, 这里定义了两个链表, 我们把它称为 `lo链表` 和 `hi链表`, `loHead` 和 `loTail` 分别指向 `lo链表`的头节点和尾节点, `hiHead` 和 `hiTail`以此类推.

```java
do {
    next = e.next;
    ...
} while ((e = next) != null);
```

从上面的框架上来看, 就是在按顺序遍历该存储桶位置上的链表中的节点.

我们再看`if-else` 语句的内容:

```java
// 插入lo链表
if (loTail == null)
    loHead = e;
else
    loTail.next = e;
loTail = e;

// 插入hi链表
if (hiTail == null)
    hiHead = e;
else
    hiTail.next = e;
hiTail = e;
```

上面结构类似的两段看上去就是一个将节点e插入链表的动作.

最后再加上 `if` 块, 则上面这段的目的就很清晰了:

> 我们首先准备了两个链表 `lo` 和 `hi`, 然后我们顺序遍历该存储桶上的链表的每个节点, 如果 `(e.hash & oldCap) == 0`, 我们就将节点放入`lo`链表, 否则, 放入`hi`链表.

```java
if (loTail != null) {
    loTail.next = null;
    newTab[j] = loHead;
}
if (hiTail != null) {
    hiTail.next = null;
    newTab[j + oldCap] = hiHead;
}
```

这一段看上去就很简单了:

> 如果lo链表非空, 我们就把整个lo链表放到新table的`j`位置上
> 如果hi链表非空, 我们就把整个hi链表放到新table的`j+oldCap`位置上

综上我们知道, 这段代码的意义就是将原来的链表拆分成两个链表, 并将这两个链表分别放到新的table的 `j` 位置和 `j+oldCap` 上, `j`位置就是原链表在原table中的位置, 拆分的标准就是:

```java
(e.hash & oldCap) == 0
```

**关于 `(e.hash & oldCap) == 0` `j` 以及 `j+oldCap`**

上面我们已经弄懂了链表拆分的代码, 但是这个拆分条件看上去很奇怪, 这里我们来稍微解释一下:

首先我们要明确三点:

1. oldCap一定是2的整数次幂, 这里假设是2^m
2. newCap是oldCap的两倍, 则会是2^(m+1)
3. hash对数组大小取模`(n - 1) & hash` 其实就是取hash的低`m`位

例如:
我们假设 oldCap = 16, 即 2^4,
16 - 1 = 15, 二进制表示为 `0000 0000 0000 0000 0000 0000 0000 1111`
可见除了低4位, 其他位置都是0（简洁起见，高位的0后面就不写了）, 则 `(16-1) & hash` 自然就是取hash值的低4位,我们假设它为 `abcd`.

以此类推, 当我们将oldCap扩大两倍后, 新的index的位置就变成了 `(32-1) & hash`, 其实就是取 hash值的低5位. 那么对于同一个Node, 低5位的值无外乎下面两种情况:

```
0abcd
1abcd
```

其中, `0abcd`与原来的index值一致, 而`1abcd` = `0abcd + 10000` = `0abcd + oldCap`

故虽然数组大小扩大了一倍，但是同一个`key`在新旧table中对应的index却存在一定联系： 要么一致，要么相差一个 `oldCap`。

而新旧index是否一致就体现在hash值的第4位(我们把最低为称作第0位), 怎么拿到这一位的值呢, 只要:

```
hash & 0000 0000 0000 0000 0000 0000 0001 0000
```

上式就等效于

```
hash & oldCap
```

故得出结论:

> 如果 `(e.hash & oldCap) == 0` 则该节点在新表的下标位置与旧表一致都为 `j`
> 如果 `(e.hash & oldCap) == 1` 则该节点在新表的下标位置 `j + oldCap`

根据这个条件, 我们将原位置的链表拆分成两个链表, 然后一次性将整个链表放到新的Table对应的位置上.

怎么样? 这个设计是不是很巧妙, 反正LZ是无比佩服源码作者的!

**总结**

1. resize发生在table初始化, 或者table中的节点数超过`threshold`值的时候, `threshold`的值一般为负载因子乘以容量大小.
2. 每次扩容都会新建一个table, 新建的table的大小为原大小的2倍.
3. 扩容时,会将原table中的节点re-hash到新的table中, 但节点在新旧table中的位置存在一定联系: 要么下标相同, 要么相差一个`oldCap`(原table的大小).

后续，关于红黑树对相关介绍，会另外写一篇文章记录，这里就不多赘述，关于hashmap，知道大概思想，感兴趣的可以自己写一个简易版的hashmap，

### ArrayList

底层基于数组实现的列表

```java
public class ArrayList<E> extends AbstractList<E>
        implements List<E>, RandomAccess, Cloneable, java.io.Serializable
```

这里`RandomAccess`接口主要这个是一个标记性接口，是为了能够更好地判断集合选择更优的遍历方式，提高性能！实现了该接口的话，那么使用普通的for循环来遍历，性能更高，例如arrayList。而没有实现该接口的话，使用Iterator来迭代，这样性能更高，例如linkedList。所以这个标记性只是为了让我们知道我们用什么样的方式去获取数据性能更好。[ArrayList集合实现RandomAccess接口有何作用](https://blog.csdn.net/weixin_39148512/article/details/79234817)

#### 底层结构

ArrayList的底层数据结构是一个Object数组：

```java
transient Object[] elementData;
```

```java
// 缺省容量
private static final int DEFAULT_CAPACITY = 10;
// 空对象数组
private static final Object[] EMPTY_ELEMENTDATA = {};
// 缺省空对象数组
private static final Object[] DEFAULTCAPACITY_EMPTY_ELEMENTDATA = {};
// 元素数组
transient Object[] elementData;
//elementData中已存放的元素的个数，注意：不是elementData的容量
private int size;
// 最大数组容量
private static final int MAX_ARRAY_SIZE = Integer.MAX_VALUE - 8;
```

上面的`elementData`属性采用了`transient来`修饰，表明其不使用Java默认的序列化机制来实例化，但是该属性是ArrayList的底层数据结构，在网络传输中一定需要将其序列化，之后使用的时候还需要反序列化，那不采用Java默认的序列化机制，我们怎么序列化它呢? 那就是使用**writeObject**和**readObject**方法。 这属于序列化的知识，简单说一下，如果目标类中没有定义私有的writeObject或readObject方法，那么序列化和反序列化的时候将调用默认的方法来根据目标类中的属性来进行序列化和反序列化，而如果目标类中定义了私有的writeObject或readObject方法，那么序列化和反序列化的时候将调用目标类指定的writeObject或readObject方法来实现。

#### 构造方法

```java
 /**
     * Constructs an empty list with an initial capacity of ten.
     */
    public ArrayList() {
        //EMPTY_ELEMENTDATA：是个空的Object[]， 将elementData初始化，
        //空的Object[]会给默认大小10，会在add的时候赋值
        this.elementData = DEFAULTCAPACITY_EMPTY_ELEMENTDATA;
    }

  //根据传入的容量创建ArrayList
    public ArrayList(int initialCapacity) {
        if (initialCapacity > 0) {
            this.elementData = new Object[initialCapacity];
        } else if (initialCapacity == 0) {
            this.elementData = EMPTY_ELEMENTDATA;
        } else {
            throw new IllegalArgumentException("Illegal Capacity: "+
                                               initialCapacity);
        }
    }

    //创建一个ArrayList，数据为Collection的数据
    public ArrayList(Collection<? extends E> c) {
        elementData = c.toArray();
        if ((size = elementData.length) != 0) {
            // c.toArray might (incorrectly) not return Object[] (see 6260652)
            if (elementData.getClass() != Object[].class)
                elementData = Arrays.copyOf(elementData, size, Object[].class);
        } else {
            // replace with empty array.
            this.elementData = EMPTY_ELEMENTDATA;
        }
    }
```

#### add源码

```java
	public boolean add(E e) {    
    //确定内部容量是否够了，size是数组中数据的个数，因为要添加一个元素，所以size+1，
    //先判断size+1的个数，这个数组能否放得下
        ensureCapacityInternal(size + 1);  // Increments modCount!!
     //在数据中正确的位置上放上元素e，并且size++
        elementData[size++] = e;
        return true;
    }

	//插入具体某个位置
	public void add(int index, E element) {
        rangeCheckForAdd(index);//检查index也就是插入的位置是否合理。

				//跟上面的分析一样，具体看上面
        ensureCapacityInternal(size + 1);  // Increments modCount!!
				//这个方法就是用来在插入元素之后，要将index之后的元素都往后移一位，
        System.arraycopy(elementData, index, elementData, index + 1,
                         size - index);
				//在目标位置上存放元素
        elementData[index] = element;
        size++;//size增加1
    }

```

很明显，`ensureCapacityInterna`l方法是一个判断数组的长度是否满足新增后size的方法，让我们跟进看一下：

```java
  private void ensureCapacityInternal(int minCapacity) {
    if (elementData == EMPTY_ELEMENTDATA) { //看，判断初始化的elementData是不是空的数组，也就是没有长度
      //判断是否新创建数组，如果是则设为默认容量10
      minCapacity = Math.max(DEFAULT_CAPACITY, minCapacity);
    }
    //这个方法才是真正的判断elementData是否够用
    ensureExplicitCapacity(minCapacity);
  }
```

继续跟进`ensureExplicitCapacity`方法

```java
  private void ensureExplicitCapacity(int minCapacity) {
    modCount++; //这个参数用于判断数据结构变化
    //判断需要的最小容量是否比目前数组的长度length大，如果是，则扩容ArrayList数组
    if (minCapacity - elementData.length > 0)
      grow(minCapacity);
  }
```

继续跟进`grow`方法

```java
    private void grow(int minCapacity) {
        // overflow-conscious code
        int oldCapacity = elementData.length;
       //扩容为当前容量的1.5倍
        int newCapacity = oldCapacity + (oldCapacity >> 1);
        //这句话就是适应于elementData就空数组的时候，length=0，那么oldCapacity=0，newCapacity=0，所以这个判断成立，在这里就是真正的初始化elementData的大小了，就是为10.前面的工作都是准备工作。
        if (newCapacity - minCapacity < 0)
            newCapacity = minCapacity;
        if (newCapacity - MAX_ARRAY_SIZE > 0)
            newCapacity = hugeCapacity(minCapacity);

        elementData = Arrays.copyOf(elementData, newCapacity);
    }
```

简单看下`hugeCapacity`方法

```java
//如果minCapacity都大于MAX_ARRAY_SIZE，那么就Integer.MAX_VALUE返回，反之将MAX_ARRAY_SIZE返回。因为maxCapacity是三倍的minCapacity，可能扩充的太大了，就用minCapacity来判断了。
//Integer.MAX_VALUE:2147483647   MAX_ARRAY_SIZE：2147483639  也就是说最大也就能给到第一个数值。还是超过了这个限制，就要溢出了。相当于arraylist给了两层防护。
    private static int hugeCapacity(int minCapacity) {
        if (minCapacity < 0) // overflow
            throw new OutOfMemoryError();
        return (minCapacity > MAX_ARRAY_SIZE) ?
            Integer.MAX_VALUE :
            MAX_ARRAY_SIZE;
    }
```

#### remove源码

```java
	public E remove(int index) {
        rangeCheck(index);//检查index的合理性

        modCount++;//这个作用很多，比如用来检测快速失败的一种标志。
        E oldValue = elementData(index);//通过索引直接找到该元素

        int numMoved = size - index - 1;//计算要移动的位数。
        if (numMoved > 0)
						//这个方法也已经解释过了，就是用来移动元素的。
            System.arraycopy(elementData, index+1, elementData, index,
                             numMoved);
				//将--size上的位置赋值为null，让gc(垃圾回收机制)更快的回收它。
        elementData[--size] = null; // clear to let GC do its work
				//返回删除的元素。
        return oldValue;
    }

	//通过判断Object是否相同来删除
	public boolean remove(Object o) {
        if (o == null) {
            for (int index = 0; index < size; index++)
                if (elementData[index] == null) {
                    fastRemove(index);
                    return true;
                }
        } else {
            for (int index = 0; index < size; index++)
                if (o.equals(elementData[index])) {
                    fastRemove(index);
                    return true;
                }
        }
        return false;
    }
```

#### 总结

1. arrayList可以存放null。
2. arrayList本质上就是一个elementData数组。
3. arrayList区别于数组的地方在于能够自动扩展大小，其中关键的方法就是gorw()方法。
4. arrayList由于本质是数组，所以它在数据的查询方面(get)会很快，而在插入(add)删除(remove)这些方面，性能下降很多，要移动数组其他元素才能达到应有的效果
5. arrayList实现了RandomAccess，所以在遍历它的时候推荐使用for循环。

### LinkedList

LinkedList是一个实现了List接口和Deque接口的双端链表，底层是双向链表，它也可以被当作堆栈、队列或双端队列进行操作。
 有关索引的操作可能从链表头开始遍历到链表尾部，也可能从尾部遍历到链表头部，这取决于看索引更靠近哪一端。

- LinkedList 实现 List 接口，能对它进行队列操作。
- LinkedList 实现 Deque 接口，即能将LinkedList当作双端队列使用。
- LinkedList 实现了Cloneable接口，即覆盖了函数clone()，能克隆。
- LinkedList 实现java.io.Serializable接口，这意味着LinkedList支持序列化，能通过序列化去传输。
- LinkedList不是线程安全的。

```java
public class LinkedList<E>
    extends AbstractSequentialList<E>
    implements List<E>, Deque<E>, Cloneable, java.io.Serializable
```

#### 底层结构

LinkedList的底层是双向链表，主要是通过Node类来构建。

```java
    private static class Node<E> {
        E item;
        Node<E> next;
        Node<E> prev;

        Node(Node<E> prev, E element, Node<E> next) {
            this.item = element;
            this.next = next;
            this.prev = prev;
        }
    }
```

LinkedList内部是一个双端链表结构，有两个变量，first指向链表头部，last指向链表尾部。 LinkedtList内部的成员变量如下：

```java

    /**
     * Pointer to first node.
     * Invariant: (first == null && last == null) ||
     *            (first.prev == null && first.item != null)
     */
    transient Node<E> first;

    /**
     * Pointer to last node.
     * Invariant: (first == null && last == null) ||
     *            (last.next == null && last.item != null)
     */
    transient Node<E> last;
```

#### add源码

```java
    public boolean add(E e) {
        linkLast(e);
        return true;
    }
    
    void linkLast(E e) {
        final Node<E> l = last; //保存原链表尾部节点
        final Node<E> newNode = new Node<>(l, e, null); //以原链表尾部节点为prev节点创建一个新节点
        last = newNode; //将链表尾部指向新节点
        if (l == null) //如果链表为空，那么该节点既是头节点也是尾节点
            first = newNode;
        else //链表不为空，那么将该结点作为原链表尾部的后继节点
            l.next = newNode;
        size++;
        modCount++;
    }
```

从上面代码可以看到，linkLast方法中就是一个链表尾部添加一个双端节点的操作，但是需要注意对链表为空时头节点的处理。

```java
    public void add(int index, E element) {
        checkPositionIndex(index);

        if (index == size)
            linkLast(element);
        else
            linkBefore(element, node(index));
    }
    
    Node<E> node(int index) {
        // assert isElementIndex(index);

        if (index < (size >> 1)) { //如果索引位置靠链表前半部分，从头开始遍历
            Node<E> x = first;
            for (int i = 0; i < index; i++)
                x = x.next;
            return x;
        } else { //否则，从尾开始遍历
            Node<E> x = last;
            for (int i = size - 1; i > index; i--)
                x = x.prev;
            return x;
        }
    }
    
    void linkBefore(E e, Node<E> succ) {
        // assert succ != null;
        final Node<E> pred = succ.prev;
        final Node<E> newNode = new Node<>(pred, e, succ);
        succ.prev = newNode;
        if (pred == null)
            first = newNode;
        else
            pred.next = newNode;
        size++;
        modCount++;
    }
```

从上面代码可以看到，`add(int index, E element)`方法主要分为3步：

1. 检查index的范围，否则抛出异常
2. 如果插入位置是链表尾部，那么调用linkLast方法
3. 如果插入位置是链表中间，那么调用linkBefore方法

`node(int index)`方法就比较简单，根据index是靠近头部还是尾部选择不同的遍历方向。

`linkBefore(E e, Node succ)`方法表示在succ节点前插入一个值为e的新节点，步骤如下：

1. 将succ节点的前驱节点保存为pred
2. 创建newNode节点，将newNode的后继指针指向succ，前驱指针指向pred
3. 将succ的前驱指针指向newNode
4. 根据pred是否为null，进行不同操作。

- 如果pred为null，说明该节点插入在头节点之前，要重置first头节点
- 如果pred不为null，那么直接将pred的后继指针指向newNode即可

#### remove源码

```java
    //删：remove目标节点
    public E remove(int index) {
        checkElementIndex(index);//检查是否越界 下标[0,size)
        return unlink(node(index));//从链表上删除某节点
    }

    //因为要考虑 null元素，也是分情况遍历
    public boolean remove(Object o) {
        if (o == null) {//如果要删除的是null节点(从remove和add 里 可以看出，允许元素为null)
            //遍历每个节点 对比
            for (Node<E> x = first; x != null; x = x.next) {
                if (x.item == null) {
                    unlink(x);
                    return true;
                }
            }
        } else {
            for (Node<E> x = first; x != null; x = x.next) {
                if (o.equals(x.item)) {
                    unlink(x);
                    return true;
                }
            }
        }
        return false;
    }
    
    //将节点x，从链表中删除,返回删除节点的值
    E unlink(Node<E> x) {
        // assert x != null;
        final E element = x.item;//继续元素值，供返回
        final Node<E> next = x.next;//保存当前节点的后置节点
        final Node<E> prev = x.prev;//前置节点

        if (prev == null) {//前置节点为null，
            first = next;//则首节点为next
        } else {//否则 更新前置节点的后置节点
            prev.next = next;
            x.prev = null;//记得将要删除节点的前置节点置null
        }
        //如果后置节点为null，说明是尾节点
        if (next == null) {
            last = prev;
        } else {//否则更新 后置节点的前置节点
            next.prev = prev;
            x.next = null;//记得删除节点的后置节点为null
        }
        //将删除节点的元素值置null，以便GC
        x.item = null;
        size--;//修改size
        modCount++;//修改modCount
        return element;//返回删除的元素值
    }

```

上面unlink方法的代码可以简单分为以下几个步骤：
 第一步：得到待删除节点的前驱节点和后继节点
 第二步：更新前驱节点和删除节点的prev
 第三步：更新后继节点和删除节点的next
 第四步：将删除节点的元素值置null，以便GC，修改size值
 经过四步，待删除的结点就从链表中脱离了。需要注意的是删除位置是头节点或尾节点时候的处理。

#### Deque接口的方法

`Deque` 是 Double ended queue (双端队列) 的缩写,读音和 deck 一样，蛋壳。
 Deque接口其具体方法的处理逻辑如下所示：

|          | 头部访问      |               | 尾部访问     |              |
| -------- | ------------- | ------------- | ------------ | ------------ |
| 操作     | 抛出异常      | 返回特殊值    | 抛出异常     | 返回特殊值   |
| 插入操作 | addFirst(e)   | offerFirst(e) | addLast(e)   | offerLast(e) |
| 删除操作 | removeFirst() | pollFirst()   | removeLast() | pollLast()   |
| 访问操作 | getFirst()    | peekFirst()   | getLast()    | peekLast()   |

在LinkedList中，返回的特殊值为null或true。举两个简单的例子分析下：

```java
    public E getFirst() {
        final Node<E> f = first;
        if (f == null)
            throw new NoSuchElementException();
        return f.item;
    }
    
    public E peekFirst() {
        final Node<E> f = first;
        return (f == null) ? null : f.item;
     }

```

很简单，当first为null时，getFirst()抛出异常，peekFirst()返回特殊值null。

```java
   public boolean offerFirst(E e) {
        addFirst(e);
        return true;
    }
    
    public void addFirst(E e) {
        linkFirst(e);
    }
```

offerFirst方法返回true，addFirst方法无返回。

#### 总结

LinkedList 是双向列表，能存储null值

链表批量增加，是靠for循环遍历原数组，依次执行插入节点操作。对比ArrayList是通过System.arraycopy完成批量增加的。增加一定会修改modCount。

通过下标获取某个node 的时候，会根据index处于前半段还是后半段 进行一个折半，以提升查询效率

删也一定会修改modCount。 按下标删，也是先根据index找到Node，然后去链表上unlink掉这个Node。 按元素删，会先去遍历链表寻找是否有该Node，如果有，去链表上unlink掉这个Node。

改也是先根据index找到Node，然后替换值。改不修改modCount。

查本身就是根据index找到Node。

LinkedList不光能够向前迭代，还能像后迭代，不光能当链表，还能当队列、栈使用

### TreeMap

TreeMap的底层数据结构就是一个红黑树。关于红黑树的知识另外会开个博客，这里不多赘述。
TreeMap的特点就是存储的时候是根据键Key来进行排序的。其顺序与添加顺序无关，该顺序根据key的自然排序进行排序或者根据构造方法中传入的Comparator比较器进行排序。自然排序要求key需要实现Comparable接口。

#### 底层结构

```java
    //比较器，若无则按Key的自然排序
    private final Comparator<? super K> comparator;
    //树根结点
    private transient Entry<K,V> root;
    //树节点个数
    private transient int size = 0;
    //用于判断数据是否变化
    private transient int modCount = 0;
```

`Entry<K,V>`表示红黑树的一个结点，既然是红黑树，那么每个节点中除了Key-->Value映射之外，必然存储了红黑树节点特有的一些内容

```java
static final class Entry<K,V> implements Map.Entry<K,V> {
        K key;
        V value;
        Entry<K,V> left;
        Entry<K,V> right;
        Entry<K,V> parent;
        boolean color = BLACK;//黑色表示为true，红色为false
}
```

#### 构造方法

```java
public TreeMap() {
    comparator = null;
}

public TreeMap(Comparator<? super K> comparator) {
    this.comparator = comparator;
}


public TreeMap(Map<? extends K, ? extends V> m) {
  comparator = null;
  putAll(m);
}

public void putAll(Map<? extends K, ? extends V> map) {
  int mapSize = map.size();
  //判断map是否SortedMap，不是则采用AbstractMap的putAll
  if (size==0 && mapSize!=0 && map instanceof SortedMap) {
    Comparator<?> c = ((SortedMap<?,?>)map).comparator();
    //同为null或者不为null，类型相同，则进入有序map的构造
    if (c == comparator || (c != null && c.equals(comparator))) {
      ++modCount;
      try {
        buildFromSorted(mapSize, map.entrySet().iterator(),
                        null, null);
      } catch (java.io.IOException cannotHappen) {
      } catch (ClassNotFoundException cannotHappen) {
      }
      return;
    }
  }
  super.putAll(map);
}
```

都比较简单，我们主要关注一下`buildFromSorted`方法和`computeRedLevel`方法。TreeMap主要通过这两个方法在初始化的时候构造一个简单的红黑树。

```java
private static int computeRedLevel(int sz) {
  int level = 0;
  for (int m = sz - 1; m >= 0; m = m / 2 - 1)
    level++;
  return level;
}
```

`computeRedLevel`方法是计算当前结点数的完全二叉树的层数。或者说，着色红色结点的层数。
TreeMap是如何构造红黑树的呢，简单来说，就是把当前的结点按照完全二叉树的结构来排列，此时，最下层的**符合二叉树又未满足满二叉树** 的那一排结点，就全部设为红色，这样就满足红黑树的条件了。（TreeMap中第一层根结点层数为0）

了解了上面的原理，后面就简单了，接着来看buildFromSorted方法：

```java
/**
* level: 当前树的层数，注意：是从0层开始
* lo: 子树第一个元素的索引
* hi: 子树最后一个元素的索引
* redLevel: 上述红节点所在层数
* it: 传入的map的entries迭代器
* str: 如果不为空，则从流里读取key-value
* defaultVal：不为空，则value都用这个值
*/
@SuppressWarnings("unchecked")
private final Entry<K,V> buildFromSorted(int level, int lo, int hi,
                                         int redLevel,
                                         Iterator<?> it,
                                         java.io.ObjectInputStream str,
                                         V defaultVal)
  throws  java.io.IOException, ClassNotFoundException {
  // hi >= lo 说明子树已经构造完成
  if (hi < lo) return null;
  // 取中间位置，无符号右移，相当于除2
  int mid = (lo + hi) >>> 1;
  Entry<K,V> left  = null;
  //递归构造左结点
  if (lo < mid)
    left = buildFromSorted(level+1, lo, mid - 1, redLevel,
                           it, str, defaultVal);
  K key;
  V value;
  //递归完左子树后，迭代器的下一个结点就是每棵树或子树的根结点，所以此时获取的key，value就是树根结点的key，value
  if (it != null) {
    if (defaultVal==null) {
      Map.Entry<?,?> entry = (Map.Entry<?,?>)it.next();
      key = (K)entry.getKey();
      value = (V)entry.getValue();
    } else {
      key = (K)it.next();
      value = defaultVal;
    }
  // 通过流来读取key, value
  } else {
    key = (K) str.readObject();
    value = (defaultVal != null ? defaultVal : (V) str.readObject());
  }
  //构建结点
  Entry<K,V> middle =  new Entry<>(key, value, null);
  // 这里是判断该节点是否是最下层的叶子结点。
  if (level == redLevel)
    middle.color = RED;
  //如果存在的话，设置左结点，
  if (left != null) {
    middle.left = left;
    left.parent = middle;
  }
  // 递归构造右结点
  if (mid < hi) {
    Entry<K,V> right = buildFromSorted(level+1, mid+1, hi, redLevel,
                                       it, str, defaultVal);
    middle.right = right;
    right.parent = middle;
  }
  return middle;
}
```



## 相关链接

[java学习笔记](https://juejin.cn/post/6844903740986621966)

