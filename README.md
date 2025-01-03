
# 1、包装类都有哪些？


基本类型都有对应的包装类型，这些包装类提供了一种面向对象的方式来处理基本数据类型，允许它们被用于需要对象的场景，如集合框架、泛型等。


对应关系：




| 基本类型 | 包装类型 |
| --- | --- |
| boolean | Boolean |
| byte | Byte |
| char | Character |
| float | Float |
| int | Integer |
| long | Long |
| short | Short |
| double | Double |


# 2、包装类特点


* 封装性：所有的包装类都是 final 类，这意味着它们不能被继承。这种设计确保了包装类的行为和特性的一致性，从而避免了子类可能引入的不确定性。
* 不可变性：包装类的实例一旦被创建后，其中保存的基本数据类型数据就不能再被改变。这种不可变性使得包装类在多线程环境中更加安全，避免了因数据被意外修改而导致的错误。
* 提供方法：包装类封装了许多实用的方法，提供了丰富的功能。例如，它们支持数据类型转换、判断字符串的大小写、以及获取最大值和最小值等。
* 继承关系：除了 Character 和 Boolean 之外，其他所有的包装类都继承自 Number 类。这种继承关系使得这些包装类能够共享一些通用的功能和特性，例如数字的比较和转换，这为在不同数值类型之间的操作提供了一致的接口。


代码为证（继承Number类并实现intValue方法的类）：


![](https://img2024.cnblogs.com/blog/494241/202412/494241-20241225161244715-1244198304.png)


 


# 3、为什么会出现包装类？


既然有基本类型了，为什么还会出现对应的包装类？


我觉得根本原因还是因为Java是面向对象的语言，基本数据类型不能参与面向对象编程：


对象操作：在Java中，许多集合类和框架方法需要对象作为参数，而不是基本数据类型。为了满足这一需求，包装类提供了将基本数据类型转换为对象的机制。通过使用包装类，我们可以轻松地在这些方法中传递基本数据类型。


Null值处理：基本数据类型无法为null，而包装类则可以。这一特性在某些情况下非常有用，例如在方法参数中，需要表示可选值或缺省值时。通过使用包装类，我们能够更灵活地处理这些场景，确保代码的健壮性和可读性。这种设计使得我们在处理数据时，可以更方便地进行null值检查，并在需要时安全地进行区分，从而提高了代码的灵活性。


# 4、装箱与拆箱


**装箱（Boxing）：**是将基本数据类型转换为相应的包装类的过程。


**拆箱（Unboxing）：**是将包装类转换为基本数据类型的过程。


### 手动装箱、拆箱


手动装箱：使用一个本地类型的值创建一个对应包装类对象的过程




```
1 int num = 10;
2  
3 Integer int1 = new Integer(num); // 手动装箱方式一
4  
5 Integer int2 = Integer.valueOf(num); // 手动装箱方式二
```


 


手动拆箱：使用 Integer 类型对象的 intValue() 方法来获取这个对象的 int 值




```
1 Integer number= new Integer(23);
2 int num = number.intValue(); // 手动拆箱
```


 


### 自动装箱、拆箱


Java 5引入了自动装箱（Auto\-boxing）和自动拆箱（Auto\-unboxing）机制，简化了基本数据类型与包装类之间的转换过程。


自动装箱是将基本数据类型自动转换为其对应的包装类对象的过程。自动装箱的底层原理其实就是通过调用包装类的valueOf()方法来实现的；同理自动拆箱就是通过调用包装类的xxxValue()方法来实现的。


以Integer 与 int 举例：




```
 Integer x = 2;     // 装箱 调用了 Integer.valueOf(2)
 int y = x;         // 拆箱 调用了 x.intValue()
```


# 5、包装类的缓存池


### 简要介绍


Java中的包装类缓存机制是为了优化性能和节省内存而设计的。


它为整型（Byte、Short、Integer、Long）、字符型（Character）和布尔型（Boolean）的包装类提供了缓存，确保在这些类型的小范围值之间可以复用对象。而对于浮点数类型的包装类（Float、Double），则没有这种缓存机制，意味着每次都需要创建新的对象。


这样一来，Java在处理常用值时更加高效，但在浮点数处理上则相对简单直接。


### 缓存范围


对于 Integer 类，Java会缓存范围在 \-128 到 127 之间的所有整数。


对于 Byte、Short 和 Character 类，缓存的范围也是类似的。具体范围如下：


Byte：\-128 到 127Short：\-128 到 127Character：0 到 127（即所有的ASCII字符）Boolean：只有 true 和 false 两个值会被缓存。


### 如何触发缓存


只有调用 valueOf() 方法时，如果要创建的值已经被缓存，则会触发缓存机制。如果要创建的 Integer 对象的值在预定范围内，则返回缓存的对象，如果不在范围内，则直接新创建一个对象。


我们来查看 Integer 类的 valueOf() 方法的源代码（valueOf() 方法就是先判断值是否在缓存池中，如果在的话就直接返回缓存池的内容，不存在就创建一个新对象，跟上面我们所说的逻辑是一致）：




```
 1     /**
 2      * Returns an {@code Integer} instance representing the specified
 3      * {@code int} value.  If a new {@code Integer} instance is not
 4      * required, this method should generally be used in preference to
 5      * the constructor {@link #Integer(int)}, as this method is likely
 6      * to yield significantly better space and time performance by
 7      * caching frequently requested values.
 8      *
 9      * This method will always cache values in the range -128 to 127,
10      * inclusive, and may cache other values outside of this range.
11      *
12      * @param  i an {@code int} value.
13      * @return an {@code Integer} instance representing {@code i}.
14      * @since  1.5
15      */
16     public static Integer valueOf(int i) {
17         if (i >= IntegerCache.low && i <= IntegerCache.high)
18             return IntegerCache.cache[i + (-IntegerCache.low)];
19         return new Integer(i);
20     }
```


  编译器会在自动装箱过程调用 valueOf() 方法，因此多个值相同且值在缓存池范围内的 Integer 实例使用自动装箱来创建，那么就会引用相同的对象。




```
1 Integer m = 123;
2 Integer n = 123;
3 System.out.println(m == n); // true
```


在 Java 8 中，Integer 缓存池的大小默认为 \-128\~127。


在 jdk 1\.8 所有的数值类缓冲池中，Integer 的缓冲池 IntegerCache 很特殊，这个缓冲池的下界是 \- 128，上界默认是 127，但是这个上界是可调的，在启动 jvm 的时候，通过 \-XX:AutoBoxCacheMax\= 来指定这个缓冲池的大小，该选项在 JVM 初始化的时候会设定一个名为 java.lang.IntegerCache.high 系统属性，然后 IntegerCache 初始化的时候就会读取该系统属性来决定上界。


下面是IntegerCache的源码（其中红色部分正是获取系统属性重新设置上界的逻辑）




```
 1     private static class IntegerCache {
 2         static final int low = -128;
 3         static final int high;
 4         static final Integer cache[];
 5 
 6         static {
 7             // high value may be configured by property
 8             int h = 127;
 9             String integerCacheHighPropValue =
10                 sun.misc.VM.getSavedProperty("java.lang.Integer.IntegerCache.high");
11             if (integerCacheHighPropValue != null) {
12                 try {
13                     int i = parseInt(integerCacheHighPropValue);
14                     i = Math.max(i, 127);
15                     // Maximum array size is Integer.MAX_VALUE
16                     h = Math.min(i, Integer.MAX_VALUE - (-low) -1);
17                 } catch( NumberFormatException nfe) {
18                     // If the property cannot be parsed into an int, ignore it.
19                 }
20             }
21             high = h;
22 
23             cache = new Integer[(high - low) + 1];
24             int j = low;
25             for(int k = 0; k < cache.length; k++)
26                 cache[k] = new Integer(j++);
27 
28             // range [-128, 127] must be interned (JLS7 5.1.7)
29             assert IntegerCache.high >= 127;
30         }
31 
32         private IntegerCache() {}
33     }
```


 


### 验证包装类的缓存池（以Integer为例）


 




```
1 Integer x = new Integer(123);
2 Integer y = new Integer(123);
3 System.out.println(x == y);    // false
4 Integer z = Integer.valueOf(123);
5 Integer k = Integer.valueOf(123);
6 System.out.println(z == k);   // true
```


 


 


# 6、补充


**基本类型与包装类如何选择**：


* 内存占用和性能：基本数据类型直接在栈中分配内存，占用空间较少，性能更高。而包装类是对象类型，需要在堆中分配内存，GC管理，因此会稍微影响性能
* 使用场景：一般来说，在性能要求较高的代码中，我们优先使用基本数据类型。而在需要面向对象的场景下（例如集合类中需要使用对象类型），我们会选择包装类。包装类还提供了一些静态方法和常量，比如Integer.parseInt()、Double.NaN等，这些方法和属性是基本数据类型所不具备的


**基本类型与包装类\=\=比较：**


只要判断中有基本数据类型，则判断的就是值是否相等，也就是说包装类在这时会自动拆箱。


![](https://img2024.cnblogs.com/blog/494241/202412/494241-20241226160314288-1788220073.png)


 




```
1 public class Example {
2     public static void main(String[] args) {
3         Integer i1 = 10;
4         int i2 = 10;
5  
6         System.out.println(i1 == i2);
7     }
8 }
```


 


**基本类型与包装类存储区别（基本类型（primitive types）和包装类的存储位置取决于它们是在哪里声明）：** 


 


**局部变量（Local Variables）**


栈（Stack）：如果基本类型是作为方法中的局部变量声明的，那么它们会被存储在栈中。栈用于管理方法调用和局部变量，具有后进先出（LIFO）的行为。自动分配和释放：当方法被调用时，其局部变量会在栈上分配空间，并且在方法执行完毕后自动释放。


**成员变量（Instance Variables）**


堆（Heap）：如果基本类型是类的成员变量（即实例变量），那么它们会随着对象一起被存储在堆中。每个对象都有自己的成员变量副本，这些数据与对象本身一同存放在堆内存中。生命周期依赖于对象：成员变量的生命周期与所属的对象相同，只要对象存在，成员变量就存在；对象被垃圾回收时，成员变量也会被回收。


**静态变量（Static Variables）**


方法区/元空间（Method Area/Metaspace）：静态变量（无论是否为基本类型）属于类的一部分，而不是某个特定的对象实例。它们通常存储在方法区（Java 7及之前）或元空间（Java 8及之后）。不过，在某些实现中，静态变量也可能直接存放在堆中，因为方法区本身可以被视为堆的一部分。共享性：静态变量由所有对象实例共享，因此它们不是随单个对象创建而创建，而是随着类加载到 JVM 时初始化 。


代码举例：




```
 1 public class Example {
 2     // 成员变量，存储在堆中
 3     int instanceVar;
 4 
 5     // 静态变量，存储在方法区或元空间
 6     static int staticVar;
 7 
 8     public void method() {
 9         // 局部变量，存储在栈中
10         int localVar = 10;
11     }
12 }
```


 


 本博客参考[FlowerCloud机场](https://yunbeijia.com)。转载请注明出处！
