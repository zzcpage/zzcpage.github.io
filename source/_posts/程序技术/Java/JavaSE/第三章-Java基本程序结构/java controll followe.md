---
title: 3. Java控制流程

categories: 
    - JavaSE
tags:
    - JAVA
    - JAVASE
---
程序的控制流程一般包含，选择结构，循环结构，顺序结构。

> 顺序结构
> >顺序结构很简单，程序都具有顺序结构，依次按顺序执行。
Java的语法，不允许在嵌套的两个块(块一般是指，通过{}包含的代码区域，称为块)中，声明同名变量。示例代码如下：
```java
public static void main(String[] args)
{
    int n;
    {
        int k;
        int n; // Error can't redefine n in inner block
    }
}
```
>选择结构
选择结构也就是 if-else结构，通过条件语句来选择需要执行的语句。else与最近的if构成匹配。示例代码如下：
```java
public static void ifElseTemplemate(){
        int a = 1 ,b = 2 ;
        if(a > b)
            System.out.println("a大于b");
        else
            if(a == b)
                System.out.println("a 等于 b");
            else
                System.out.println("a 小于 b");
}
```
如果有多个选择情况，可以通过Switch-case语句进行实现。代码如下:
```java
 public static void ifElseTemplemate(){
        int a = 1 ,b = 2 ;
        if(a > b)
            System.out.println("a大于b");
        else
            if(a == b)
                System.out.println("a 等于 b");
            else
                System.out.println("a 小于 b");
        switch (a){
            case 1:
                System.out.println(a);break ;
            case 2:
                System.out.println(a);
                break ;
            default:
                System.out.println(a);break ;
        }
    }
```
switch语句将从与选项值相匹配的case标签处开始执行直到遇到 break 语句，或者执行到switch i吾句的结束处为止。如果没有相匹配的case标签,而有default子句,就执行这个子句。
case 标签可以是：

    •类型为 char、byte、 short 或 int 的常量表达式。
    •枚举常量。
    •从 Java SE 7开始， case 标签还可以是字符串字面量。


>循环结构
循环执行某个代码块。一般包含for循环和while循环，do-while循环。示例代码如下：
```java
   public static void forWhileStatement(){
        // 计算1-100的和
        int sum = 0 , i = 0 ;
        for(i = 0 ; i <= 100 ; i++){
            sum +=i;
        }
        System.out.println(sum);
        i = 0 ;
        sum = 0 ;
        while(i<=100){
            sum+=i;
            i++;
        }
        System.out.println(sum);
        i = 0 ;
        sum = 0 ;
        do{
            sum+=i;
            i++;
        }while(i<=100) ;
        System.out.println(sum);
    }
```
还有一种循环方式为for-each循环，参考数组的循环。

如果想跳出循环可以使用break;，如果想要跳出某个执行过程可以用continue。Java也支持类似于goto的语法，通过break 标签来结束循环。示例代码如下：
```java
public static void forWhileStatement(){
        int   i = 0, j  ;
        lables:
        for(i = 0 ; i < 10 ; i++){
            for(j=0;j<10;j++){
                System.out.println("in");
                break lables;//跳出指定的循环
            }
        }
    }
```



