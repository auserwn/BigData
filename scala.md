# scala



```
学习记录：
20241224：001-57 58开始看
20241226： -74 75开始看


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

![](scala_image\s3.png)

通过scalac编译scala文件生成两个字节码文件

![](scala_image\s4.png)



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



![](scala_image\s5.png)

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



![](scala_image\s6.png)



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

![](scala_image\s7.png)

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

#### 5.2.1 高阶函数

在scala中，函数是一等公民。可以定义、调用函数。

函数可以作为 **值/参数/返回值** 传递[看下边]



以下代码为函数作为**值**进行传递：

```
package scala.chapter05

object Test06_HighOrderFunction {

  def main(args: Array[String]): Unit = {

    def f(n:Int):Int={
      println("f调用")
      n + 1
    }

    val res = f(3)
    println(res)

    // 函数作为值进行传递
    val f1 = f _ //这种表示，f _ 代表函数整体，而不是调用函数
    val f2: Int=>Int = f //这里效果同上
    println(f2(3))
    println(f1)
    println(f2)
  }
}

```

以下代码为函数作为**参数**进行传递：

```scala
package scala.chapter05

object Test06_HighOrderFunction2 {

  def main(args: Array[String]): Unit = {

    def dualEval(op:(Int, Int)=>Int,a:Int, b:Int):Int = {
      op(a,b)
    }

    def op_add(a:Int, b:Int):Int = {
      a + b
    }

    print(dualEval(op_add,4,6))
    // 注意这里不是print(dualEval(op_add(),4,6))会报错
    val op_add_2 = op_add _
    print(dualEval(op_add_2,4,6))
  }
}

```

以下代码为函数作为函数的**返回值**进行传递：

```scala
package scala.chapter05

object Test06_HighOrderFunction3 {

  def main(args: Array[String]): Unit = {

    def f5(): Int=>Unit = {
      def f6(a:Int):Unit = {
        println("function f6 called, return :" + a)
      }
      f6
      // 或者写 f6 _
    }
    println(f5())
    f5()(7)
  }
}

```



```scala
package scala.chapter05

object Test07_Practice_CollectionOperation {

  def main(args: Array[String]): Unit = {

    // 对数组进行处理，将操作抽象出来，处理完毕之后的结果返回一个新的数组

    def arrayOp(array:Array[Int],op:Int => Int):Array[Int] = {
      for (elem <- array) yield  op(elem)
    }

    val arr:Array[Int] = Array(10,20,30)
    def addOne(elem:Int):Int = {
      elem+1
    }

    val res = arrayOp(arr, addOne)
    println(res.mkString(","))

    val res2 = arrayOp(arr,_ *2)
    println(res2.mkString(","))
  }
}

```





#### 5.2.2 匿名函数

定义：没有名字的函数就是匿名函数。

定义格式如下：`(x:Int)=>{函数体}`

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



上边代码通过至简原则可以简化为：

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
    //直接替换fun
    f((name:String) => { println(name) })
    // 至简原则后
    //  1.其实前边已经定义好这个参数类型，可以省略
    f((name) => { println(name) })
    //  2.只有一个参数，括号可以省略
    f(name => { println(name) })
    //  3.匿名函数只有一行，大括号可以省略
    f(name => println(name))
    //  4.如果参数只出现一次，则参数省略且后面参数可以使用_代替
    f(println(_))
    //  5.如果可以推断出当前传入的println是一个函数体，而不是调用函数，则可以直接省略下划线
    f(println)


    //  实例
    def FunctionOneAndTwo(fun:(Int,Int)=>Int):Int = {
      fun(1,2)
    }

    val f_add = (a:Int,b:Int) => a+b
    val f_minus = (a:Int,b:Int) => a-b

    println(FunctionOneAndTwo(f_add))
    // 先省略参数类型 然后参数只出现一次，下划线替代
    println(FunctionOneAndTwo((a,b) => a+b))
    println(FunctionOneAndTwo( _ - _ )) // 这里是a-b b-a呢？
    println(FunctionOneAndTwo( -_ + _ )) // 这里是-a+b = b-a
  }
}

```



#### 5.2.4 函数的柯里化&闭包

闭包：函数式编程的标配

闭包：如果一个函数，访问到了它的外部(局部)变量的值，那么这个函数和他所处的环境，称为闭包

函数柯里化：把一个参数列表的多个参数，变成多个参数列表。

请查看下边代码：

```scala
package scala.chapter05

object Test08_Practice2 {

  def main(args: Array[String]): Unit = {

    def fun(i:Int):String=>(Char=>Boolean) = {
      def f1(s:String):Char=>Boolean = {
        def f2(c:Char):Boolean = {
          if(i==0 && s == "" && c == '0') false else true
        }
        f2
      }
      f1
    }
    println(fun(0)("")('0'))
  }
}
```



函数调用在栈stack内存

对象实例的保存在堆heap内存，对象实例把相关的依赖的外部环境和局部变量保存在对象实例中，这就是闭包。所以不会导致依赖的外部环境丢失的情况。

以下为**闭包**的应用示例代码：

```scala
package scala.chapter05

object Test09_ClosureAndCurrying {

  def main(args: Array[String]): Unit = {

    def add(a:Int, b:Int):Int = {
      a + b
    }

    // 1.考虑固定一个加数的场景 4+b
    def addByFour(b:Int): Int = {
      4 + b
    }
    // 2.扩展固定加数改变的情况

    // 3.将固定的加数作为另一个参数传入，但是作为第一层传入 add()()
    def addByFour1():Int=>Int = {
      val a = 4
      def addB(b:Int):Int = {
      a + b
      }
      addB
    }

    def addBya(a:Int): Int => Int = {
      def addB(b: Int): Int = {
        a + b
      }
      addB
    }

    // 以上两个就是闭包，内层函数用到了外部的局部变量/参数
    println(addBya(11)(99))

    // addBya转化为lambda表达式
    def addBya1(a:Int):Int=>Int = {
      (b:Int) =>{a+b}
    }
    // 继续至简
    def addBya2(a: Int): Int => Int = {
      b => a + b
    }
  }
}
```

以下为**柯里化**的应用示例代码：

如果用到了柯里化表达，底层一定是用到了闭包

```scala
package scala.chapter05

object Test09_addCurrying {

  def main(args: Array[String]): Unit = {
    
    def addCurrying(a:Int)(b:Int): Int = {
      a + b
    }
  }
}
```



#### 5.2.5 递归

> 注意一定要有跳出的逻辑
>
> scala中递归必须声明函数返回值类型

经典案例阶乘代码如下：

```
package scala.chapter05

object Test10_digui_jiecheng {

  def main(args: Array[String]): Unit = {

    def digui(n:Int):Int = {
      if (n<0)
        return -1
      if (n == 0)
        1
      else
        digui(n-1)*n
    }

    print(digui(4))
  }
}

```

**递归的缺点**：StackWorkFlow 耗费更多的栈空间资源。

**尾递归**：节省资源

尾递归示例代码如下：

```scala
package scala.chapter05

import scala.annotation.tailrec

object Test10_TailRecursion {

  def main(args: Array[String]): Unit = {
    
    def tailFact(n:Int):Int = {
      
      @tailrec
      def loop(n:Int,currRes:Int):Int = {
        if (n == 0)
          return currRes
        else
          loop(n-1,currRes*n)
      }
      loop(n,1)
    }

    println(tailFact(4))
  }
}

// 尾递归代码使用@tailrec注解，可以保证为尾递归。
```



#### 5.2.6 控制抽象

主要是针对函数参数而言

```scala
package scala.chapter05

object TestControl {

  def main(args: Array[String]): Unit = {


  }

  // 这里是值调用，把计算后的值传递过去  - 传值参数
  def f0(a:Int):Unit = {
    println(a)
  }

  def f1(a:Int):Int = {
    println(a)
    a
  }
  // 名调用，把代码传递过去 - 传名参数
  // a: =>Int 注意这里 =>Int 表示代码块，没有输出，返回值为int
  // 这里传递不是具体的值而是代码块
  def f2(a: =>Int):Unit = {
    println("a is :" + a)
  }

  f2(23)
  f2(f1(10))
}

```

实现while循环的示例代码如下：

```scala
package scala.chapter05

object Test12_MyWhile {

  def main(args: Array[String]): Unit = {

    // 用闭包实现一个函数，将代码块作为参数传入，递归调用
    def myWhile(condition: =>Boolean):(=>Unit)=>Unit = {

      def doLoop(op: => Unit):Unit = {
        if (condition){
          op
          myWhile(condition)(op)
        }
      }
      doLoop _
    }
  }
}

```



#### 5.2.7 惰性加载 -lazy

当函数返回值被声明为lazy时，函数的执行将被推迟，知道我们首次对此取值，该函数才会执行，我们称之为惰性函数。

示例代码如下：

```scala
package scala.chapter05

object Test13_LazyFun {

  def main(args: Array[String]): Unit = {

    // val temp = sum(11,22)
    // 注意这里加上lazy注释，对比和没加的区别
    lazy val temp = sum(11,22)
    println("--------")
    println("temp is : " + temp)
  }

  def sum(i:Int, j:Int):Int = {
    println("function sum execute ... ")
    i + j
  }
}

```







## 6 面向对象



### 6.1 包

package 包名

命名规则：只能包含数字、字母、下划线、小圆点 但不能用数字开头，也不要使用关键字。

一般是：com.company.project.hexin

两种包管理风格：

- 与java类似，每个源文件一个包，包名用'.'分割表示包的层级关系。如：com.atguigu.scala
- 另一种是通过嵌套风格，下边这种风格特点如下：一个源文件中可以声明多个package，子包中的类可以直接访问父包中的内容，无需导包。

```scala
// 用嵌套风格定义包
package com{

  object Outer{
    var out:String  = "out"
  }

  package atguigu{
    package scala{
      object Inner{

        def main(args: Array[String]): Unit = {
          // 可以直接访问外层包的对象
          println(Outer.out)
        }

        def fun():Unit = {
          println("fun begin execute")
        }
      }
    }
  }
}


// 可以定义多个包层级
package aaa{
  package bbb{
    package ccc{
      import com.atguigu.scala.Inner

      object Test01_o{
        def main(args: Array[String]): Unit = {
          Inner.fun()
        }
      }
    }
  }
}
```





#### 6.1.3 包对象

在scala中可以为每个包定义一个同名的包对象，定义在包对象中的成员，作为其对应包下所有的class和object的共享变量，可以被直接访问。

IDEA中可以直接创建：

![](scala_image\s8.png)



包对象示例代码如下：

```scala
package scala

package object chapter06 {

  // 定义当前包共享的属性和方法
  val commanValue = "auserwn"
  def commonMethod():Unit = {
    println("commonMethod start execute ... ")
  }
}

// 通过这个定义，那么包chapter06 下的所有类都可以访问当前的属性commanValue 和 方法commonMethod
```



#### 6.1.4 导包

和java一样。

- 局部导入：在其作用范围内可用
- 通配符导入：import java.util._
- 给类起名：import java.util.{ArrayList=>JL}
- 一下导入多个：import java.util.{HashSet,ArrayList}
- 屏蔽类：import java.util.{ArrayList=> _ , _ }
- scala中默认导入



```scala
import users._      // 导入包 users 中的所有成员
import users.User   // 导入类 User
import users.{User, UserPreferences}      // 仅导入选择的成员
import users.{UserPreferences => UPrefs}  // 导入类并且设置别名
import users.{User => _, _}               // 导入出User类以外的所有users包中的内容

// 所有scala源文件默认导入：
import java.lang._
import scala._
import scala.Predef._
```



### 6.2 类和对象

scala中没有public ，一个.scala中可以写多个类



```scala
package scala.chapter06

import scala.beans.BeanProperty

object Test03_Class {

  def main(args: Array[String]): Unit = {

    val student = new Student()
    println(student.age)
    println(student.sex)
  }
}


class Student{

  // 定义属性  默认public 可以加private
  private var name: String = "kasha"
  // 对于属性，要符合javabean规范 可以增加一下@ 则有了对应的get set方法
  @BeanProperty
  var age: Int  = _
  // 表示当前sex的初值为空，之前说scala变量需要有初始值
  var sex: String = _

  // 定义方法

}


//成员如果需要Java Bean规范的getter和setter的话可以加@scala.beans.BeanProperty相当于自动创建，不需要显式写出。
```



#### 6.2.5 构造器

和java一样，scala构造对象也需要调用构造方法，并且可以有任意多个构造方法。

scala类的构造器包括：主构造器和辅助构造器。

辅助构造器，函数的名称为this，可以有多个。

辅助构造方法不能直接构建对象，必须直接或者间接的调用主构造方法。

```scala
class 类名(形参列表){  // 主构造器
    // 类体
    
    def this(形参列表){   // 辅助构造器
    }
    def this(形参列表){	// 辅助构造器
    }
}

// 注意：辅助构造器必须是def this 
// 类名后可以带参数作为主构造器
```

构造器测试示例代码如下：

```scala
object Constructor {
    def main(args: Array[String]): Unit = {
        val p: Person = new Person()
        p.Person() // call main constructor

        val p1 = new Person("alice")
        val p2 = new Person("bob", 25)
        p1.Person()
    }
}
class Person {
    var name: String = _
    var age: Int = _
    println("call main construtor")

    def this(name: String) {
        this()
        println("call assist constructor 1")
        this.name = name
        println(s"Person: $name $age")
    }

    def this(name: String, age: Int) {
        this(name)
        this.age = age
        println("call assist constructor 2")
        println(s"Person: $name $age")
    }

    // just a common method, not constructor
    def Person(): Unit = {
        println("call Person.Person() method")
    }
}
```

主构造方法默认有，查看以下示例代码：

```scala
package scala.chapter06

object Test06_Constructor_Main1 {

  def main(args: Array[String]): Unit = {

    val s = new TestStudent
  }
}


class TestStudent(){

  var name:String = _
  var age:Int = _
  println("Main Constructor execute ...")

  def this(name:String){
    this()
    println("Constructor 2 execute ...")
    this.name = name
    println(s"name:$name , age:$age")
  }
}

# 执行后代码显示如下： 默认调用了主构造方法
Main Constructor execute ...

Process finished with exit code 0
```



#### 6.2.6 构造器参数

scala类的主构造函数的形参包括三种类型：未用任何修饰、var修饰、val修饰

- 未用任何修饰：这个参数就是一个局部变量
- var修饰，作为类的成员属性使用，可以修改
- val修饰，作为类的只读属性使用，不可修改

示例代码如下：

```scala
package scala.chapter06

object Test06_ConstructorParams {

  def main(args: Array[String]): Unit = {

    val stu01_1 = new Student01
    println(s"stu10_1:[name:${stu01_1.name},age:${stu01_1.age}]")
    // 赋值需要
    stu01_1.name = "kasha"
    stu01_1.age = 13
    println(s"stu10_1:[name:${stu01_1.name},age:${stu01_1.age}]")
    // 这种赋值太麻烦，请看Student01

    val yasuo = new Student02("Yasuo", 15)
    println(s"yasuo:[name:${yasuo.name},age:${yasuo.age}]")
  }
}

// 无参构造 :创建时没办法赋值
class Student01{
  var name:String = _
  var age:Int = _
}

class Student02(var name:String, var age:Int)

// 这种写法获取不到，还得单独设置，受java影响太大，不推荐使用
class Student03(name:String, age:Int)


class Student6(var name:String, var age:Int){
  var school:String = _

  def this(name:String, age:Int, school:String){

    this(name,age)
    this.school = school
  }
}
```





### 6.3 封装

```
scala中考虑到Java太冗余了，脱裤子放屁一样。scala中的公有属性，底层实际为private，并通过get方法obj.field()和set方法obj.field_=(value)对其进行操作。所以scala不推荐设置为private。如果需要和其他框架互操作，必须提供Java Bean规范的getter和setter的话可以加@scala.beans.BeanProperty注解。
```



访问权限：

- scala中属性和方法默认公有，并且不提供`public`关键字。
- `private`私有，类内部和伴生对象内可用。
- `protected`保护权限，scala中比java中严格，只有同类、子类可访问，同包无法访问。【因为java中说实话有点奇怪】
- `private [pacakgeName]`增加包访问权限，在包内可以访问。



```scala
package scala.chapter06

object Test04_ClassForAccess {

  def main(args: Array[String]): Unit = {

  }
}


class Person{

  private var idCard: String = "123456"
  protected var name: String = "Kasha"
  var sex: String = "male"
  protected [chapter06] var age: Int = 19
}

```



以下代码为访问权限的示例：

```scala
package scala.chapter06

object Test04_Access {

  def main(args: Array[String]): Unit = {


  }
}


class Worker extends Person{
  override def printInfo(): Unit = {
    println(s"Worker:$name $sex $age")
  }
}
```



### 6.4 继承和多态



#### 6.4.1 继承

语法：class 子类 extends 父类 {类体}

scala是单继承

继承的调用顺序：父类构造器>子类构造器

示例代码如下：

```scala
package scala.chapter06

object Test07_Inherit {

  def main(args: Array[String]): Unit = {

    val test = new Student7("test", 18, 10001)
    test.printerInfo()
    // 结果显示这段代码的调用顺序为：
    /*
    1. 父类主构造器 -- 创建完父类
    2. 子类主构造器
    3. 子类的辅助构造器
     */
  }
}


// 定义父类
class Person7(){

  var name:String = _
  var age:Int = _

  println("1.Person7 Constructor")

  def this(name:String, age:Int){
    this()
    println("2. Person7 父类辅助构造器调用")
    this.name = name
    this.age = age
  }

  def printerInfo(): Unit = {
    println(s"Person7: name:$name, age:$age")
  }
}

// 定义子类
class Student7(name:String, age:Int) extends Person7{
  var stuNo:Int = _
  println("1.Student7 Constructor")

  def this(name: String, age: Int ,stuNo:Int) {
    this(name,age)
    println("2. Student7 子类辅助构造器调用")
    this.stuNo = stuNo
  }


}
```



#### 6.4.2 多态

动态绑定和多态



！！！ java中存在的问题，方法是动态绑定的，但是属性是静态绑定的

```java
// 以下是java测试代码， Worker类是Person类的子类
Person person = new Worker()
person.hello();
System.out.println(person.name);
// 上述方法中，调用的hello方法由于多态 是person的方法，但是打印的person.name则是person类的变量，这种叫 属性是静态绑定的。
// Java这中容易出现问题，则scala中都是动态绑定的
```

以下是scala中的多态示例代码：

```scala
package scala.chapter06

object Test08_DynamicBind {

  def main(args: Array[String]): Unit = {

    val worker08:Peron08 = new Worker08
    println(worker08.name)
    worker08.hello()
  }
}

class Peron08{

  val name:String = "person08"

  def hello(): Unit = {
    println("hello person08")
  }
}

class Worker08 extends Peron08{

  override val name: String = "worker08"

  override def hello(): Unit = {
    println("hello worker08")
  }
}

// 输出如下：
worker08
hello worker08

Process finished with exit code 0
```

scala中 关于多态，scala比java做的更加彻底



### 6.5 抽象类

#### 6.5.1 抽象属性和抽象方法

基本语法：

- 和java一样：只要有抽象属性和抽象方法就得是抽象类，但是抽象类里边可以既没有抽象属性也没有抽象方法

- `abstract calss ClassName`
- 抽象属性：`val/var name: Type`，不给定初始值。
- 抽象方法：`def methodName(): RetType`，只声明不实现。

- 子类如果没有覆写全部父类未定义的属性和方法，那么就必须定义为抽象类。老生常谈了。
- 重写非抽象方法属性必须加`override`，重写抽象方法则可以不加`override`。
- 子类调用父类中方法使用`super`关键字。
- 子类重写父类抽象属性，父类抽象属性可以用`var`修饰，子类`val var`都可以。因为父类没有实现嘛，需要到子类中来实现。
- 如果是**重写非抽象属性**，则父类非抽象属性只支持`val`，不支持`var`。因为`var`修饰为可变量，子类继承后可以直接使用修改，没有必要重写。`val`不可变才有必要重写。
- 实践建议是重写就加`override`，都是很自然的东西，理解就好，不必纠结于每一个细节。



示例代码如下：

```scala
package scala.chapter06

object Test09_AbstractClass {

  def main(args: Array[String]): Unit = {

    val student = new Student9
    student.sleep()
  }
}

// 定义抽象类
abstract class Person9{

  // 非抽象属性
  val name:String = "person"
  // 抽象属性
  var age:Int

  // 非抽象方法
  def eat():Unit = {
    println("Person eat ... ")
  }
  // 抽象方法
  def sleep():Unit
}

// 继续抽象子类
abstract class SubPerson9{

}

// 定义具体的实现子类
class Student9 extends Person9{
  override var age: Int = _

  override def sleep(): Unit = {
    // 调用父类的
    super.eat()
    println("student sleep")
  }
}
```



#### 6.5.2 匿名子类

应用背景：实际使用时可能并不关心具体实现子类叫什么，直接实现就可以了。就出现了匿名子类。

匿名子类的实例代码如下所示：

```scala
package scala.chapter06

object Test10_AnnoymousClass {

  def main(args: Array[String]): Unit = {

    val person1 = new Person10 {
      override val name: String = "Annoymous"

      override def eat(): Unit = {

        println("eat ingggggg")
      }
    }
    person1.eat()
  }
}


abstract class Person10{

  val name:String
  def eat():Unit
}
```



#### 6.5.3 伴生对象/单例对象

背景：java中有static关键字，对象声明static属性或者方法，就可以基于 类名.属性/方法进行调用了，不是很符合面向对象的理念，所以scala中诞生了伴生对象。

如果单例对象名和类名一致，则称该单例对象为这个类的伴生对象，这个类的所有静态属性/方法都可以放在这个伴生对象中进行声明。

基本语法：

```
object Person{
	val country:String = "China";
}
```

伴生对象和伴生类之间可以互相访问。即使是private修饰的。

```scala
package scala.chapter06

object Test11_Object {

  def main(args: Array[String]): Unit = {

    val auser = new Student11("auser", 17)
    auser.printInfo()
  }
}


class Student11(val name:String, val age:Int){
  def printInfo(): Unit = {
    println(s"student11: name:$name, age:$age ,${Student11.school}")
  }
}

object Student11{
  val school:String = "dlut"
}
```

用伴生对象实现单例模式，示例代码如下：

```scala
package scala.chapter06

/**
 * 以下代码使用伴生对象实现单例设计模式
 */
object Test12_ObjectSingleton {

  def main(args: Array[String]): Unit = {

    val s1 = Student12.getStu()
    val s2 = Student12.getStu()
    println(s1)
    println(s2)
  }
}

class Student12 private(val name: String, val age: Int) {
  def printInfo(): Unit = {
    println(s"student12:[name:$name, age:$age]")
  }
}

object Student12 {
  // 单例对象
  private val stu12: Student12 = new Student12("Ryze", 18)

  // 获取单例方法
  def getStu(): Student12 = stu12
}
```











7 集合

8 模式匹配

9 异常

10 隐式转换

11 泛型

12 总结