# scala



```
学习记录：
20241224：001-52 53开始看



跳过的部分[28,47]
```



scala基于JVM，与java兼容。

scala比java更加面向对象。

scala是一门函数式编程语言。

spark底层使用scala编写。

官网：https://www.scala-lang.org/

scala 2.12



[TOC]



## 1 scala入门

### 1.1 概述

#### 1.1.1 scala历史



#### 1.1.2 scala和java之间的关系

Scala和Java及JVM之间的关系如下图：

![](.\scala_image\s3.png)

通过scalac编译scala文件生成两个字节码文件

![](.\scala_image\s4.png)



```
其他对比区别请参考：
https://blog.csdn.net/weixin_45428910/article/details/135497800?ops_request_misc=&request_id=&biz_id=102&utm_term=scala%20java%20jvm&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduweb~default-3-135497800.142^v100^pc_search_result_base3&spm=1018.2226.3001.4187

```



#### 1.1.3 scala语言特点

面向对象+函数式编程 静态类型编程语言



### 1.2 scala环境搭建



```
windows下安装步骤：
	1.确保jdk安装完
	2.下载对应scala文件 ： https://www.scala-lang.org/download/all.html
	3.配置scala环境变量
	
可以参考以下教程：
https://blog.csdn.net/qq_45956730/article/details/130259056?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522b5f73f3e664377466a341e162007603a%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=b5f73f3e664377466a341e162007603a&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~top_positive~default-1-130259056-null-null.142^v100^pc_search_result_base3&utm_term=scala%E5%AE%89%E8%A3%85%E6%95%99%E7%A8%8B&spm=1018.2226.3001.4187

验证： cmd中scala
C:\Users\auserwn>scala
Welcome to Scala 2.12.15 (Java HotSpot(TM) 64-Bit Server VM, Java 1.8.0_261).
Type in expressions for evaluation. Or try :help.


```



### 1.3 scala插件安装

IDEA中settings-plugins 增加Scala插件，创建scala项目

### 1.4 hello scala 

> 简单说明hello scala代码

```scala
package scala
/*
  Object:关键字，声明一个单例对象。这个对象在JVM加载时自动实例化，并且其生命周期与JVM相同。由于它是单例的，因此在整个程序中只能有一个该对象的实例。
  专业的话叫：伴生对象  和同名的类是伴生关系
 */
object HelloWord {

  /*
    main方法：从外部可以直接调用执行。
    def 方法名称(参数名称：参数类型):返回值类型 = {
        ...
      }
   */
  def main(args: Array[String]): Unit = {
    println("hello scala")

    System.out.print("hello scala from java")
  }
}

/* 
	上述代码中即可以使用scala代码也可以融合java代码
	比java少了很多关键字 public static等等
*/

```



以下为java和scala的对比

```scala
/*
	下边代码解释了伴生对象和伴生了，其实就是为了替代java中的static关键字
	下述代码对应java的实现其实是：有一个java类，有属性name age 和static school = 'dlut'，那么创建对象时，引用类.school 
	但是scala为了更好的面向对象，认为类.属性不符合面向对象理念
*/

package scala

class Student(name:String ,age:Int){

  def printInfo():Unit = {
    print(name + " " + age  + " " + Student.school)
  }
}

object Student{
  val school:String = "dlut"

  def main(args: Array[String]): Unit = {
    val kasha = new Student("Kasha", 20)
    kasha.printInfo()
  }
}

```

上边的代码在java中是这么做的：

```
package java;

public class Student {
    private String name;
    private Integer age;
    private static String school;

    public Student(String name, Integer age) {
        this.name = name;
        this.age = age;
    }
    
    public void printInfo(){
        System.out.println(this.name + " " + this.age + " " + Student.school);
    }
}

```



![](.\scala_image\s5.png)

认真反思对比 为什么出现伴生对象伴生类，其实是为了替代java中的static关键字

伴生对象 和 伴生类必须名称一样，而且放在一个文件当中。

编译后伴生类会在Student.class入口类文件中，伴生对象在Student$.class文件中。



### 1.5 源码学习

推荐查看：

```
在线：https://docs.scala-lang.org/zh-cn/
源码：下载scala-docs-2.11.8.zip
https://docs.scala-lang.org/overviews/scala-book/scala-features.html
```





## 2.变量和数据类型

### 2.1 注释

> scala注释和java完全一样

```scala
// 单行注释

/*
	多行注释
*/

/**
 *	文档注释 通常用来方法或者类进行注释
 */
```



### 2.2 常量和变量

```scala
Java中的变量和常量用法是：
int a = 10;
final int b = 20;

scala中的定义方式：
var i:Int = 10;
val j:Int = 20;
var/val 变量名[:变量类型] = 初始值
scala会对变量常量进行自动类型推算，有时可以省略。

注意，能用常量的地方不用变量，尽量使用常量。
```

代码示例如下：

```scala
package scala.chapter02

object Test02_Variable {
  def main(args: Array[String]): Unit = {

    // 1.声明变量时，类型可以省略，编译器自动推导，即类型推导
    var a1: Int = 10
    var a2 = 20
    print(a1)

    // 2.类型确定后，就不能修改，说明scala是强数据类型语言
    var b = 15
    //var b = "aaa" 这里是不行的，上边已经确认是int类型了

    // 3.变量声明时，必须有初值
    // var c: Int 是会报错的

    // 4.在定义一个变量时，可以使用var或者val来修饰

    val kasha = new Student("Kasha", 20)
//    kasha = new Student("kasha", 21) val修饰不可变
    kasha.age = 21 // 这里注意Student对应的定义：class Student(name:String ,var age:Int)
    var yasuo = new Student("Yasuo",19)
    yasuo = new Student("Yasuo2",15)
  }
}

```



### 2.3 标识符的命名规范

> 标识符：对各种变量、方法、函数等命名时使用的字符序列。



标识符的命名规则：

- 以字母或者下划线开头，后接字母、数字、下划线
- 以操作符开头，且只包含操作符
- 用反引号··包括的任意字符串，即使是scala关键字也可以

```scala
package scala.chapter02

object Test03_Identifier {

  def main(args: Array[String]): Unit = {

    val `main` = 20
    val ++ = 10
    val __aaa = 19
    val +-*/ = "test"
  }

}

```



### 2.4 字符串

基本语法：

- 连接：字符串+字符串
- printf用法：字符串通过%传值
- 字符串模板/插值字符串：通过$获取变量值

示例代码如下：

```scala
package scala.chapter02

object Test04_String {

  def main(args: Array[String]): Unit = {

    val s1 = "hello, "
    val s2 = "scala"
    val s3 = s1 + s2
    println(s3)
    printf("%s %s\n",s1,s2)
    println(s"${s1} ${s2}")

    val num: Double = 3.141592
    // f 代表格式化模板字符串
    printf(s"the num is : ${num}\n")
    println(f"the num is : ${num}%2.2f")

    // 三引号表示字符串，保持多行字符串的原格式输出 和py类似
    val lines =
      """
        |select *
        |from a
        |where a.num = 1;
        |""".stripMargin
    println(lines)
  }

}

```



### 2.6 数据类型

Java基本类型：char byte short int long float double boolean 

基本类型对应的包装类：Character Byte Short Integer Long Float Double Boolean

java引用类型：对象类型

由于java有基本类型，而且基本类型不是真正意义的对象，所以认为java不是真正意义的面向对象。scala考虑这个，scala中一切数据都是对象，都是Any的子类。

scala中数据分为两大类：数值类型AnyVal和引用类型AnyRef 不管是值类型还是引用类型都是对象。

scala数据仍然遵守 低精度的值向高精度的值类型自动转换，隐式转换。

scala中的StringOps是对java中的String的增强。

Unit：对应java中的void，用于方法返回值的位置，表示方法没有返回值。def main(args: Array[String]): Unit = {...}。Unit是一个数据类型，只有一个对象就是()。Void不是数据类型，只是一个关键字。

Null是一个类型，只有一个对象就是null。他是所有引用类型AnyRef的子类。

Nothing是所有数据类型的子类，主要用在一个函数没有明确返回值时使用。



![](.\scala_image\s6.png)



#### 2.6.1 整数类型



#### 2.6.2 浮点类型



#### 2.6.3 字符类型



#### 2.6.4 布尔类型



#### 2.6.5 Unit类型、Null类型、Nothing类型

| 数据类型 | 描述                                                         |
| -------- | ------------------------------------------------------------ |
| Unit     | 表示无值，和其他语言中的void等同。用作不返回任何结果的方法的结果类型。Unit只有一个实例值，写成()。 |
| Null     | Null类型只有一个实例值。写成()                               |
| Nothing  | 在scala的类层级的最低端，是任何其他类型的子类型。            |

示例代码:

```scala
package scala.chapter02

object Test07_DataType {

  def main(args: Array[String]): Unit = {

    // Unit
    def testMethod(i:Int):Unit = {
      println("Method testMethod:" + i)
    }

    val res = testMethod(99)
    println(res)

    // Null
    var kasha = new Student("Kasha", 12)
    kasha = null
    println(kasha)

    // Nothing
    def m2(i:Int) :Int = {
      if (i==0)
        throw new NullPointerException
      else
        i
    }
    val a = m2(0)
    val b = m2(2)
    println(a)
    println(b)
  }
}

```



### 2.7 类型转换

#### 2.7.1 数值类型自动转换



#### 2.7.2 强制类型转换

> 精度大->精度小，可能造成精度降低或者溢出

示例代码如下：

```scala
package scala.chapter02
object Test08_DataTypeConversion {
  def main(args: Array[String]): Unit = {
    val d:Double = 1.235
    val i:Int = 2.9.toInt
    val f:Float = d.toFloat
    println(i)
    println(f)
  }

}

```



#### 2.7.3 数值类型和String类型的转换

```scala
val n:Int = 27
val s:String = n + ""
val s2:String = n.toString
val t:Int = s.toInt
val f2:Int = "12.3".toDouble.toInt
```



3 运算符 

4 流程控制





## 5 函数式编程



### 5.1 函数基础

```scala
def sum(x: Int ,y: Int):Int = {
    x+y
}
```

![](.\scala_image\s7.png)

类中的函数称为方法。

函数没有重载和重写的概念，方法可以进行重载和重写。

scala中函数可以嵌套定义。



```scala
package scala.chapter05

object Test01_FunctionAndMethon {

  def main(args: Array[String]): Unit = {

    def sayHi(name:String): Unit = {
      println("hi1, " + name)
    }

    sayHi("Kasha")
    Test01_FunctionAndMethon.sayHi("Yasuo")
  }

  def sayHi(name:String): Unit = {
    println("hi2, " + name)
  }
}

```



#### 5.1.4 函数参数

要考虑情况：

- 可变参数
- 如果参数列表存在多个参数，那么可变参数放在最后
- 参数默认值，一般将有默认值的参数放置在参数列表后面
- 带名参数

示例代码如下：

```scala
package scala.chapter05

object Test03_FunctionParameter {

  def main(args: Array[String]): Unit = {

    // 1.可变参数
    def testFun_1(str : String*): Unit = {
      println(str)
    }
    testFun_1("aaa","kasha")
    // 输出：WrappedArray(aaa, kasha)
    testFun_1("yasuo")
    // 输出：WrappedArray(yasuo)


    // 2.多个参数
    def testFun_2(str1: String, str2: String*): Unit = {
      println(str2)
    }
    testFun_2("aaa")
    // 输出：WrappedArray()
    testFun_2("aaa","kasha")
    // 输出：WrappedArray(kasha)
    testFun_2("aaa","kasha", "ryze")
    // 输出：WrappedArray(kasha, ryze)

    // 3.默认参数
    def testFun_3(str1: String = "hello"): Unit = {
      println(str1)
    }

    // 4.带名参数
    def testFun_4(name:String ,age:Int): Unit = {
      println(s"name is:${name},age is:${age}")
    }
    testFun_4(age = 18,name = "Ryze")
  }

}

```



#### 5.1.5 至简原则

如下：

- 如果函数体只有一行代码，则可以省略花括号：testFun_5

- 返回值类型如果能推断，可以省略返回值类型
- 匿名函数/Lambda表达式

```scala
def testFun_5(name: String): String = name
```



### 5.2 函数高级

#### 5.2.1 匿名函数

应用场景如下

```scala
package scala.chapter05

object Test05_Lambda {

  def main(args: Array[String]): Unit = {

    val fun = (name:String) => { println(name) }
    fun("Ryze")

    // 函数作为参数
    def f(func : String => Unit) : Unit = {
      func("atguigu")
    }

    f(fun)
  }
}

// func : String => Unit 这段代码相当于声明func为一个函数类型

```

匿名函数的作用呢？可以定义一个函数，以函数为参数输入

匿名函数的至简原则：

- 参数类型可以省略，会根据形参进行自动推导
- 类型省略后，如果只有一个参数，则圆括号也可以省略
- 匿名函数如果只有一行，则大括号也可以省略
- 如果参数只出现一次，则参数省略且后面参数可以用_代替



6 面向对象

7 集合

8 模式匹配

9 异常

10 隐式转换

11 泛型

12 总结