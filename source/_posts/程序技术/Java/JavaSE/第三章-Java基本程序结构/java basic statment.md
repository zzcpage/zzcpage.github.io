---
title: 2. Java基本语法

categories: 
    - JavaSE
tags:
    - JAVA
    - JAVASE
---
## [1. Java注释](#one)

## [2. Java数据类型](#two)

## [3. Java变量](#three)

## [4. Java运算符](#four)

## [5. Java字符串](#five)

## [6. Java输入流](#six)

## [7. Java数组](#seven)

## [8. 大数值](#eight)

一般我们在学习一门语言的时候，总是从hello world入门，所以我们也是从hello world开始入门。
```java
public class FirstSample
{
    public static void main(String[] args)
    {
        System.out.println("Hello World!");
    }
}
```
如果你想运行一个java程序，那么需要在类里面加上main方法，作为程序的入口让程序运行起来。格式如下。
```java
public class ClassName
{
    public static void main(String口 args)
    {
        program statements
    }
}
```
### 1.<a id="one"></a> 注释

注释一般用于书写一些关于程序或者变量，算法的说明。用于辅助他人阅读你的程序代码，增加代码的可读性。

Java的注释可以分成三种注释。
>1. 以双反斜杠标记的注释 //

&nbsp;&nbsp;&nbsp;&nbsp;如下代码所示，通过//注释对变量进行解释说明。
```java
public class ClassName
{
    public static void main(String口 args)
    {
       int i , j ; // i和j为临时变量
    }
}
```
>2, 以/* TO-DO */,将一段内容进行注释。

&nbsp;&nbsp;&nbsp;&nbsp;这个注释和//注释的区别在于。//是单行注释，而/*....*/可以表示多行注释。示例如下。

```java
public class ClassName
{
    public static void main(String口 args)
    {
       /*
            这是多行注释。
            可以注释多行。
       */
       int i , j ; 
    }
}
```

>3. 第三种是/** */注释，称为文档注释，用于生成程序说明文档。一般在类上或者方法上进行注释。

&nbsp;&nbsp;&nbsp;&nbsp;示例如下：通过文档注释，标明了类创建的时间。关于文档注释的其它声明，可以查阅相关资料。
```java
/**
 * @date: 2021-10-26 19:44
 **/
public class Java基本数据类型 {
    public static void main(String args[]) {
        System.out.println("hello world");
        // 如果想要程序在执行中间退出，则通过System.exit()退出程序
        System.exit(1);
        System.out.println("程序中止返回");
    }
}
```

### 2. <a id="two"></a>数据类型

Java是一种强类型语言，Java的变量在使用前，都需要进行变量的声明。Java有8种基本数据类型。分别为4种整型，2种浮点型，1种字符型，1种布尔型(boolean).
>1, 整型

整型也就是常说的整数。用于表示数据为整数。

| 类型  | 字长  |      取值      |
| :---: | :---: | :------------: |
| byte  |   1   |    -128-127    |
| short |   2   | -2^15-(2^15-1) |
|  int  |   4   | -2^31-(2^31-1) |
| long  |   8   | -2^63-(2^63-1) |



示例代码如下：
```java
 public static void basicType(){
        byte a ;
        short b ;
        int c;
        long d  ;
        d = 10000000L ; //通过L表示为长整型数据
        c = 0x123 ; //通过0x表示为十六进制
        c = 0b1001 ; //通过0b表示二进制数据
        c = 010 ; //通过0开头，表示为八进制数据。
    } 

```
>2. 浮点类型

浮点类型的数据也就是我们常说的小数。

|  类型  | 字长  |                          取值                           |
| :----: | :---: | :-----------------------------------------------------: |
| float  |   4   |     大约 ± 3.402 823 47E+38F (有效位数为 6 ~ 7 位）     |
| double |   8   | 大约 ± 1.797 693 134 862 315 70E+308 (有效位数为 15 位) |

double 表示这种类型的数值精度是 float 类型的两倍（有人称之为双精度数值)。绝大部分应用程序都采用 double 类型。float 类型的数值有一个后缀 F 或 f (例如，3.14F。) 没有后缀 F 的浮点数值（如 3.14 ) 默认为double 类型。当然，也可以在浮点数值后面添加后缀 D 或 d (例如，3.14D)
。

>3. 字符类型
字符类型char采用unicode编码格式。char 类型的字面量值要用单引号括起来。, char 类型的值可以表示为十六进制值，其范围从 \u0000 到 \Uffff。例如：W2122 表示注册符号 ( ), \u03C0 表示希腊字母 it。在Java中，char类型占用两个字节（采用16位的Unicode字符集）。示例代码如下，用于输出显示两个特殊的字符。
```java
  public static void basicType(){
        char g = '\u2122'; //注册符号 Tm
        System.out.println(g);
        g = '\u03C0' ;
        System.out.println(g);

    }
```
>4. Boolean类型
boolean(布尔)类型一般有两个值:true和false,用来判定逻辑条件。在Java中整型不能替代布尔类型(这在C/c++却是可以的。)

对于数据类型，我们可以在不同的数据类型之间进行转化(强制类型转换)。

### 3. <a id="three"></a> 变量
变量的声明在前面数据类型也可以看到。一般格式为：数据类型  变量名; 
>变量名的命名格式

变量名必须是一个以字母开头并由字母或数字构成的序列。需要注意，与大多数程序设
计语言相比，Java 中“ 字母” 和“ 数字” 的范围更大。字母包括“A’~Z”、“a~z”、或在某种语言中表示字母的任何 Unicode 字符。

>变量的初始化和赋值操作

初始化一般是在变量的声明时候，给变量一个初始值(这是一个好的编程习惯)。变量的赋值一般通过=对变量进行赋值操作。示例代码如下所示。
```java
  public static void bytes(){
        int a = 30 , b ; //对变量进行初始化
        b = 20 ; //对变量进行赋值
    }
```

>常量

常量也就是一直不变的量，只能被赋值一次。一般我们通过final来指示常量(一般用纯大写作为变量名，下划线进行分割)。示例代码如下:
```java
    public static void bytes(){
        int a = 30 , b ; //对变量进行初始化
        b = 20 ; //对变量进行赋值
        final  double PI = 3.1415962 ; 
    }
```
对于类常量，一般是作为类的成员变量，并且访问修饰符为static。可以理解为，加了sttic外部就能直接引用该变量。
```java
public class test{
     public static final double PI = 3.1415926 ; 
}
```

### 4. <a id="four"></a>运算符

运算符一般就是加减乘除这些运算符，以及一些特殊操作。+,-,*./,%(取余)。

对于取余有一个技巧，保证不取负数。通过（(position +
adjustment) % 12 + 12) % 12。公式进行取余操作，就保证了余数在0-11之间，不出现负数的情况。

还有一些运算符属于双目运算符，如 +=,-=,*=...。这写运算符和前面的运算符类似只不过，多了赋值操作。a += b; 等价于 a = a+b 。可以编程进行验证。

自增运算符：++和--, x++也就等价于 x += 1; 

关系运算符：主要是用于比较数值的大小，如>=,<=,>,<，!= 等关系运算。简单的例子就是：a>b ,比较a和b的大小。


逻辑运算符：也就是与，或，非三个运算。&& 表示与， || 表示或，！表示非。一般用于条件表达式表示多条件的情况。如表示 a> b 且 a> c的情况,可以写成 a>b && a>c 

三元操作符?: 用法 a>b?a:b 。如果a>b则执行a,反之执行b

位运算符：&,|,^（异或）,~(非)。一般用于对整数各个位(二进制)进行位运算。>>和<<运算符，用于将数据左移和右移。需要建立位模式来完成位掩码时， 这两个运算符会很方便。>>>会用0填充高位，而>>则是用符号位填充高位。移位运算符的右操作数要完成模 32 的运算（除非左操作数是 long 类型， 在这种情况下需要对右操作數模 64 )。例如， 1 «35 的值等同于 1 «3 或 8。也就是最多不会超过int类型，如果超过那么就等同于模32后再右移动。

>运算符优先级
运算符优先级也就是运算符的优先执行顺序。如果不确定优先级，我们可以用括号包含起来。
运算符优先级 单目运算(!,~,++,new,强制类型转换) > (*，/,%) > (+,-) > (<<, >> , >>>) > (< , >  , >= , instanceof) > (==,!=) > (&) > (^) >(|) > (&&) > (||) > (?) > (=,+=,-=......)。

>枚举类型 
通过enum 进行枚举类型的声明，包括有限个命名的值。示例代码如下:
```java
public enum 枚举类型 {
    SMALL(1,"小号"),
    MEDIUM(2,"中号"),;

    private int code ;
    private String message ;
    枚举类型(int code , String message){
        this.code = code ;
        this.message = message ;
    }

    public static int getCode(String define){
        // 返回枚举类型的code,根据Message
        return 枚举类型.valueOf(define).code ;
    }

    public static String getMessage(String define){
        return 枚举类型.valueOf(define).message ;
    }
}
```

### 5. <a id="five"></a>字符串
字符串也就是一串字符，通常用""包含。一般我们使用String表示字符类型。示例: String str = "hello world" ; , 字串则是字符串的一串连续的字符，如"worl"就是str的一个字串。如果需要拼接字符，可以通过+号进行拼接。如果需要拼接多个，可以通过定界符(，)分割，然后用静态join方法拼接。如:String str = String.join("h","ello","w","rold") ; 

Java如果要修改某个字符，一般需要创建新的字符常量对象。Java的String类似于指针，指向字串常量在存储池（堆）的相应位置。如果指向相同的字符常量， 则这两个变量是相同的。可以运行如下代码:
```java
public static void byteString(){
        String a = "123";
        String b = "123" ;
        String c = new String("123") ;
        System.out.println(a == b);
        System.out.println(a.equals(b));
        System.out.println(a == c);
        System.out.println(a.equals(c));
}
```
上面的代码中两个变量 a == b 比较的是两个变量是否相同(类比于比较地址是否相同)，a.equeals(b)检测的是两个字符串是否相等。
检查一个字符串既不是 null 也不为空串，这种情况下就需要使用以下条件：if (str != null && str.length()!= 0),先检测变量是否为空，在判断长度是否为空。

>StringBuffer和StringBuilder的区别

StringBuilder支持单线程，StringBuffer支持多线程。就效率而言,StringBuilder快于StringBuffer，所以在单线程程序中采用StringBuilder较好，多线程程序采用StringBuffer。

### 6. <a id="six"></a>输入输出流

java输入输出流，通过Scanner对象和标准输入流（System.in）相关联进行获取。
```java
 public static void ioStream(){
        // Java构建输入流，通过Scanner对象和标准输入流Sytem.in对象关联。
        Scanner in = new Scanner(System.in) ;

        System.out.print("输入字符：");

        String text = in.nextLine() ;

        System.out.println(text);

}
```
因为输入是可见的， 所以 Scanner 类不适用于从控制台读取密码。Java SE 6 特别
引入了 Console 类实现这个目的。要想读取一个密码， 可以采用下列代码：

    Console cons = System.console();

    String username = cons.readLine("User name: ")；

    char [] passwd = cons.readPassword("Password:");

为了安全起见， 返回的密码存放在一维字符数组中， 而不是字符串中。在对密码进
行处理之后，应该马上用一个填充值覆盖数组元素（数组处理将在 3.10 节介绍）。
采用 Console 对象处理输入不如采用 Scanner 方便。每次只能读取一行输入， 而没有
能够读取一个单词或一个数值的方法。

>格式化输出
最简单的方法是采用C语言的方法进行输出System.printf就可以通过C语言的printf输出方式进行输出。

>文件输入与输出
通过File对象构建Scanner对象进行文件的读取，通过PrintWriter进行文件的输出。示例代码如下所：
```java
public static void fileIO() throws IOException {
        //文件输入流 , get路径为文件路径
        Scanner in = new Scanner(Paths.get("javase/src/main/java/Java核心技术/第三章Java的基本程序设计/myfile.txt"),"UTF-8") ;
        while(in.hasNext()){
            System.out.println(in.next());
        }
        in.close();
        System.out.println();
        in = new Scanner(new File("javase/src/main/java/Java核心技术/第三章Java的基本程序设计/myfile.txt")) ;
        while(in.hasNextLine()){
            System.out.println(in.nextLine());
        }
        in.close();

        //文件输出流，路径填写文件路径
        PrintWriter out = new PrintWriter(new File("javase/src/main/java/Java核心技术/第三章Java的基本程序设计/myfile.txt") ) ;
        out.write("this is a test two");
        out.close();
    }
```

### 7.<a id="seven"></a>数组
数组存储了一串连续地址的元素，通过元素下标可以访问到数组元素。数组有如下几种声明方式。

    int[] a ;
    int[] a = new int[100] ; 
    int a[] ; 
    int[] a = {1,2,3,4,5} ; 
    new int[] {17,19,23,29} ; //匿名数组

>数组的迭代方法

>1. for，while循环
通过遍历数组下标来遍历数组的元素
>2. for-each循环
通过for-each遍历数组的每一个元素。for-each的一般形式为：for (variable : collection) statement。如果要遍历集合，那么对应的集合必须是数组或者实现了Iterable接口的类对象。

```java
public static void array(){
        int i ;
        int[] a = {1,2,3,4,5} ;
        //for循环遍历
        for(i = 0 ; i < a.length ; i++){
            System.out.println(a[i]);
        }
        //for-each循环
        for(int temp : a){
            System.out.println(temp);
        }
}
```

>打印数组
通过Arrays类的toString方法，可以打印一个数组。
```java
 public static void array(){
        int i ;
        int[] a = {1,2,3,4,5} ;
        //for循环遍历
        for(i = 0 ; i < a.length ; i++){
            System.out.println(a[i]);
        }
        //for-each循环
        for(int temp : a){
            System.out.println(temp);
        }
        //打印数组
        System.out.println(Arrays.toString(a) );;
    }
```
>数组拷贝
简单的数组赋值，只是浅拷贝，二者指向的还是一个堆栈空间。如果需要进行数值拷贝，则通过Arrays.copyOf进行数据的拷贝。
```java
    int[] a = {1,2,3,4} ; 
    int[] b ; 
    b = a ; //这是简单的赋值，浅拷贝
    b[1] = 10 ;// 则a[1]也变为10 
    b = Arrays.copyOf(a,a.length) ; //
    b[1] = 2 ; //则a[1] 还是为10并不影响。
```
Arrays包含许多关于数组的操作,包括数组的打印，数组的拷贝，排序等功能。

>数组排序
通过Arrays.sort（a），可以对数组a进行排序（无返回值）。该排序方法使用了优化的快速排序算法(可以阅读源码，理解排序方法)。

>数组填充
通过Arrays.fill（a,value），用数据填充数组。

>多维数组
多维数组的初始化方法：
    double[][] balances ;
    balances = new double[NYEARS] [NRATES];
    int[][] magicSquare =
            {
                {16, 3, 2, 13}， {5, 10, 11, 8},
                (9, 6, 7, 12},
                {4, 15, 14, 1}
            };
>多维数组的迭代
for循环迭代，只需要通过下标进行迭代即可。
for-each迭代格式如下：

    for (doubleG row : a)
        for (double value : row)
            do something with value        
>多维数组的打印
通过Arrays.deepToString进行多为数组的打印。

>不规则的数组
不规则数组一般是多维的维数可能不一致。如果需要创建多维数组，必须先分配具有所含行数的数组，然后再分配行数组的长度。
### 8. <a id="eight"></a>大数值
如果基本的整数和浮点数精度不能够满足需求， 那么可以使用jaVa.math 包中的两个
很有用的类：Biglnteger 和 BigDecimaL 这两个类可以处理包含任意长度数字序列的数值。
Biglnteger 类实现了任意精度的整数运算， BigDecimal 实现了任意精度的浮点数运算。
```java
public static void bigData(){
        //通过BigInter和BigDecimal可以实现大数的运算,需要将数据转化为大数对象，再进行运算。
        BigInteger a = BigInteger.valueOf(1) ;
        a = a.add(BigInteger.valueOf(Long.MAX_VALUE));
        System.out.println(a.toString());
}
```





