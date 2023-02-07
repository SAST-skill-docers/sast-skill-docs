# Tour of Java: Chapter 1 :Basic  Language Structure

## 前置知识

+无。



## Java is a *C-style* language

### Comments

```java
// This is a one-line comment

/*
 * This is a multi-line comment
 */

/**
 * This is JavaDoc
 */
```



### Variable Declare & Assignment / Data types

```java
jshell> int a;
a ==> 0
jshell> a = 1;
a ==> 1
jshell> long b = 1L
b ==> 1
jshell> char e = 'E'
e ==> 'E'
jshell> String f = "java"
f ==> "java"
jshell> boolean g = true
g ==> true
```

```java
jshell> float c = 1.0
|  错误:
|  不兼容的类型: 从double转换到float可能会有损失
|  float c = 1.0;
|            ^-^
jshell> float c = 1.0f
c ==> 1.0
jshell> c = (float) 2.0
c ==> 2.0
jshell> double d = 1.0
d ==> 1.0
```

- 整数类型：`byte` `short` `int` `long`分别为 1、2、4、8 字节*有符号*整数。

- 浮点数：`float` `double`分别为 4、8 字节浮点数。

- 逻辑值：`boolean`。

- 字符：`char` 2 字节 Unicode 字符。

  

### Operators: Arithmetic

```java
jshell> 1 + 1
$2 ==> 2
jshell> 2 * 2
$3 ==> 4
jshell> 3 / 2
$4 ==> 1
jshell> 3.0 / 2
$5 ==> 1.5
jshell> 3 % 2
$6 ==> 1
jshell> -3 % 2
$6 ==> -1
```

```java
jshell> int a = 1;
a ==> 1
jshell> a += 100;
$17 ==> 101
jshell> a *= 20;
$18 ==> 2020
jshell> a /= 3;
$19 ==> 673
jshell> a %= 20;
$20 ==> 13
```



### Operators: ++ --

```java
jshell> int a = 1;
a ==> 1

jshell> ++a
$29 ==> 2
jshell> --a
$30 ==> 1

jshell> a++
$31 ==> 1
jshell> a--
$32 ==> 2
```



### Operators: Bitwise


```java
jshell> 1 & 2
$11 ==> 0

jshell> 1 | 2
$12 ==> 3

jshell> 1 ^ 2
$13 ==> 3

jshell> ~1
$14 ==> -2

jshell> 1 << 20;
$21 ==> 1048576

jshell> -1 << 20;
$22 ==> -1048576

jshell> 1024 >> 3;
$23 ==> 128

jshell> -1024 >> 3;
$24 ==> -128

jshell> -1024 >>> 3;
$25 ==> 536870784
```

对最后两个例子的解释 :

Java 中 ```-1024``` 作为字面量，被视为**有符号的**```int```类型，以补码表示为```11111111 11111111 11111100 00000000```。

```>>```的行为：如果被操作的数的最高位是```1```，则右移时在高位补```1``` ；如果被操作的数的最高位是```0```，则右移时在高位补```0```。

```>>>```的行为：右移时在高位补```0```。

因此，```-1024 >> 3```   得到 ```11111111 11111111 11111111 10000000``` ，即```-128```。

因此，```-1024 >>> 3``` 得到 ```00011111 11111111 11111111 10000000``` ，即```563870784```。



### Operators: Compare

```java
jshell> int a = 1, b = 2;
a ==> 1
b ==> 2

jshell> a < b
$3 ==> true

jshell> a <= b
$4 ==> true

jshell> a == b
$5 ==> false
    
jshell> a != b
$6 ==> true

jshell> a >= b
$7 ==> false

jshell> a > b
$8 ==> false
```



### Operators: Logic

```java
jshell> true && false
$15 ==> false

jshell> true || false
$16 ==> true

jshell> !true
$17 ==> false
    
jshell> "hello" + "world"
$10 ==> "helloworld"

jshell> "1 + 1 = " + (1 + 1)
$11 ==> "1 + 1 = 2"

jshell> System.out.println("hello\nworld")
hello
world

jshell> System.out.println("Col1\tCol2\n1\t2\n11111\t22222")
Col1	Col2
1	2
11111	22222
```



### Function / Method Syntax

```java
jshell> int mul(int a, int b) {
   ...>     return a * b;
   ...> }
|  已创建 方法 mul(int,int)

jshell> mul(2, 3)
$20 ==> 6
```



### Control Flow: if-else

```java
jshell> boolean canIGetA(double score) {
   ...>     if (score >= 90) {
   ...>         System.out.println("Good Job! You got a 4.0!");
   ...>         return true;
   ...>     } else if (score >= 70) {
   ...>         System.out.println("Keep Going! You can do better!");
   ...>         return false;
   ...>     } else {
   ...>         System.out.println("Emm... What happends?");
   ...>         return false;
   ...>     }
   ...> }
|  已创建 方法 canIGetA(double)
    
jshell> canIGetA(100)
Good Job! You got a 4.0!
$2 ==> true

jshell> canIGetA(80)
Keep Going! You can do better!
$3 ==> false

jshell> canIGetA(0)
Emm... What happends?
$4 ==> false
```



### Control Flow: ?:

```java
jshell> int gcd(int a, int b) {
   ...>     return b == 0 ? a : gcd(b, a % b);
   ...> }
|  已创建 方法 gcd(int,int)

jshell> gcd(48, 18)
$26 ==> 6
```



### Control Flow: switch

```java
jshell> int calculate(int a, int b, char op) {
   ...>     int res;
   ...>     switch(op) {
   ...>         case '+': res = a + b; break;
   ...>         case '-': res = a - b; break;
   ...>         case '*': res = a * b; break;
   ...>         case '/': res = a / b; break;
   ...>         default: res = -1;
   ...>     }
   ...>     return res;
   ...> }
|  已创建 方法 calculate(int,int,char)

jshell> calculate(2, 3, '+')
$6 ==> 5
jshell> calculate(2, 3, '-')
$7 ==> -1
jshell> calculate(2, 3, '*')
$8 ==> 6
jshell> calculate(2, 3, '/')
$9 ==> 0
jshell> calculate(2, 3, '^')
$10 ==> -1
```



### Control Flow: for

```java
jshell> boolean isPrime(int a) {
   ...>     if (a <= 1) return false;
   ...>     for (int i = 2; i * i <= a; i++) {
   ...>         if(a % i == 0) return false;
   ...>     }
   ...>     return true;
   ...> }
|  已创建 方法 isPrime(int)

jshell> for (int a = 1; a <= 100; a++) {
   ...>     if (isPrime(a)) System.out.print(a + " ");
   ...> }
2 3 5 7 11 13 17 19 23 29 31 37 41 43 47 53 59 61 67 71 73 79 83 89 97
```



### Control Flow: while

```java
jshell> import java.lang.Math;

jshell> double x;
x ==> 0.0

jshell> while( (x = Math.random()) < 0.8) {
   ...>     System.out.println(x + " < 0.8");
   ...> }
0.17636000596270907 < 0.8
0.5029857602823097 < 0.8
0.3349284868587292 < 0.8
0.3650514335114553 < 0.8
```



### Control Flow: do-while

```java
jshell> do {
   ...>     x = Math.random();
   ...> } while(x < 0.8);

jshell> x // is a random double >= 0.8
x ==> 0.8346328176085303
```



## Other basic syntax & API

### *[[JEP286](https://openjdk.org/jeps/286), Java10]* `var` type inference

```java
jshell> var mapOfMap = new HashMap<String, Map<String, String>>()
mapOfMap ==> {}

jshell> mapOfMap.put("Java", Map.of("Type", "Strong-Typed", "Paradigm", "OOP"))
$111 ==> null

jshell> mapOfMap
mapOfMap ==> {Java={Paradigm=OOP, Type=Strong-Typed}}

jshell> mapOfMap.forEach(
    ..>     (var key, var val) -> System.out.println(key + " : " + val))
Java : {Paradigm=OOP, Type=Strong-Typed}
```





### *[[JEP378](https://openjdk.org/jeps/378), Java15]* Text Blocks

`"""`包含的字符串称为*文本块 TextBlock*。
文本块允许使用`"`而不需转义，`\"""`则需要转义。

```java
jshell> """
   ...> Line 1
   ...> Line 2
   ...> Line 3
   ...> Line 4
   ...> """
$4 ==> "Line 1\nLine 2\nLine 3\nLine 4\n"
```


使用`\`取消换行

```java
jshell> """
   ...> Line 1 \
   ...> Line 2 \
   ...> Line 3 \
   ...> Line 4 \
   ...> """
$5 ==> "Line 1 Line 2 Line 3 Line 4\n"
```


使用`\s`保证末尾空格不会删除，否则末尾空格会被替换为换行符。下面用`·`表示空格，而非实际字符：


```java
jshell> """
   ...> red···
   ...> green·
   ...> blue··
   ...> """
$1 ==> "red\ngreen\nblue\n"
    
jshell> """
   ...> red···\s
   ...> green·\s
   ...> blue··\s
   ...> """
$1 ==> "red···\ngreen·\nblue··\n"
```




### Array

```java
jshell> var matrix = new int[5][5];
matrix ==> int[5][] { int[5] { 0, 0, 0, 0, 0 }, int[5] { 0,  ... int[5] { 0, 0, 0, 0, 0 } }
jshell> for (int i = 0; i<5; i++) {
   ...>     for(int j = 0; j<5; j++) {
   ...>         matrix[i][j] = i * j;
   ...>     }
   ...> }
jshell> matrix
matrix ==> int[5][] {
    int[5] { 0, 0, 0, 0, 0 },
    int[5] { 0, 1, 2, 3, 4 },
    int[5] { 0, 2, 4, 6, 8 },
    int[5] { 0, 3, 6, 9, 12 },
    int[5] { 0, 4, 8, 12, 16 } }
```

Array Utility:
```java
jshell> import java.util.Arrays;
jshell> Arrays.sort(intArr)
jshell> intArr
intArr ==> int[10] { 0, 0, 0, 1, 1, 2, 5, 8, 9, 9 }
jshell> Arrays.binarySearch(intArr, 5)
$64 ==> 6

jshell> System.out.println(intArr)
[I@2d6eabae
jshell> System.out.println(Arrays.toString(intArr))
[0, 0, 0, 1, 1, 2, 5, 8, 9, 9]
jshell> System.out.println(Arrays.deepToString(matrix))
[[0, 0, 0, 0, 0], [0, 1, 2, 3, 4], [0, 2, 4, 6, 8], [0, 3, 6, 9, 12], [0, 4, 8, 12, 16]]
```

API Ref: [java.util.Arrays](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/Arrays.html)

- API是*应用编程接口 (英语 ：Application Programming Interface）*，描述了 Java 类库向外提供的功能、使用方法、注意事项等信息。
- JavaDoc 是内置的生成网站形式的 API 文档的方式，Java 标准库参考文档，也就是 [API Ref](https://docs.oracle.com/en/java/javase/17/docs/api/)就使用 JavaDoc 生成的。
- Java 标准库内容丰富，生态庞大，学会阅读、查找、遵循 API 文档是充分利用 Java 优势的第一步。



### String

```java
jshell> String s = "I Love Java"
s ==> "I Love Java"
jshell> s.length()
$37 ==> 11
jshell> s.split(" ")
$38 ==> String[3] { "I", "Love", "Java" }
jshell> s.concat(" and Rust")
$39 ==> "I Love Java and Rust"
jshell> s.contains("C++")
$40 ==> false
jshell> s.charAt(2)
$41 ==> 'L'
jshell> s.replace("Java", "C++")
$42 ==> "I Love C++"
jshell> s.toUpperCase()
$46 ==> "I LOVE JAVA"
jshell> s.toLowerCase()
$47 ==> "i love java"
jshell> s.startsWith("I")
$48 ==> true
jshell> s.endsWith("Java")
$49 ==> true
jshell> s
s ==> "I Love Java"
jshell> s == "I Love Java"
$43 ==> true
jshell> s == new String("I Love Java")
$44 ==> false
jshell> s.equals(new String("I Love Java"))
$45 ==> true
jshell> s.equalsIgnoreCase("i lOve jAVA")
$50 ==> true
```

API Ref: [java.lang.String](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/lang/String.html)



### Console IO
```java
jshell> System.out.println("I Love Java")
I Love Java

jshell> System.out.print("I Love Java")
I Love Java
jshell> System.out.printf("I Love %s\n", "Java")
I Love Java
$82 ==> java.io.PrintStream@4d95d2a2
jshell> import java.util.Scanner;
jshell> var input = new Scanner(System.in);
jshell> input.hasNextInt()
100
$86 ==> true
jshell> input.nextInt()
$87 ==> 100
jshell> input.hasNextBoolean()
100
$89 ==> false
```

API Ref: [java.util.Scanner](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/Scanner.html)



### BigInteger  

```java
jshell> import java.math.BigInteger;
jshell> 1000000000000000 + 1
|  错误:
|  整数太大
|  1000000000000000 + 1
|  ^
jshell> new BigInteger("1000000000000000").add(BigInteger.ONE)
$92 ==> 1000000000000001
jshell> new BigInteger("19260817").isProbablePrime(10)
$93 ==> true
```

API Ref: [java.math.BigInteger](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/math/BigInteger.html)



### BigDecimal

```java
jshell> import java.math.BigDecimal;

jshell> 0.1 + 0.2
$94 ==> 0.30000000000000004

jshell> new BigDecimal("0.1").add(new BigDecimal("0.2"))
$95 ==> 0.3

jshell> new BigDecimal("0.000000001").toEngineeringString()
$100 ==> "1E-9"
```

API Ref: [java.math.BigDecimal](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/math/BigDecimal.html)



### Math

```java
jshell> Math.
E                 IEEEremainder(    PI                abs(              absExact(
acos(             addExact(         asin(             atan(             atan2(
cbrt(             ceil(             class             copySign(         cos(
cosh(             decrementExact(   exp(              expm1(            floor(
floorDiv(         floorMod(         fma(              getExponent(      hypot(
incrementExact(   log(              log10(            log1p(            max(
min(              multiplyExact(    multiplyFull(     multiplyHigh(     negateExact(
nextAfter(        nextDown(         nextUp(           pow(              random()
rint(             round(            scalb(            signum(           sin(
sinh(             sqrt(             subtractExact(    tan(              tanh(
toDegrees(        toIntExact(       toRadians(        ulp(
```

```java
jshell> Math.sin(Math.PI / 4)
$103 ==> 0.7071067811865475
jshell> Math.floor(1.002)
$104 ==> 1.0
jshell> Math.log(Math.E)
$107 ==> 1.0
jshell> Math.pow(2, 1.5)
$108 ==> 2.82842712474619
jshell> Math.sqrt(2)
$109 ==> 1.4142135623730951
```

API Ref: [java.lang.Math](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/lang/Math.html)



### *CodeLab 1-1* a +-*/^&| b

[本CodeLab链接](https://github.com/xsun2001/tour-of-java/tree/master/codelab/chapter1)

- `codelab/chapter1/src/Lab1.java`
- 标准输入输出
- 输入格式：若干行`a op b`，其中`op`是`+-*/^&|`中的其中一种，以标准输入结束为终止
- 输出格式：若干行计算结果
- 提示：计算结果可能非常大

### *CodeLab 1-2* QuickSort

[本CodeLab链接](https://github.com/xsun2001/tour-of-java/tree/master/codelab/chapter1)

- `codelab/chapter1/src/Lab2.java`
- 实现`public static void quickSort(int[] arr)`
- 运行`main`方法会自动检测实现的正确性



## 资源链接

本章CodeLab：https://github.com/xsun2001/tour-of-java/tree/master/codelab/chapter1

- Java API Ref: https://docs.oracle.com/en/java/javase/17/docs/api/
	-  java.util.Arrays https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/Arrays.html
	- java.lang.String https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/lang/String.html
	- java.lang.Math https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/lang/Math.html
	-  java.util.Scannerhttps://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/Scanner.html
	-  java.math.BigIntegerhttps://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/math/BigInteger.html
	-  java.math.BigDecimalhttps://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/math/BigDecimal.html
	
	