# scala



```
学习记录：
20241224：001-57 58开始看
20241226： -74 75开始看
20241228：82开始看
20241229: 101看完了 102开始看
20241230: 113看完了 114 开始看 今晚计划看完121 明天看122
20241231: 137异常开始看

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

![](image\scala_image\s3.png)

通过scalac编译scala文件生成两个字节码文件

![](image\scala_image\s4.png)



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



windows本地编译运行环境搭建可以参考：

```
https://mp.csdn.net/mp_blog/creation/editor/145264313
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



![](image\scala_image\s5.png)

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



![](image\scala_image\s6.png)



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

函数相关：

- 函数的形参列表可以是多个, 如果函数没有形参，调用时 可以不带()
- 函数没有参数列表的时候, 定义时, 也可以省略()
- 函数体中如果只有一行代码, 则可以省略大括号{}
- 不确定返回值类型，那么返回值类型可以省略(也就是自动推导),或声明为Any



### 5.1 函数基础

```scala
def sum(x: Int ,y: Int):Int = {
    x+y
}
```

![](image\scala_image\s7.png)

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

![](image\scala_image\s8.png)



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

辅助构造方法不能直接构建对象，必须直接或者间接的调用主构造方法。也就是说：辅助构造器无论是直接或间接，最终都一定要调用主构造器，执行主构造器的逻辑。



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



#### 6.2.7 对象相关

1. scala创建对象的流程

```
案例
class Person {
  var age: Short = 90
  var name: String = _
  def this(n: String, a: Int) {
    this()
    this.name = n
    this.age = a
  }}
var p : Person = new Person("小倩",20)


Scala对象创建对象流程分析
1)加载类的信息(属性信息和方法信息)
2)在内存中(堆)给对象开辟空间
3)使用父类的构造器(主构造器/辅助构造器)完成父类的初始化 (多个父类)
4)使用本类的主构造器完成初始化( age = 90 name = "null")
5)使用本类的辅助构造器继续初始化(age =20,name = "小倩")
6)将对象在内存中的地址赋给 p 这个引用
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



### 6.6 伴生对象/单例对象/Object

背景：java中有static关键字，对象声明static属性或者方法，就可以基于 类名.属性/方法进行调用了，不是很符合面向对象的理念，所以scala中诞生了伴生对象。

如果单例对象名和类名一致，则称该单例对象为这个类的伴生对象，这个类的所有静态属性/方法都可以放在这个伴生对象中进行声明。

scala中的Object是单例对象，相当于java中的工具类，可以看成是定义静态方法的类。

使用Obejct时，不用new，使用class的时候要用new，并且new的时候，class中除了方法不执行，其他都执行。

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


// 这是饿汉单例模式
//object Student12 {
//  // 单例对象
//  private val stu12: Student12 = new Student12("Ryze", 18)
//
//  // 获取单例方法
//  def getStu(): Student12 = stu12
//}


// 懒汉单例模式
object Student12{
  private var stu12: Student12 = _

  def getStu():Student12 = {
    if (stu12 == null){
      new Student12("Ryze",19)
    }
    else
      stu12
  }


}
```

单例模式：饿汉 懒汉 查看上述示例代码，实现和java逻辑一致。



### 6.7 特质 Trait

- 替代java接口的概念。但比接口更为灵活，一种实现多继承的手段。
- 多个类具有相同的特征时，就可以将这个特征提取出来，用继承的方式来复用。
- 用关键字`trait`声明。特质和抽象类类似，既可以有具体的属性和方法也可以有抽象的属性和方法。
- 这里区分继承和接口：继承可以是一些本质的属性，接口则是一些特征。所以何时使用继承父类，何时使用接口要考虑。
- scala中引入trait，第一可以替代java中的接口，第二则也是对单继承机制的一种补充。
- 注意：先写父类(如果有)，再写特质

```scala
trait traitName {
    ...
}
基本语法：
class 类名 extends 特质1 with 特质2 with 特质3 ...
class 类名 extends 父类 with 特质1 with 特质2 ...
```



#### 6.7.1 Trait基础

示例代码如下：

```scala
package scala.chapter06

object Test13_Trait {

  def main(args: Array[String]): Unit = {

    val student1 = new Student13
    student1.dating()
  }
}

// 定义一个父类
class Person13{
  val name:String  = "Person"
  var age:Int = 18

  def sayHello():Unit = {
    println("hello from :" + name)
  }
}

//定义特质
trait Young{
  // 声明抽象和非抽象属性
  var age:Int
  val name:String = "young"

  // 定义抽象和非抽象方法
  def play():Unit = {
    println("young play")
  }
  def dating():Unit
}
class Student13 extends Person13 with Young {
  override def dating(): Unit = {
    println("student13 dating")
  }
}
```

示例代码：

```scala
package scala.chapter06

object Test13_Trait {

  def main(args: Array[String]): Unit = {

    val student1 = new Student13
    student1.sayHello()
    student1.dating()
  }
}

// 定义一个父类
class Person13{
  val name:String  = "Person"
  var age:Int = 18

  def sayHello():Unit = {
    println("hello from :" + name)
  }
}

//定义特质
trait Young{
  // 声明抽象和非抽象属性
  var age:Int
  val name:String = "young"

  // 定义抽象和非抽象方法
  def play():Unit = {
    println("young play")
  }
  def dating():Unit
}
class Student13 extends Person13 with Young {
  // 父类和trait中都有name age 那继承后算谁的呢？优先级
  // 属性冲突，需要重写lass Student13 inherits conflicting members:
  override val name: String = "student13's name"

  // 实现抽象方法
  override def dating(): Unit = {
    println("student13 dating")
  }

  // 重写父类方法
  override def sayHello(): Unit = {
    super.sayHello()
    println("hello from student13")
  }
}
```

- 引入/混入(mixin)特征：
  - 有父类`class extends baseClass with trait1 with trait2 ... {}`
  - 没有父类`class extends trait1 with trait2 ... {}`
- 其中可以定义抽象和非抽象的属性和方法。
- 匿名子类也可以引入特征。
- 特征和基类或者多个特征中重名的属性或方法需要在子类中覆写以解决冲突，最后因为动态绑定，所有使用的地方都是子类的字段或方法。属性的话需要类型一致，不然提示不兼容。方法的话参数列表不一致会视为重载而不是冲突。
- 如果基类和特征中的属性或方法一个是抽象的，一个非抽象，且兼容，那么可以不覆写。很直观，就是不能冲突不能二义就行。
- 多个特征和基类定义了同名方法的，就需要在子类重写解决冲突。其中可以调用父类和特征的方法，此时`super.methodName`指代按照顺序最后一个拥有该方法定义的特征或基类。也可以用`super[baseClassOrTraitName].methodName`直接指代某个基类的方法，注意需要是直接基类，间接基类则不行。
- 也就是说基类和特征基本是同等地位。



动态混入的实现：

```scala
val stu = new Student14 with Talent{
	...
}
其实这个代码和匿名子类很像
```



#### 6.7.2 特质叠加-钻石问题

特征的叠加顺序，定义多个特征。

```
class Student15 extends Person13 with Talent15 with Knowledge15
如果特征Talent15 和 Knowledge15 都有increase方法
override def increase():Unit = {
	super.increase()
}
这时候调用的是谁的呢？--Knowledge15
特征是从后往前 不管前边是父类还是Trait
```



![](image\scala_image\s9.png)

钻石问题**特质叠加**：示例代码如下：

```scala
package scala.chapter06

/**
 * 该案例主要是针对钻石问题**特质叠加**的代码示例
 */
object Test15_TraitOverlying {

  def main(args: Array[String]): Unit = {

    // 钻石问题 特诊叠加
    val myBall = new MyFootBall
    println(myBall.describe())
  }
}


// 定义球类特征
trait Ball{
  def describe():String = "Ball"
}

// 定义子特征
// 定义颜色特征
trait ColorBall extends Ball{
  var color:String = "red"
  override def describe(): String = color + "_" + super.describe()
}
// 定义种类特征
trait CategoryBall extends Ball{
  var category:String = "foot"
  override def describe(): String = category + "_" + super.describe()
}

// 定义一个自定义球的类
class MyFootBall extends CategoryBall with ColorBall{
  // 这里应该是ColorBall 的describe
  override def describe(): String = "MyFootBall is : " + super.describe()

  // 按照之前的输出应该是MyFootBall is : super.describe() = MyFootBall is : red_Ball
  // 实际输出却是：MyFootBall is : red_foot_Ball 怎么出现了CategoryBall的foot_呢？
}
```

以上代码的解释如下：

![](image\scala_image\s10.png)

#### 6.7.5 特质自身类型

自身类型(self type)可实现依赖注入的功能。

- 可实现**依赖注入**的功能。
- 一个类或者特征指定了自身类型的话，它的对象和子类对象就会拥有这个自身类型中的所有属性和方法。
- 是将一个类或者特征插入到另一个类或者特征中，属性和方法都就像直接复制插入过来一样，能直接使用。但不是继承，不能用多态。
- 语法，在类或特征中：`_: SelfType =>`，其中`_`的位置是别名定义，也可以是其他，`_`指代`this`。插入后就可以用`this.xxx`来访问自身类型中的属性和方法了。
- 注入进来的目的是让你能够使用，可见，提前使用应该拥有的属性和方法。最终只要自身类型和注入目标类型同时被继承就能够得到定义了。

示例代码如下：

```scala
package scala.chapter06

object Test16_TraitSelfType {

  def main(args: Array[String]): Unit = {

    val ryze = new RegisterUser("Ryze", "123456")
    ryze.insert()
  }
}

// 定义用户类
class User(val name: String, val password: String)

trait UserDao{

  _: User =>
  // 上边这种写法就= 操作时默认当前有一个user 接下来可以直接操作 这里下划线可以用任意字符代替
  // 这里避免使用了继承，实现了依赖注入的功能

  // 插入用户
  def insert():Unit = {
    println(s"insert into db: ${this.name}, ${this.password}")
  }
}
class RegisterUser(name:String, password: String) extends User(name, password) with UserDao
```



#### 6.7.6 特质Trait和抽象类

何时使用特质，何时使用抽象类

- 优先使用Trait。因为一个类扩展多个Trait是很方便的，但是却只能扩展一个抽象类。

- 如果需要构造参数函数，使用抽象类。抽象类可以定义带参数的构造函数，而Trait不可以。



### 6.8 扩展

#### 6.8.1 类型的检查和转换

- 判断类型：`obj.isInstanceOf[T]`，确切匹配的类型或者父类都返回true。
- 转换类型：`obj.asInstance[T]`，转换为目标类型。
- 获取类名：`classOf[T]`，得到类对应的`Class`对象`Class[T]`，转字符串结果是`class package.xxx.className`。
- 获取对象的类：`obj.getClass`

示例代码如下：

```scala
package scala.chapter06

object Test17_Extends {

  def main(args: Array[String]): Unit = {

    val ryze: Student17 = new Student17("Ryze", 18)
    val kasha: Peron17 = new Student17("Kasha", 18)

    // 以下四个结果都是true
    println(ryze.isInstanceOf[Student17])
    println(ryze.isInstanceOf[Peron17])
    println(kasha.isInstanceOf[Student17])
    println(kasha.isInstanceOf[Student17])

    // 类型的转换
    // kasha创建时声明为kasha: Peron17 那么先判断可以的话转化为student17 再调用方法
    if (kasha.isInstanceOf[Student17]){
      val newKasha = kasha.asInstanceOf[Student17]
      newKasha.study()
    }

    //classof
    println(classOf[Student17])
  }
}


class Peron17(val name:String, val age:Int){

  def sayHi():Unit = {
    println("hi from person:" + name)
  }
}

class Student17(name:String, age:Int) extends Peron17(name, age){
  override def sayHi(): Unit = {
    println("hi from student:" + name)
  }
  def study():Unit = {
    println(" student study")
  }
}
```



#### 6.8.2 枚举类和应用类

说明：

- 枚举类：需要继承Enumeration。用`Value`类型定义枚举值。
- 应用类：需要继承App，包装了`main`方法，就不需要显式定义`main`方法了，可以直接执行。

示例代码如下：

```scala
package scala.chapter06

object Test18_Enumeration {

  def main(args: Array[String]): Unit = {

    println(WorkDay.MONDAY)
    println(WorkDay.MONDAY.id)

  }
}

// 定义枚举类对象
/*
在 Scala 的 Enumeration 中，Value 方法可以用来定义枚举值，括号中的第一个参数（例如这里的 1）表示 枚举值的 ID。ID 是枚举值的唯一标识，默认从 0 开始递增，但你可以显式指定它。
1：这是枚举值 MONDAY 的 ID（唯一标识）。
"Monday"：这是该枚举值的字符串表示，或者说是该枚举值的名称

Scala 的 Enumeration 会把每个枚举值存储为一个 Value 对象，其中包含以下内容：
ID：标识符，用于区分不同的枚举值。
名称：人类可读的名称，默认是变量名，但可以显式指定。

如果不显式指定 ID 和名称，则 Scala 会自动生成：
ID 从 0 开始递增。
名称与变量名相同。
 */
object WorkDay extends Enumeration{

  // 这里边底层存储就是1
  val MONDAY = Value(1,"Monday")
  val TUESDAY = Value(2,"Tuesday")
}

// 定义应用类对象
/*
是一种方便的方式来定义一个应用程序入口点。它的主要作用是简化程序的启动逻辑，使得你可以直接编写代码而不需要显式地定义 main 方法。
Scala 编译器会自动生成 main 方法，其行为等效于：
object TestApp {
  def main(args: Array[String]): Unit = {
    println("App start")
  }
}
你写在 object 体中的代码会被放入 App 特质的 delayedInit 方法中执行。
程序启动时，App 的 main 方法会调用 delayedInit，执行所有在 object 体中定义的代码。


使用场景
简单应用程序：当你的程序逻辑不复杂，只需要一个入口点时。
快速测试：用于快速测试一些代码片段。
脚本风格代码：将 Scala 用作脚本语言时（类似于 Python）。
 */
object TestApp extends App{
  println("App start")

  type MyString = String
  val a: MyString = "test myString"
  println(a)
  println(a.getClass)
}
```



#### 6.8.3 Type定义新类型

定义类型别名：`type SelfDefineType = TargetType`

示例代码如下： 定义一个MyString类型

```scala
object TestApp extends App{
  println("App start")

  type MyString = String
  val a: MyString = "test myString"
  println(a)
  println(a.getClass)
  println(classOf[MyString])
}
// 运行后输出如下：
App start
test myString
class java.lang.String
class java.lang.String
```



## 7 集合

Java集合：

- 三大类型：列表`List`、集合`Set`、映射`Map`，有多种不同实现。

Scala集合三大类型：

- 三大类型：序列`Seq`，集合`Set`，映射`Map`，所有集合都扩展自`Iterable`。
- 对于几乎所有集合类，都同时提供 可变和不可变 的版本 分别位于不同包
  - 不可变集合：`scala.collection.immutable`
  - 可变集合：`scala.collection.mutable`
  - 两个包中可能有同名的类型，需要注意区分是用的可变还是不可变版本，避免冲突和混淆。
- 对于不可变集合，指该集合长度数量不可修改，每次修改（比如增删元素）都会返回一个新的对象，而不会修改源对象。
- 可变集合可以对源对象任意修改，一般也提供不可变集合相同的返回新对象的方法，但也可以用其他方法修改源对象。
- **建议**：操作集合时，不可变用操作符，可变用方法。操作符也不一定就会返回新对象，但大多是这样的，还是要具体看。
- scala中集合类的定义比java要清晰不少。



### 7.1 集合简介

#### 7.1.1 不可表集合介绍

不可变集合：

- `scala.collection.immutable`包中不可变集合关系一览：

![](image\scala_image\s11.png)

- 不可变集合没有太多好说的，集合和映射的哈希表和二叉树实现是肯定都有的，序列中分为随机访问序列（数组实现）和线性序列（链表实现），基本数据结构都有了。
- `Range`是范围，常用来遍历，有语法糖支持`1 to 10 by 2` `10 until 1 by -1`其实就是隐式转换加上方法调用。
- scala中的`String`就是`java.lang.String`，和集合无直接关系，所以是虚箭头，是通过`Perdef`中的低优先级隐式转换来做到的。经过隐式转换为一个包装类型后就可以当做集合了。
- `Array`和`String`类似，在图中漏掉了。
- 此类包装为了兼容java在scala中非常常见，scala中很多类型就是对java类型的包装或者仅仅是别名。
- scala中可能会推荐更多地使用不可变集合。能用不可变就用不可变。



#### 7.1.2 可变集合

![](image\scala_image\s12.png)

- 序列中多了`Buffer`，整体结构差不多。

不可变和可变：

- 不可变指的是对象大小不可变，但是可以修改元素的值（不能修改那创建了也没有用对吧），需要注意这一点。而如果用了`val`不变量存储，那么指向对象的地址也不可变。
- 不可变集合在原集合上个插入删除数据是做不到的，只能返回新的集合。









### 7.2 数组

一组连续空间的

#### 7.2.1 不可变数组

`val arr1 = new Array[Int](10)`

示例代码如下：

```scala
package scala.chapter07

object Test01_ImmutableArray {

  def main(args: Array[String]): Unit = {

    // 1.创建数组
    val arr1 = new Array[Int](10)
    // 还有Array的伴生对象
    val arr2 = Array.apply(12,123,1234,12345)
    println(arr2)

    // 访问元素
    println(arr2(0))
  }
}

```

数组的遍历打印代码如下：

```scala
package scala.chapter07

object Test01_ImmutableArray {

  def main(args: Array[String]): Unit = {

    // 1.创建数组
    val arr1 = new Array[Int](10)
    // 还有Array的伴生对象
    val arr2 = Array.apply(12,123,1234,12345)
    println(arr2)

    arr2(0)=99
    // 访问元素
    println(arr2(0))
    // 打印
    for (i<- 0 to arr2.length-1){
      print(arr2(i) + " ")
    }
    println()
    for (i <- 0 until  arr2.length) {
      print(arr2(i) + " ")
    }
    println()
    for (i <- arr2.indices) print(arr2(i) + " ")
    println()
    // 直接遍历所有元素 不走索引 增强for循环
    for (elem <- arr2) print(elem + " ")
    println()
    // 迭代器
    val iter  = arr2.iterator
    while (iter.hasNext){
      print(iter.next() + " ")
    }
    println()
    // for each
    arr2.foreach((elem:Int) => print(elem + " "))
    arr2.foreach( print )

    println()
    print(arr2.mkString("--"))
  }
}

```

数组添加元素，并不是在原数组添加元素，而是添加后返回一个新的数组

```scala
package scala.chapter07

object Test01_ImmutableArray_2 {

  def main(args: Array[String]): Unit = {

    val arr2 = new Array[Int](10)
    val arr3 = arr2.:+(13)  // 往后添加
    var arr4 = arr2.+:(11)  // 往前添加

    println(arr2.mkString(" ")) // 0 0 0 0 0 0 0 0 0 0
    println(arr3.mkString(" ")) // 0 0 0 0 0 0 0 0 0 0 13
    println(arr4.mkString(" ")) // 11 0 0 0 0 0 0 0 0 0 0

    var arr5 = arr2 :+ 15
    println(arr5.mkString(" ")) // 0 0 0 0 0 0 0 0 0 0 15
    var arr6 = 10 +: 29 +:  arr2
    println(arr6.mkString(" ")) // 10 29 0 0 0 0 0 0 0 0 0 0

  }
}

```



#### 7.2.2 可变数组

类型`ArrayBuffer`

- 推荐：不可变集合用运算符，可变集合直接调用对应方法。运算符容易迷惑。
- 更多方法查看文档和源码用到去找就行。
- 可变数组和不可变数组可以调用方法互相转换。
- 常用方法：append prepend insert() insertAll appendAll prependAll remove -=

示例代码如下：

```scala
package scala.chapter07

import scala.collection.mutable.ArrayBuffer

object Test02_MutableArray {

  def main(args: Array[String]): Unit = {

    val arr1:ArrayBuffer[Int] = new ArrayBuffer[Int]()
    // 或者使用伴生对象进行创建
    val arr2 = ArrayBuffer(1,12,123,1234)
    println(arr2.mkString(" "))

    // 可变数组追加
    arr1 += 14  // 后边增加
    println(arr1.mkString(" "))
    13 +=: arr1  // 前边增加
    println(arr1.mkString(" "))

    arr1.append(15) // 后边增加
    println(arr1.mkString(" "))
    arr1.prepend(12) // 前边增加
    println(arr1.mkString(" "))

    arr1.insert(0,11) // 指定位置
    println(arr1.mkString(" "))

    arr1.prependAll(ArrayBuffer(9,10))
    println(arr1.mkString(" "))
  }
}

```





#### 7.2.3 可变 不可变的转换

可变数组和不可变数组可以调用方法互相转换:

- 可变=>不可变 toArray
- 不可变=>可变 toBuffer

```scala
package scala.chapter07

import scala.collection.mutable.ArrayBuffer

object Test02_Transform {

  def main(args: Array[String]): Unit = {

    val arr_InMutable = Array(11,12,13)
    val arr_Mutable = ArrayBuffer(101,102,103)

    // 可变转换为不可变
    val new_arr1 = arr_Mutable.toArray
    println(new_arr1.mkString(" "))

    // 不可变转换为可变
    val new_arr2 = arr_InMutable.toBuffer
    println(new_arr2.mkString(" "))
    println(new_arr2)
  }
}

```



#### 7.2.4 多维数组

- 就是数组的数组。
- 使用`Array.ofDim[Type](firstDim, secondDim, ...)`方法。



### 7.3 Seq集合-List

#### 7.3.1 不可变List

- `List`，抽象类，不能直接`new`，使用伴生对象`apply`传入元素创建。
- `List`本身也有`apply`能随机访问（做了优化）即可以通过下标获取，但是不能`update`更改。
- `foreach`方法遍历。
- 支持`+: :+`首尾添加元素。
- `Nil`空列表，`::`添加元素到表头。
- 常用`Nil.::(elem)`创建列表，换一种写法就是`10 :: 20 :: 30 :: Nil`得到结果`List(10, 20, 30)`，糖是真滴多！
- 合并两个列表：`list1 ::: list2` 或者`list1 ++ list2`。

```scala
package scala.chapter07

object Test04_List {

  def main(args: Array[String]): Unit = {

    // 创建不可变List
    val list1 = List(1,12,123,1234)
    println(list1)
    println(list1(1))
    list1.foreach(print)

    // 增加元素
    val l1 = list1.+:(99)
    val l2 = list1.:+(99)
    println(l1)
    println(l2)

    val l4 = list1.::(99)  // 增加到前边
    println(l4)

    val l5 = Nil.::(99) // 增加到前边
    println(l5)

    val l6 = 1 :: 2 :: 3 :: Nil // 创建列表
    println(l6)

    // 列表合并
    val ll1 = 1 :: Nil
    val ll2 = 2 :: Nil
    val ll3 = ll1 ::: ll2
    val ll4 = ll1 ++ ll2
    println(ll3)
    println(ll4)
  }
}

```



#### 7.3.2 可变ListBuffer

- 可变列表`ListBuffer`，和`ArrayBuffer`很像。
- `final`的，可以直接`new`，也可以伴生对象`apply`传入元素创建（总体来说scala中更推荐这种方式）。
- 方法：`append prepend insert remove`
- 添加元素到头或尾：`+=: +=`
- 合并：`++`得到新的列表，`++=`合并到源上。
- 删除元素也可以用`-=`运算符。
- 具体操作很多，使用时阅读文档即可。

```scala
package scala.chapter07

import scala.collection.mutable.ListBuffer

object Test05_ListBuffer {

  def main(args: Array[String]): Unit = {

    val list1:ListBuffer[Int] = new ListBuffer[Int]()
    val list2 = ListBuffer(11,22,33)

    println(list2)
    list1.append(12,123)
    list1.prepend(1)
    println(list1)
    list1.remove(2)
    println(list1)
  }
}

```



### 7.4 Set集合

注意：scala中默认使用的是不可变set，如果想使用可变set，需要引入：scala.collection.mutable.Set包

#### 7.4.1 不可变Set

- 数据无序，不可重复。
- 可变和不可变都叫`Set`，需要做区分。默认`Set`定义为`immutable.Set`别名。
- 创建时重复数据会被去除，可用来去重。
- 添加元素：`set + elem`  因为是无序的，所以直接+ 不需要考虑是前边还是后边
- 合并：`set1 ++ set2 ` 返回结果本身无序
- 移除元素：`set - elem`
- 不改变源集合。

```scala
package scala.chapter07

import scala.collection.mutable.Set

object Test06_Set {

  def main(args: Array[String]): Unit = {

    val s1 = Set(11, 12)
    println(s1)
    val s2 = s1 + 11
    println(s1)
  }
}

```



#### 7.4.2 可变Set

- 操作基于源集合做更改。
- 为了与不可变集合区分，`import scala.collection.mutable`并用`mutable.Set`。
- 不可变集合有的都有。
- 添加元素到源上：`set += elem` `add`
- 删除元素：`set -= elem` `remove`
- 合并：`set1 ++= set2`
- 都很简单很好理解，多看文档和源码就行。

```scala
package scala.chapter07

import scala.collection.mutable.Set

object Test07_MutableSet {

  def main(args: Array[String]): Unit = {

    val s1 = Set(11, 12, 11, 13)
    println(s1)

    val flag1 = s1.add(14)
    println(s1)

    val flag2 = s1.remove(12)
    println(s1)
  }

}

```





### 7.5 Map集合

#### 7.5.1 不可变Map

- `Map`默认就是`immutable.Map`别名。
- 两个泛型类型。
- 基本元素是一个二元组。
- 遍历Traverse:

```scala
package scala.chapter07

object Test08_Map {

  def main(args: Array[String]): Unit = {

    val map:Map[String,Int] = Map("a" -> 13,"b"->14)
    println(map)

    // traverse
    map.foreach(println)
    println("====")
    map.foreach((kv:(String,Int)) => println(kv))
    map.foreach(kv => println(s"k:${kv._1},v:${kv._2}"))

    // get keys and values
    for (key <- map.keys) {
      println(s"${key}:${map.get(key)}")
    }

    println("---get value of given key ")
    // get value of given key
    println(map.get("a"))
    println(map.get("a").get)
    println(map.getOrElse("b",-1))
    println(map("a"))

    // merge
    val map2 = map ++ Map("c"->33)
    println(map2)
  }
}

```





#### 7.5.2 可变Map

- `import scala.collection.mutable.Map`
- 不可变的都支持，常见操作：put remove -= ++= 

```scala
package scala.chapter07

import scala.collection.mutable

object Test09_MutableMap {

  def main(args: Array[String]): Unit = {

    // create mutable Map
    val map: mutable.Map[String, Int] = mutable.Map("a" -> 10, "b" -> 20)
    // add element
    map.put("c", 30)
    map += (("d", 40)) // two () represent tuple to avoid ambiguity
    println(map)
    // remove element
    map.remove("a")
    map -= "b" // just need key
    println(map)
    // modify element
    map.put("c", 100) // call update, add/modify
    println(map)
    // merge Map
    map ++= Map("a" -> 10, "b" -> 20, "c" -> 30) // add and will override
    println(map)
      
    // val map4 = map1 + map2
  }
}

```





### 7.6 元组

> 元组可以理解成为一个容器，可以存放各种相同或者不同类型的数据。就是将多个无关的数据封装成为一个整体，称为 **元组**。

- `(elem1, elem2, ...)` 类型可以不同。
- 最多只能22个元素，从`Tuple1`定义到了`Tuple22`。
- 使用`_1 _2 _3 ...`访问。
- 也可以使用`productElement(index)`访问，下标从0开始。
- `->`创建二元组。
- 遍历：`for(elem <- tuple.productIterator)`
- 可以嵌套，元组的元素也可以是元组。



```scala
package scala.chapter07

object Test10_Tuple {

  def main(args: Array[String]): Unit = {

    val t1 = (1, "Ryze" ,'a' , true)
    println(t1)

    // 访问数据
    println(t1._2)  // 这是从1开始
    println(t1.productElement(1)) // 这是从0开始

    // 遍历元组数据
    for (elem <- t1.productIterator){
      println(elem)
    }
  }
}

```



### 7.7 集合常用函数

#### 7.7.1 基本属性和常用操作

- 线性序列才有长度`length`、所有集合类型都有大小`size`。
- 遍历`for (elem <- collection)`、迭代器`for (elem <- collection.iterator)`。
- 生成字符串`toString` `mkString`，像`Array`这种是隐式转换为scala集合的，`toString`是继承自`java.lang.Object`的，需要自行处理。
- 是否包含元素`contains`。



```scala
package scala.chapter07

object Test11_CommonOp {

  def main(args: Array[String]): Unit = {

    val list = List(1,12,123)

    println(list.length) // 获取集合长度
    println(list.size)

    for(elem <- list){ // 循环遍历
      print(elem)
    }
    println()
    for (elem <- list.iterator){
      print(elem)
    }

    println(list.mkString(" ")) // 生成字符串

    // 是否包含
    println(list.contains(123))
  }
}

```



#### 7.7.2 衍生集合

- 获取集合的头元素`head`（元素）和剩下的尾`tail`（集合）。
- 集合最后一个元素`last`（元素）和除去最后一个元素的初始数据`init`（集合）。
- 反转`reverse`。
- 取前后n个元素`take(n) takeRight(n)`
- 去掉前后n个元素`drop(n) dropRight(n)`
- 交集`intersect`
- 并集`union`，线性序列的话已废弃用`concat`连接。
- 差集`diff`，得到属于自己、不属于传入参数的部分。
- 拉链`zip`，得到两个集合对应位置元素组合起来构成二元组的集合，大小不匹配会丢掉其中一个集合不匹配的多余部分。
- 滑窗`sliding(n, step = 1)`，框住特定个数元素，方便移动和操作。得到迭代器，可以用来遍历，每个迭代的元素都是一个n个元素集合。步长大于1的话最后一个窗口元素数量可能个数会少一些。

```scala
package scala.chapter07

object Test12_DerivedCollection {

  def main(args: Array[String]): Unit = {

    val ll = List(1,2,3,4,5)
    val ll2 = List(4,5,6,7)
    println(ll.head) // 头 :1
    println(ll.tail) // 非头元素:List(2, 3, 4, 5)
    println(ll.last) // 最后元素：5
    println(ll.init) // 除去最后一个元素的初始数据:List(1, 2, 3, 4)
    println(ll.reverse)
    println(">>>")
    println(ll.intersect(ll2)) //交集intersec
  }
}

```



#### 7.7.3 集合简单计算

- 求和`sum` 求乘积`product` 最小值`min` 最大值`max`
- `maxBy(func)`支持传入一个函数获取元素并返回比较依据的值，比如元组默认就只会判断第一个元素，要根据第二个元素判断就返回第二个元素就行`xxx.maxBy(_._2)`。
- 排序`sorted`，默认从小到大排序。从大到小排序`sorted(Ordering[Int].reverse)`。
- 按元素排序`sortBy(func)`，指定要用来做排序的字段。也可以再传一个隐式参数逆序`sortBy(func)(Ordering[Int].reverse)`
- 自定义比较器`sortWith(cmp)`，比如按元素升序排列`sortWith((a, b) => a < b)`或者`sortWith(_ < _)`，按元组元素第二个元素升序`sortWith(_._2 > _._2)`。
- 例子：

```
package scala.chapter07

object Test13_SimpleFunction {

  def main(args: Array[String]): Unit = {

    val ll = List(1, 12, 100)
    val ll2 = List(("Kasha",480),("Yasuo",1350),("Ryze",6300))

    println(ll.sum) // 求和

    println(ll.product) // 乘积

    println(ll.max)
    println(ll.min)

    println(ll2.maxBy((tuple:(String,Int)) => tuple._2))
    // 省略后
    println(ll2.maxBy(_._2))

    // 排序
    val sorted = ll.sorted
    val sorted2 = ll.sorted(Ordering[Int].reverse)
    println(sorted2)

    // sortWith
    println(ll.sortWith((a:Int, b:Int) => {a<b}))
    // 简化
    println(ll.sortWith(_>_))
  }
}

```



#### 7.7.4 集合高级计算

- 大数据的处理核心就是映射（map）和归约（reduce）。
- 映射操作（广义上的map）：
  - 过滤：自定义过滤条件，`filter(Elem => Boolean)`
  - 转化/映射（狭义上的map）：自定义映射函数，`map(Elem => NewElem)`
  - 扁平化（flatten）：将集合中集合元素拆开，去掉里层集合，放到外层中来。`flatten`
  - 扁平化+映射：先映射，再扁平化，`flatMap(Elem => NewElem)`
  - 分组（group）：指定分组规则，`groupBy(Elem => Key)`得到一个Map，key根据传入的函数运用于集合元素得到，value是对应元素的序列。
- 归约操作（广义的reduce）：
  - 简化/归约（狭义的reduce）：对所有数据做一个处理，归约得到一个结果（比如连加连乘操作）。`reduce((CurRes, NextElem) => NextRes)`，传入函数有两个参数，第一个参数是第一个元素（第一次运算）和上一轮结果（后面的计算），第二个是当前元素，得到本轮结果，最后一轮的结果就是最终结果。`reduce`调用`reduceLeft`从左往右，也可以`reduceRight`从右往左（实际上是递归调用，和一般意义上的从右往左有区别，看下面例子）。
  - 折叠（fold）：`fold(InitialVal)((CurRes, Elem) => NextRes)`相对于`reduce`来说其实就是`fold`自己给初值，从第一个开始计算，`reduce`用第一个做初值，从第二个元素开始算。`fold`调用`foldLeft`，从右往左则用`foldRight`（翻转之后再`foldLeft`）。具体逻辑还得还源码。从右往左都有点绕和难以理解，如果要使用需要特别注意。
- 以上：

```scala

// Map代码
package scala.chapter07

object Test14_HighLevelFunction {

  def main(args: Array[String]): Unit = {

    val ll = List(1,2,3,4,5,6,7,8,9)

    // 过滤操作
    val ll_filter = ll.filter((i: Int) => {i % 2 == 0})
    println(ll_filter)
    println(ll.filter(_ % 2 == 0))

    // map转换操作
    val ll_map = ll.map((i: Int) => (("num:"+i, i)))
    println(ll_map)

    println("=====flatten")
    val needList:List[List[Int]] = List(List(1,2,3),List(4,5,6))
    val flatten = needList.flatten
    println(flatten)

    println("=====flatmap")
    val fm = List("hello spark", "hello redis", "hello scala")
//    val fm_res = fm.flatMap(s => s.split(" "))
    val fm_res = fm.flatMap(_.split(" ")) // 分词
    println(fm_res)

    println("=====groupby")
    val llg = List("hello spark", "hello redis", "hello scala")
    val llg_res1 = llg.flatMap(_.split(" "))
    val llg_res2 = llg_res1.groupBy((_,1))
    println(llg_res2)

    // 奇偶分组
    val tupleToInts = ll.groupBy( num => if(num%2==0) "奇数" else "偶数")
    println(tupleToInts)
  }
}

```



```scala
package scala.chapter07

object Test15_HighLevelFunction_Reduce {

  def main(args: Array[String]): Unit = {

    val ll = List(1,2,3,4)

    //val ll_sum = ll.reduce((a, b) => a + b)
    val ll_sum = ll.reduce( _ + _ )
    println(ll_sum)
    println(ll.reduceLeft( _ + _ ))
    println(ll.reduceRight( _ + _ ))

    // 左右的区别
    val ll2 = List(10,9,2)
    println(ll2.reduceLeft( _ - _)) // 先10-9= 1 再 1-2 = -1
    println(ll2.reduceRight( _ - _)) // 先 9-10 = -1 再 2-(-1) =3

    // Fold折叠
    println("=====Fold")
    val ll3 = List(1,2,3,4)
    println(ll3.fold(10)(_ + _)) // 初始状态+操作
    // foldLeft foldRight 同上
  }
}

```

复杂示例如下： 实现mergeMap

```scala
package scala.chapter07

import scala.collection.mutable

object Test16_MergeMap {

  def main(args: Array[String]): Unit = {

    val map1 = Map("a"->1, "b"->2, "c"->3)
    val map2 = mutable.Map("a"->9, "b"->8, "c"->7, "d"->10)

    val m3 = map1.foldLeft(map2){
      (mergeMap,m) => {
        val key = m._1
        val value = m._2
        mergeMap(key) = mergeMap.getOrElse(key,0) + value
        mergeMap
      }
    }

    println(m3)
  }

}

```



##### 7.7.4.1 mapValues-专项

在Scala中，`mapValues` 是 `Map` 类型的一个方法，用于对 `Map` 中的值进行映射操作。`mapValues` 方法返回一个新的 `Map`，其中的值是通过对原 `Map` 中的每个值应用一个函数得到的。原 `Map` 中的键（`key`）保持不变，而只有值（`value`）被转换。

`mapValues` 方法的定义如下：

```scala
def mapValues[V2](f: V => V2): Map[K, V2]
```

例子如下：

案例1：**对Map中的值进行加倍**:假设我们有一个 `Map[String, Int]`，我们希望对每个值进行加倍操作

```scala
package scala.chapter07

object Test08_MapValues_1 {

  def main(args: Array[String]): Unit = {

    val mm = Map("a" -> 1,"b" -> 2,"c" -> 3)
    val after_mv = mm.mapValues(v => v * v)
    println(after_mv)
  }
}
// 输出如下：
Map(a -> 1, b -> 4, c -> 9)

```

案例2：**对map中嵌套的数组进行求和** 示例代码如下：

```scala
package scala.chapter07

/*
Map("a" -> List(1, 2, 3), "b" -> List(4, 5, 6))
OUT===>
Map(a -> 6, b -> 15)

 */
object Test08_MapValues_2 {

  def main(args: Array[String]): Unit = {
    val map_list = Map("a" -> List(1, 2, 3), "b" -> List(4, 5, 6))
    val lsum = map_list.mapValues(f => f.sum)
    println(lsum)
  }
}

```





#### 7.7.5 经典WordCount案例

统计排序取前k个

```scala
package scala.chapter07

object Test17_WordCountCase {

  def main(args: Array[String]): Unit = {

    val ll = List("hello scala", "hello spark", "hello flink")
    val ll_flatmap = ll.flatMap(_.split(" "))
    println(ll_flatmap)

    // 相同的单词进行分组
    val ll_group = ll_flatmap.groupBy(word => word)
    println(ll_group)

    // 分组后个数统计
    val ll_count = ll_group.map(kv => (kv._1, kv._2.size))
    println(ll_count)

    // map转化为list 排序取前1
    val ll_res = ll_count.toList
//      .sortWith((a,b) => a._2 > b._2)
      .sortWith(_._2 > _._2)
      .take(2)

    print(ll_res)
  }
}

```

复杂一点的wordcount案例：

```scala
package scala.chapter07

object Test18_ComplexWordCount {

  def main(args: Array[String]): Unit = {
    val ll: List[(String, Int)] = List(
      ("hello", 1),
      ("hello spark", 2),
      ("hello flink", 2),
      ("hello kafka", 2)
    )

    println("===========method 1 ")
    val ll_temp = ll.map(
      kv =>{
        (kv._1.trim + " " ) * kv._2
      }
    )
    println(ll_temp)
    val res = ll_temp.flatMap(_.split(" "))
      .groupBy( word => word )
      .map(kv => (kv._1,kv._2.size))
      .toList
    println(res)


    println("===========method 2 :直接基于预统计的结果进行转换")
    val preCountList: List[(String, Int)] = ll.flatMap(
      t => {
        val strings: Array[String] = t._1.split(" ")
        strings.map( word => (word, t._2))
      }
    )
    val res2 = preCountList.groupBy(_._1)
      .mapValues(
        tupleList => {
          tupleList.map(_._2).sum
        }
      )
      .toList
    println(res2)


    println("===========method 3")
    val temp3 = ll.flatMap{
      case (words, count) => words.split(" ").map((_,count))
    }
    val res3 = temp3.groupBy(_._1)
      .mapValues(
        tuplel => {
          tuplel.map(_._2).sum
        }
      )
      .toList
    println(res3)
  }
}

```

其他实现：

```scala
package scala.chapter07

object Test19_WordCountRealizeSelf {

  def main(args: Array[String]): Unit = {

    val ll: List[(String, Int)] = List(
      ("hello", 1),
      ("hello spark", 2),
      ("hello flink", 2),
      ("hello kafka", 2)
    )

    val temp = ll.flatMap(
      kv => {
        kv._1.split(" ")
          .map(a => (a, kv._2))
      }
    )
      .groupBy(_._1)
      .mapValues(
        slists => {
          slists.map(_._2).sum
        }
      )
      .toList
      .sortWith(_._2 > _._2)
      .take(2)

    println(temp)
  }

}

```



### 7.8 队列Queue

队列的特点是先进先出

Traversable -> iterable -> seq -> linearable -> queue

- 可变队列`mutable.Queue`
- 入队`enqueue(Elem*)` 出队`Elem = dequeue()`
- 不可变队列`immutable.Queue`，使用伴生对象创建，出队入队返回新队列。

示例代码如下：

```scala
package scala.chapter07

import scala.collection.mutable

object Test19_Queue {

  def main(args: Array[String]): Unit = {

    // 可变队列
    // 或者这样定义 val que = new mutable.Queue[String]()
    // 下边是采用伴生对象的方法
    val que = mutable.Queue(1,2,3,4)
    println(que)
    val en_res = que.enqueue(5)
    val de_res = que.dequeue()
    println("en_res :" + en_res)
    println("de_res :" + de_res)
    println(que)
  }
}

```



### 7.9 并行集合

背景：scala为了充分使用多核CPU，提供了并行集合，有别于前边的串行集合，用于多核环境的并行计算。

```scala
package scala.chapter07

object Test20_Parallel {

  def main(args: Array[String]): Unit = {

    // 串行化执行
    val threads = (0 to 100).map(
      i => (Thread.currentThread.getName, Thread.currentThread.getId)
    )
    println(threads)

    // 并行计算
    val parThread = (0 to 100).par.map(
      i => (Thread.currentThread.getName, Thread.currentThread.getId)
    )
    println(parThread)
  }
}

```





## 8 模式匹配

> 什么是scala中的模式匹配？类似于java中的switch语法，但它功能更强大，可以匹配复杂的数据结构和进行解构。

### 8.1 基本语法

```scala
value match {
    case caseVal1 => returnVal1
    case caseVal2 => returnVal2
    ...
    case _ => defaultVal
}
```

- 每一个case条件成立就返回不会往下走，否则继续往下走。
- 每个case不需要使用break语句，匹配成功自动中断case。
- => 后边的代码块，直到下一个case语句之前的代码是作为一个整体执行，可以用{}括起来，也可以不括。
- case _ => " " 类似于通配符
- `case`匹配中可以添加模式守卫，用条件判断来代替精确匹配。

```scala
def abs(num: Int): Int= {
    num match {
        case i if i >= 0 => i
        case i if i < 0 => -i
    }
}
```



示例代码如下：

```scala
package scala.chapter8

object Test01_PatternMatchBase {

  def main(args: Array[String]): Unit = {

    println(oper(10,5,"+"))
    println(oper(10,5,"-"))
    println(oper(10,5,"*"))
    println(oper(10,5,"/"))
    println(oper(10,5,"s"))
    
    println("===========")
    val x: Int = 2
    val y: String = x match {
      case 1 => "None"
      case 2 => "yes"
      case _ => "error"
    }
    println(y)
  }


  def oper(x: Int , y: Int, ope: String) = {
    ope match {
      case "+" => x+y
      case "-" => x-y
      case "*" => x*y
      case "/" => x/y
      case _ => "illeagle"
    }
  }
}

```



### 8.2 模式守卫

如果想要表达匹配某个范围的数据，需要在模式匹配中增加条件守卫

示例代码如下：

```scala
package scala.chapter8

object Test02_MatchGuard {

  def main(args: Array[String]): Unit = {

    def abs(x: Int) = x match {
      case i: Int if i>= 0 => i
      case j: Int if j<0 => -j
      case _ => "illegal input"
    }

    // 类型匹配
    def describeType(x: Any): String = x match {
      case i: Int => "Int: " + i
      case i: String => "String: " + i
      case i => "Unknown type"
    }
    println(abs(9))
    println(abs(-8))
    println("类型匹配：" + describeType(9))
    println("类型匹配：" + describeType("zzzz"))
  }
}

```



### 8.3 模式匹配类型

- 模式匹配支持类型：所有类型字面量，包括字符串、字符、数字、布尔值、甚至数组列表等。
- 你甚至可以传入`Any`类型变量，匹配不同类型常量。
- 需要注意默认情况处理，`case _`也需要返回值，如果没有但是又没有匹配到，就抛出运行时错误。默认情况`case _`不强制要求通配符（只是在不需要变量的值建议这么做），也可以用`case abc`一个变量来接住，可以什么都不做，可以使用它的值。
- 通过指定匹配变量的类型（用特定类型变量接住），可以匹配类型而不匹配值，也可以混用。示例代码见上边的// 类型匹配
- 需要注意类型匹配时由于泛型擦除，可能并不能严格匹配泛型的类型参数，编译器也会报警告。但`Array`是基本数据类型，对应于java的原生数组类型，能够匹配泛型类型参数。



#### 8.3.1 匹配...

> 匹配常量、类型、数组、列表、元组、对象以及样例类

示例代码如下：

```scala
package scala.chapter8

object Test02_MatchTypes {

  def main(args: Array[String]): Unit = {

    // 匹配常量
    def describeConst(x: Any): String = x match {
      case 1 => "x is : 1"
      case "a" => " x is : a"
      case _ => "unknown x"
    }
    println(describeConst(1))
  }

  // 匹配类型。之前写过这里不写了，但是List会有泛型擦除
  // case x:List[String] => "is list"
  // 这里如果x上传List(1,2,3) 也会成功，这就是泛型擦除，只能到List这个粒度

  // 匹配集合类型
  for (arr <- List(
    Array(0),
    Array(1, 0),
    Array(1, 0, 0),
    Array("hello", 12)
  )){
    val res = arr match {
      case Array(0) => "array: 0"
      case Array(1, 0) => "array: 1, 0"
      case Array(x, y) => "array: " + x +"," + y // 匹配两元素数组
      case Array(0 , _*) => "array 0 开头"
      case _ => "unknown x"
    }
    println(res)
  }

  val first :: second :: rest = List(1,2,33,44,55)
  println(s"first is : $first, second is : $second , rest is : $rest")
}

```



- List 匹配和Array差不多，也很灵活。还可用用集合类灵活的运算符来匹配。
  - 比如使用`::`运算符匹配`first :: second :: rest`，将一个列表拆成三份，第一个第二个元素和剩余元素构成的列表。
- 注意模式匹配不仅可以通过返回值当做表达式来用，也可以仅执行语句类似于传统`switch-case`语句不关心返回值，也可以既执行语句同时也返回。
- 元组匹配：
  - 可以匹配n元组、匹配元素类型、匹配元素值。如果只关心某个元素，其他就可以用通配符或变量。
  - 元组大小固定，所以不能用`_*`。





#### 8.3.2 匹配对象及样例类

匹配对象：

- 对象内容匹配。
- 直接`match-case`中匹配对应引用变量的话语法是有问题的。编译报错信息提示：不是样例类也没有一个合法的`unapply/unapplySeq`成员实现。
- 要匹配对象，需要实现伴生对象`unapply`方法，用来对对象属性进行拆解以做匹配。

样例类：

- 第二种实现对象匹配的方式是样例类。
- `case class className`定义样例类，会直接将打包`apply`和拆包`unapply`的方法直接定义好。
- 样例类定义中主构造参数列表中的`val`甚至都可以省略，如果是`var`的话则不能省略，最好加上的感觉，奇奇怪怪的各种边角简化。

```scala
// 这是不使用样例类的方法
package scala.chapter8

object Test04_MatchObject {

  def main(args: Array[String]): Unit = {

    val stu1 = new Student("Ryze", 19)

    val res = stu1 match {
      case Student("Ryze", 19) => println("yes")  // 匹配成功时输出 "yes"
      case _ => println("no")  // 其他情况输出 "no"
    }
    // 由于case语句中的println已经输出了内容，res 其实是 Unit 类型
    println(res)  // 这里会打印出 res 的结果，实际上是 `()`，因为 println 输出了 Unit
  }
}

class Student(val name: String, val age: Int)

// 定义伴生对象
object Student {
  def apply(name: String, age: Int): Student = new Student(name, age)

  // 定义 unapply 方法进行模式匹配解构
  def unapply(student: Student): Option[(String, Int)] = {
    if (student == null) {
      None
    } else {
      Some((student.name, student.age))  // 返回 Some 包裹的元组
    }
  }
}

```

以下代码为使用样例类

```scala
// 使用样例类

package scala.chapter8

object Test05_MatchCaseClass {

  def main(args: Array[String]): Unit = {

    val stu1 = Student1("Kasha", 21)
    val res = stu1 match {
      case Student1("Kasha", 21) => "yes"
      case _ => "no"
    }
    println(res)
  }
}

// 定义样例类后，apply 和 unapply方法自动生成
case class Student1(val name: String, val age: Int)
```



**样例类详解**：

`case class` 是 Scala 提供的一种类，通常用于不可变对象的定义。与普通类不同，样例类在编译时会自动生成一些常用的方法，如：

- `apply` 方法，用于简化对象的创建。
- `unapply` 方法，用于在模式匹配中提取对象的内容。
- `toString` 方法，用于返回对象的字符串表示。
- `equals` 和 `hashCode` 方法，用于比较对象是否相等。





### 8.4 声明变量中的模式匹配

- 变量声明也可以是一个模式匹配的过程。
- 元组常用于批量赋值。
- `val (x, y) = (10, "hello")`
- `val List(first, second, _*) = List(1, 3, 4, 5)`
- `val List(first :: second :: rest) = List(1, 2, 3, 4)`





### 8.5 for表达式中的模式匹配

`for`推导式中也可以进行模式匹配：

- 元组中取元素时，必须用`_1 _2 ...`，可以用元组赋值将元素赋给变量，更清晰一些。
- `for ((first, second) <- tupleList)`
- `for ((first, _) <- tupleList)`
- 指定特定元素的值，可以实现类似于循环守卫的功能，相当于加一层筛选。比如`for ((10, second) <- tupleList)`
- 其他匹配也同样可以用，可以关注数量、值、类型等，相当于做了筛选。
- 元组列表匹配、赋值匹配、`for`循环中匹配非常灵活，灵活运用可以提高代码可读性。



## 9 异常

scala异常处理整体上的语法和底层处理细节和java非常类似。

java异常处理如下：

- 用`try`语句包围要捕获异常的块，多个不同`catch`块用于捕获不同的异常，`finally`块中是捕获异常与否都会执行的逻辑。

```java
package scala.chapter8;

public class Test06_ExceptionJava {
    public static void main(String[] args) {

        try{
            int a = 10;
            int b = 0;
            int c = a/b;
        } catch (ArithmeticException e) {
            e.printStackTrace();
        } catch (Exception e){
            e.printStackTrace();
        } finally {
            System.out.println("finally");
        }
    }
}
/* 代码执行输出如下：
java.lang.ArithmeticException: / by zero
	at scala.chapter8.Test06_ExceptionJava.main(Test06_ExceptionJava.java:9)
finally
 */

```

scala的异常处理如下：

- `try`包围要捕获异常的内容，`catch`仅仅是关键字，将捕获异常的所有逻辑包围在`catch`块中。`finally`块和java一样都会执行，一般用于对象的清理工作。
- scala中没有编译期异常，所有异常都是运行时处理。
- scala中也是用`throw`关键字抛出异常，所有异常都是`Throwable`的子类，`throw`表达式是有类型的，就是`Nothing`。`Nothing`主要用在一个函数总是不能正常工作，总是抛出异常的时候用作返回值类型。
- java中用了`throws`关键字声明此方法可能引发的异常信息，在scala中对应地使用`@throws[ExceptionList]`注解来声明，用法差不多。

```scala
package scala.chapter8

object Test06_ExceptionScala {

  def main(args: Array[String]): Unit = {

    try {
      val n = 10 / 0
    } catch {
      case e: ArithmeticException => println(s"ArithmeticException raised.")
      case e: Exception => println(s"Exception raised.")
    } finally {
      println("finally")
    }
  }
}

/* 最终输出如下
ArithmeticException raised.
finally
 */
```





## 10 隐式转换

前面说了很多了，编译器做隐式转换的时机：

- 编译器第一次编译失败时，会在当前环境中查找能让代码编译通过的方法，将类型隐式转换，尝试二次编译。

### 10.1 隐式函数

隐式函数：

- 函数定义前加上`implicit`声明为隐式转换函数。
- 当编译错误时，编译器会尝试在当前作用域范围查找能调用对应功能的转换规则，这个过程由编译器完成，称之为隐式转换或者自动转换。

```scala
package scala.chapter09


/*
测试学习隐式转换：隐式函数 隐式参数 隐式类
 */
object Test02_Implicit {

  def main(args: Array[String]): Unit = {

    // 比如我想调用myMax函数进行数据大小比较 12.myMax(25)
    // 这里如果想正常调用下述方法，显然需要先 new MyRichInt(12) 然后比较，示例代码如下
    // val mint = new MyRichInt(12)
    // println(mint.myMax(9))

    // 隐式函数
    implicit def convert(n: Int):MyRichInt = new MyRichInt(n)
    println(13.myMax(9))

    /* 输出如下，结果显示输出了MyRichInt的构造函数
    executed method MyRichInt
    13
     */
  }
}

// 自定义类
class MyRichInt(val self: Int){

  println("executed method MyRichInt")

  def myMax(n:Int): Int = if (n>self) n else self

  def myMin(n:Int): Int = if (n<self) n else self
}

```





### 10.2 隐式参数

- 普通方法或者函数中的参数可以通过`implicit`关键字声明为隐式参数，调用方法时，如果传入了，那么以传入参数为准。如果没有传入，编译器会在当前作用域寻找符合条件的隐式值。例子：集合排序方法的排序规则就是隐式参数。
- 隐式值：
  - 同一个作用域，相同类型隐式值只能有一个。
  - 编译器按照隐式参数的类型去寻找对应隐式值，与隐式值名称无关。
  - 隐式参数优先于默认参数。（也就是说隐式参数和默认参数可以同时存在，加上默认参数之后其实就相当于两个不同优先级的默认参数）
- 隐式参数有一个很淦的点：
  - 如果参数列表中只有一个隐式参数，无论这个隐式参数是否提供默认参数，那么如果要用这个隐式参数就应该**将调用隐式参数的参数列表连同括号一起省略掉**。如果调用时又想加括号可以在函数定义的隐式参数列表前加一个空参数列表`()`，那么`()`将使用隐式参数，`()()`将使用默认参数（如果有，没有肯定编不过），`()(arg)`使用传入参数。
  - 也就是说一个隐式参数时通过是否加括号可以区分隐式参数、默认参数、传入参数三种情况。
  - 那么如果多参数情况下：隐式参数、默认参数、普通参数排列组合在一个参数列表中混用会怎么样呢？没有试验过，不要这么用，思考这些东西搞什么哦！
  - 具体要不要加这个柯里化的空参数列表，那看习惯就行。不加可能更好一点，加了可能有点让人费解。
- 可以进一步简写隐式参数，在参数列表中直接去掉，在函数中直接使用`implicity[Type]`（`Predef`中定义的）。但这时就不能传参数了，有什么用啊？相当于一个在自己作用域范围内起作用的全局量？

```scala
// 隐式参数找的不是相同的变量名，而是相同的类型 所以说：同一个作用域，相同类型隐式值只能有一个。

package scala.chapter09

object Test02_ImpParam {

  def main(args: Array[String]): Unit = {

    implicit val ss: String = "ryze"
    def fun1()(implicit name: String):Unit = {
      println(s"fun1 , name is : ${name}")
    }

    def fun2(name: String): Unit = {
      println(s"fun2 , name is : ${name}")
    }

    fun1
  }
}

```



### 10.3 隐式类

- scala2.10之后提供了隐式类，使用`implicit`声明为隐式类。将类的构造方法声明为隐式转换函数。
- 也就是说如果编译通不过，就可能将数据直接传给构造转换为对应的类。
- 隐式函数的一个扩展。
- 说明：
  - 所带构造参数有且只能有一个。
  - 隐式类必须被定义在类或者伴生对象或者包对象中，隐式类不能是顶层的。
- 同一个作用域定义隐式转换函数和隐式类会冲突，定义一个就行。

示例代码定义隐式类：

```scala
package scala.chapter09

object Test02_ImpClass {

  def main(args: Array[String]): Unit = {

    implicit class MyRichInt(val self: Int) {

      println("executed method MyRichInt")

      def myMax(n: Int): Int = if (n > self) n else self

      def myMin(n: Int): Int = if (n < self) n else self
    }

    println(13.myMax(9))
  }
}

```



### 10.4 隐式解析机制

隐式解析机制的作用域：

- 首先在**当前代码作用域下**查找隐式实体（隐式方法、隐式类、隐式对象）。
- 如果第一条规查找隐式对象失败，会继续在**隐式参数的类型的作用域**中查找。
- 类型的作用域是指该类型相关联的全部伴生对象以及该类型所在包的包对象。

作用：

- 隐式函数和隐式类可以用于扩充类的功能，常用语比如内建类`Int Double String`这种。
- 隐式参数相当于就是一种更高优先级的默认参数。用于多个函数需要同一个默认参数时，就不用每个函数定义时都写一次默认值了。为了简洁无所不用其极啊真是。





## 11 泛型

泛型：

- `[TypeList]`，定义和使用都是。
- 常用于集合类型中用于支持不同元素类型。
- 和java一样通过类型擦除/擦拭法来实现。
- 定义时可以用`+-`表示协变和逆变，不加则是不变。

```
class MyList[+T] {} // 协变
class MyList[-T] {} // 逆变
class MyList[T] {} // 不变
```

协变和逆变：

- 比如`Son`和`Father`

  是父子关系，`Son`是子类。

  - 协变（Covariance）：`MyList[Son]`是`MyList[Father]`的子类，协同变化。
  - 逆变（Contravariance）：`MyList[Son]`是`MyList[Father]`的父类，逆向变化。
  - 不变（Invariant）：`MyList[Father] MyList[Son]`没有父子关系。

- 还需要深入了解。

泛型上下限：

- 泛型上限：`class MyList[T <: Type]`，可以传入`Type`自身或者子类。
- 泛型下限：`class MyList[T >: Type]`，可以传入`Type`自身或者父类。
- 对传入的泛型进行限定。

上下文限定：

- `def f[A : B](a: A) = println(a)`等同于`def f[A](a: A)(implicit arg: B[A])`
- 是将泛型和隐式转换结合的产物，使用上下文限定（前者）后，方法内无法使用隐式参数名调用隐式参数，需要通过`implicitly[Ordering[A]]`获取隐式变量。
- 了解即可，可能基本不会用到。





## 12 内存空间



### 12.1 基础

scala 的内存分配和java一致：引用放在栈内存, 指向堆内存

![](image\scala_image\s13.png)









12 总结