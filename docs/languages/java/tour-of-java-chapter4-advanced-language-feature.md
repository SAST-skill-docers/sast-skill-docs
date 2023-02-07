# Tour of Java: Chapter 4 Advanced Language Feature

## 前置知识

- 基本 Java 语法。

???+ 关于JEP

    目前，Java 的新特性、功能均需要先提出并起草 JEP（JDK Enhancement Proposals）*草案。在 OpenJDK 社区充分审核、修改、投票后成为正式的、带编号 *JEP* 提案。在开发 *JEP* 对应的功能分支，通过所有平台测试、代码审查后，择期并入下一个发行版本的主分支。本章提到的 *JEP* 都标注了并入的 Java 版本和提案原文连接，便于同学们学习参考。


## Switch Expressions [[JEP361](https://openjdk.org/jeps/361), Java14]

Switch Expressions [[JEP361](https://openjdk.org/jeps/361), Java14] 与传统的`switch`不同，使用`->`连接`case`子句和具体代码块，并且`case`子句可以一次匹配多个，代码块也不需要`break`来防止连续执行。

```java
switch (day) {
    case MONDAY, FRIDAY, SUNDAY -> System.out.println(6);
    case TUESDAY                -> System.out.println(7);
    case THURSDAY, SATURDAY     -> System.out.println(8);
    case WEDNESDAY              -> System.out.println(9);
}
```

`switch`表达式，顾名思义可以产生表达式的值：

```java
static void howMany(int k) {
    System.out.println(
        switch (k) {
            case  1 -> "one";
            case  2 -> "two";
            default -> "many";
        }
    );
}
```

如果需要复杂代码块，使用`{}`包围，并使用`yield`关键字产生`case`子句对应的值：

```java
int j = switch (day) {
    case MONDAY  -> 0;
    case TUESDAY -> 1;
    default      -> {
        int k = day.toString().length();
        int result = f(k);
        yield result;
    }
};
```

## Pattern Matching for instanceof [[JEP394](https://openjdk.org/jeps/394), Java16]

使用`instanceof`判断一个对象是否为一个类的实例。一般来说，如果判断出确实是某一个类的实例，那下面一般都要向下转型为那个类的对象后再使用：

```java
Object o = createObject();
if (o instanceof MyClass) {
    var myobj = (MyClass) o;
    // do something with myobj
}
```

为了简化这这一过程，引入了`instanceof`的模式匹配语法：

```java
Object o = createObject();
if (o instanceof MyClass myobj) {
    // do something with myobj
}
```

模式匹配出的变量名的作用域会由Java自动推导：

例如：

```java
Object o = createObject();
if (o instanceof MyClass myobj) {
    // myobj is available
}
// myobj is NOT available
```

```java
Object o = createObject();
if (!(o instanceof MyClass myobj)) {
    // myobj is NOT available
}
// myobj is available
```

```java
Object o = createObject();
if (o instanceof String s || s.length() > 5) { // CE!
}
```



## Records [[JEP395](https://openjdk.org/jeps/395), Java16]

对于纯数据类，我们仍然要定义一大堆构造函数、赋值、Getter&Setter、`toString`、`hashCode()`、`equals(Object)`等一大堆方法。尽管这些方法都可以让 IDE 帮忙生成，但是一大堆没营养的代码堆在那里都让人觉得异常繁琐。

记录(英语：Record） 就是为了解决这个问题而生。`Record`是一个**只读**的数据类，并且 Java 编译器会自动生成上面说的那一堆方法。



```java
record Point(int x, int y) {}
```

这就是一个典型的`Record`。它大多数行为都与普通类没有区别，除了它的 Getter 方法不叫`getXXX` 而直接和变量名相同。

```shell
jshell> var p1 = new Point(1, 2)
p1 ==> Point[x=1, y=2]
jshell> p1.toString()
$35 ==> "Point[x=1, y=2]"
jshell> p1.hashCode()
$36 ==> 33
```

```shell
jshell> p1.x() //getter 方法
$37 ==> 1
jshell> var p2 = new Point(2, 3)
p2 ==> Point[x=2, y=3]
jshell> p1.equals(p2)
$38 ==> false
```

### Compact Canonical Constructor 紧凑型构造器

可以使用紧凑型构造器来预处理或验证输入参数：

```java
record Rational(int num, int denom) {
    Rational {
        int gcd = gcd(num, denom);
        num /= gcd;
        denom /= gcd;
    }
}
```

紧凑型构造器（英语：Compact Canonical Constructor）运行完后，Java 会再根据改变后的参数值对成员域进行一遍初始化。

相当于传统的构造器：

```java
record Rational(int num, int denom) {
    Rational(int num, int demon) {
        int gcd = gcd(num, denom);
        num /= gcd;
        denom /= gcd;
        this.num = num;
        this.denom = denom;
    }
}
```



`record`的使用有以下规则：

- *record*类不应该继承其他的类，因为它默认继承`java.lang.Record`；
- *record*类隐式为`final`，并不允许为`abstract`；
- *record*类的成员变量隐式为`final`。



## Annotation

注解（英语：Annotation）为代码提供了一些附加信息，如`@Override`。通过提供响应的注解处理程序，可以实现以下等功能：

1. 编译时代码检查；
2. 编译时代码生成；
3. 运行时动态发现与注册。

本文不讲解反射系统，因此也不会设计注解处理程序的设计。本节的目的在于如何使用注解语法。



Java 标准库内提供的注解有：

- `@Override`：检查是否重载方法。
- `@Deprecated`：表示注解的类、常量、方法被废弃。
- `@FunctionalInterface`：表示注解的接口是函数式接口。
- `@SuppressWarnings`：用于让编译器忽略特定的警告。
- 

注解也可以有参数值，如`@Deprecated`有`forRemoval`（警告后续版本会被移除）和`since`（从哪个版本开始废弃），语法如下：

```java
@Deprecated(forRemoval=true, since="v233")
class OldClass { /* ... */ }
```



声明注解的语法如下：

```java
@Documented
@Retention(RetentionPolicy.RUNTIME)
@Target(value={CONSTRUCTOR, FIELD, LOCAL_VARIABLE,
                METHOD, PACKAGE, MODULE, PARAMETER, TYPE})
public @interface Deprecated {
    String since() default "";
    boolean forRemoval() default false;
}
```

- `@Document`：提示这个注解应该被收入 JavaDoc。
- `@Retention(RetentionPolicy.RUNTIME)`：表示这个注解的信息应该保留到什么时候。
  - `RetentionPolicy.RUNTIME`：保留至运行时，反射系统可以读取。
  - `RetentionPolicy.CLASS`：保留至类文件。
  - `RetentionPolicy.SOURCE`：编译后即被丢弃。
- `@Target(value={ /* ... */ })`：提示这个注解可以应用给谁。

- `@interface`：声明注解。
- `<Type> name() [default <value>]`：声明某一类型的参数，可以提供默认值。

注解系统在 Java 标准库中应用较少，但是在其他库，如 Lombok ，Spring 等中，都会起到举足轻重的作用。如果有时间，我们也会接触到这些类库。



## Exception Handling

想要向外部抛出异常表示程序出错，使用`throw`：

```java
if (!SUPPORTED_FILETYPE.contains(filetype)) {
    throw new IllegalArgumentException(filetype + " is not supported");
}
```

异常均支持在实例化的时候提供自定义的错误信息以便调试查错。

想要捕获异常，使用`try-catch`：

```java
try {
    var content = Files.readString(Path.of(location));
} catch (IOException e) {
    System.err.println("Failed to read " + location);
    e.printStackTrace();
}
```

使用`printStackTrace()`函数打印异常栈信息，描述了异常传播的途径和对应的源代码位置等信息。



你可以利用捕获的异常去构建其他类型等异常并重新抛出，原来捕获等异常就称为新异常的*原因*。这样可以提升异常系统的语义，也防止单一异常栈过长：

```java
try {
    var config = Files.readString(Path.of(configFile));
} catch (IOException e) {
    throw new GameInitException("Failed to read config file", e);
}
```



你可以一次捕获多个异常：

```java
try {
    var config = Files.readString(Path.of(configFile));
    var jsonConfig = parseJSON(config);
} catch (IOException e) {
    throw new GameInitException("Failed to read config file", e);
} catch (JSONException e) {
    throw new GameInitException("Config file format error", e);
}
```



如果有一些系统资源，如文件句柄等，无论后续读取是否正常，都应该将其关闭。这时就需要`finally`子句进行资源回收：

```java
var input = new FileReader("file.txt");
try {
    // read data using input
} catch (IOException e) {
    // error handling
} finally {
    input.close();
}
```



对于实现了`AutoCloseable`接口的类，如所有输入输出组件，支持`try-with-resources`自动调用`close()`而无需`finally`：


```java
try (var input = new FileReader("file.txt")) {
    // read data using input
} catch (IOException e) {
    // error handling
}
```





异常分为两类：

- `java.lang.RuntimeException`：运行时异常，又称为**非检查异常**。通常表示程序逻辑错误，比如数组越界，空指针等问题。
- 其他`java.lang.Exception`子类但是不是`java.lang.RuntimeException`子类的其他类：**受检查异常**。通常表示外部环境诱导的错误，程序应该考虑这些情况并进行处理，比如文件不存在，网络连接超时等问题。



受检查异常得名的原因在于：

- 可能抛出受检查异常的代码必须被`try-catch`处理，或者向外部声明自己可能传播受检查异常。

通过`throws`子句声明方法可能会抛出某些受检查异常：

```java
public static void main(String[] args) throws IOException {
    var content = Files.readString(Path.of(args[0]));
}
```



你可以使用Java标准库中提供的异常类，请参见[API Ref](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/lang/Exception.html)中的`Direct Known Subclasses`小节寻找是否有自己需要的异常类。

通过继承自`java.lang.Exception`或`java.lang.RuntimeException`，你也可以创建自定义的受检异常和非受检异常类。你可以让其包含更多调试信息或生成更清晰直观的`toString()`表示。



## Generic 泛型

通过以下的例子来了解泛型（英语：Generic）的动机：

所有的列表都是`java.util.List`的实现类，而它是可以存储任意对象的，即`java.lang.Object`。如果我们想存一个文件名列表，还需要进行强制类型转换：

```java
List filenames = new ArrayList();
filename.add("file1.txt");

String aFile = (String) filenames.get(0);
```

一方面，我们不期望这个`filenames`里面存在任何非字符串的对象，但是没有禁止我们插入：

```java
filenames.add(114514);
```

这样在运行时，无辜的代码会执行`(String)`类型转换，于是会引起`java.lang.ClassCastException`。



另一方面，在每次使用的时候都进行一遍类型转化显然是非常蠢的语法。

所以有什么办法能够将我们的列表和字符串类型绑定在一起呢？这就是泛型：

```java
var filenames = new ArrayList<String>();
filename.add("file1.txt");
String aFile = filename.get(0);

// filename.add(114514);
```

### 语法

使用泛型的语法如上所示，在类型名后面的`<>`内依次输入*类型参数*即可：

```java
List<String> // 存储String的List列表
Function<String, Integer> // 接受String类型参数返回一个整数的函数
```

定义泛型类或接口的方法也和使用的方法相似，我们使用*类型变量*来作为需要关联的类型的占位符：

```java
public interface List<T> {
    T get(int index);
    void add(T t);
    void set(int index, T t);
    // ...
}
```

定义泛型方法的方式略有不同，你需要在返回值类型前加入类型变量列表。使用泛型方法时，需要在`.`和方法名之间插入类型参数列表：

```java
class ArrayUtil {
    public static <T> T getMiddle(T... a) {
        return a[a.length / 2];
    }
}

var middle = ArrayUtil.<String>getMiddle("one", "two", "three"); // "two"
```

在几乎大多数情况下，类型参数列表可以自动推导：

```java
var middle = ArrayUtil.getMiddle("one", "two", "three");
```

### 对类型变量的限定

有时我们不想要所有类型都混入我们的方法中，因此需要对类型变量做一些限定，比如下面寻找数组最小值的方法：

```java
class ArrayUtil {
    public static <T> T getMin(T[] a) {
        if (a == null || a.length == 0) return null;
        T min = a[0];
        for(int i = 1;i<a.length;i++)
            if (min.compareTo(a[i]) > 0)
                min = a[i];
        return min;
    }
}
```



但是并不是所有的对象都支持正确的比较方法，因此我们限定`T`应当是可比较的，即`Comparable`。

```java
class ArrayUtil {
    public static <T extends Comparable> T getMin(T[] a) {
        if (a == null || a.length == 0) return null;
        T min = a[0];
        for(int i = 1;i<a.length;i++)
            if (min.compareTo(a[i]) > 0)
                min = a[i];
        return min;
    }
}
```

如果想要`T`同时是多个接口的实现类，那么可以使用`&`连接这些接口：

```java

<T extends Comparable & Serializable>
```

### 通配符类型

由于 Java 限制，下面代码是不能通过编译的：

```java
class Person {}
class Student extends Person {}

public static void printPersons(List<Person> persons) { /* ... */ }

public static void main(String[] args) {
    var students = new ArrayList<Student>();
    // add students
    printPersons(students);
}
```

简单来说，就是虽然`Student`是`Person`的子类型，但是`List<Student>`不是`List<Person>`的子类型。这听起来确实有点反直觉，因为学生的列表就应该是人的列表。

为了解决这种情况，Java引入了通配符类型`?`，用于表示类型参数的派生：

```java
public static void printPersons(List<? extends Person> persons) {
    for(Person p : persons)
        System.out.println(p);
}
```

## 资源链接

Switch Expressions [JEP361]
https://openjdk.org/jeps/361

Pattern Matching for instanceof
[JEP394]https://openjdk.org/jeps/394

Records [JEP395]
https://openjdk.org/jeps/395

Java API ref 异常类https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/lang/Exception.html

Switch Expressions [JEP361]https://openjdk.org/jeps/361