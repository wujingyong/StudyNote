# 一、基础
[1. 基本数据类型](#jbsjlx)  
[2. for循环](#for)  
[3. 方法和函数](#function)  
[4. 数组](#array)  

> [4.1 定长数组](#immutableArray)  
> [4.2 可变数组](#mutableArray) 

[5. 映射](#map)  
[6. 元组](#tuple)  
[7. 操作符](#czf)
[8. 集合](#collection)  

>[8.1 Seq（序列）](#sequence)  
>[8.2 Set（集）](#set)  
>[8.3 Map（映射）](#collectionMap)  

[练习1](#practice1)
[练习2](#practice2)

# 二、面向对象
[1. 构造器](#constructor)  
[2. 单例模式](#singleton)  
[3. 伴生对象](#bsdx)  
[4. apply和unapply](#applyUnapply)  
[5. private关键字](#private)  
[6. 特质、抽象类、继承、实现、重写](#trait)  
[7. 模式匹配](#match)  

>[7.1 匹配字符串、类型、数组、元素、集合](#match)  
>[7.2 模式匹配之样例类](#caseClass)  
>[7.3 模式匹配之偏函数](#PartialFunction)  

# 三、高级函数
[1. 柯里化](#currying)  
[2. 隐式转换](#implicit)  
[3. 泛型](#fanxing)  

<span id='jbsjlx'></span>
## 7大基本数据类型（无类型引用）
`Byte` `Char` `Short` `Int` `Long` `Float` `Double`

<span id='for'/>

## for循环

```
for(i <- 1 to 10){}  //1-10
//多层for嵌套
for(i <- 1 to 3;j <- 1 to 3){println("i => " + i + "j =>" + j)}
//yield将每次迭代生成放入集合
val res = for(i <- 1 until 10) yield i
```
<span id='function'/>

## 方法和函数声明  方法转函数

```scala
//方法f1有两个Int参数，返回类型为Int，结果为x+y
def m1(x : Int, y : Int) : Int = x + y

//函数
val f1 = (x : Int,y : Int) => x + y

//函数作为方法的参数  (感觉有点像js)
def m2(f : (Int,Int) => Int,x : Int,y : Int) : Int = f(x,y)

//方法转函数
m1 _ == f1

//scala会将方法隐士转换为函数
m2(f1,2,3) == m2(m1,2,3) //相等
```
<span id='array'/>

## 数组
<span id='immutableArray'/>
###定 长数组

```scala
//定长数组,长度为5
val array1 = new Array[Int](5)

//固定值数组
val array2 = Array(1,2,3,4,5)

//取下标为1的值
println(array2(0))

//取全部值
println(array2.toBuffer)
```
<span id='mutableArray' />

### 变长数组,需要导入 scala.collection.mutable.ArrayBuffer

```scala
val arrayBuffer = new ArrayBuffer[int]()

//添加、删除值(从左开始删，删存在的)
arrayBuffer += 1
arrayBuffer -= 1

//添加、删除多个值
arrayBuffer += (1,2,3)
arrayBuffer -= (1,2,3)

//添加或删除数组、可变数组
arrayBuffer ++= Array(1,2,3)
arrayBuffer --= ArrayBuffer(1,2,3)

//插入数值  从0的位置开始插入1,2,3
arrayBuffer.insert(0,1,2,3)

//删除数值  从0位置开始删，删两个,不够删报错
arrayBuffer.remove(0,2)
```

<span id='map'></span>

## 映射（Map）

```scala
//新建一个Map（值不可变）
val map = Map("scala" -> 1)
val map = Map(("scala",1))

//获取值
map("scala")  //若没有这个key会报错
map.getOrElse("scala",-1)  //若有值返回该值，若无值返回-1
map.getOrUpdate("scala",1)  //若有值返回该值，若无值则插入并返回

//若要建可变的Map 需要导入 import scala.collection.mutable.Map
//改变值
map("scala") = 2

//插入值
map.+=(("scala",1))
map.+=("scala" -> 1)

```

<span id="tuple"></span>

## 元组

```scala
//新建一个元组
val tuple = ("scala",1,10L,1.0,("tuple",1))

//元组取值取"tuple"  取第五个元素的第一个值
tuple._5._1

//数组转map
val array = Array(("scala",1),("java",2))
array.toMap  // scala.collection.immutable.Map[String,Int] = Map(scala -> 1, java -> 2)

//拉链操作 一一对应，对应不上的就不对应
val array1 = Array("scala","java")
val array2 = Array(1,2,3,4)
array1 zip array2  //Array[(String, Int)] = Array((scala,1), (java,2)) 
```

<span id="czf"></span>

## scala 四种操作符

>1. :: 该方法被称为cons，意为构造，向队列的头部追加数据，创造新的列表。用法为 x::list,其中x为加入到头部的元素，无论x是列表与否，它都只将成为新生成列表的第一个元素，也就是说新生成的列表长度为list的长度＋1(btw, x::list等价于list.::(x))

>2. :+和+: 两者的区别在于:+方法用于在尾部追加元素，+:方法用于在头部追加元素，和::很类似，但是::可以用于pattern match ，而+:则不行. 关于+:和:+,只要记住冒号永远靠近集合类型就OK了。

>3. ++ 该方法用于连接两个集合，list1++list2

>4. ::: 该方法只能用于连接两个List类型的集合

<span id="collection"></span>

## 集合

<span id="sequence"></span>

### 序列（sequence）
#### 不可变序列

```scala
//新建一个不可变序列
val seq = List(1,2,3,4)

//添加值得到一个新的list
0 :: seq  // List(0, 1, 2, 3, 4)
seq.::(0)  // List(0, 1, 2, 3, 4)
0 +: seq  // List(0, 1, 2, 3, 4)
seq :+ 0  // List(1, 2, 3, 4 ,0)
seq.:+(0)  // List(1, 2, 3, 4, 0)
seq.+:(0)  // List(0, 1, 2, 3, 4)
seq ++:/++ List(5,6,7,8)  // List(1, 2, 3, 4, 5, 6, 7, 8)
seq.++(List(5,6,7,8))  //List(1, 2, 3, 4, 5, 6, 7, 8)
seq.++:(List(5,6,7,8))  // List(5, 6, 7, 8, 1, 2, 3, 4)
```

#### 可变序列

```scala
//新建一个可变序列
val listBuffer = ListBuffer(1,2,3)

//添加值
listBuffer += 4  // ListBuffer(1, 2, 3, 4)
listBuffer.append(5) //ListBuffer(1, 2, 3, 4, 5)

```

<span id="set"></span>

### 集（set） 默认不可变

```scala
//新建一个set
val set = Set(1,2,3)
val set = HashSet(1,2,3,4,1)

//添加值
set += 5
set.add(6)

//删除元素
set -= 5
set.remove(5)  //返回是否移除成功，若没有该值返回false
```

<span id="collectionMap"></span>

### 映射（map）

```scala
//新建一个map
val map = HashMap[String,Int]()

//添加元素
map.put("scala",1)

//移除数据
map -= "scala"
map.remove("scala")
```

<span id="practice1"></span>

## 练习1

```scala
object AllTest {
  def main(args: Array[String]): Unit = {
    val list0 = List(0,3,2,1,6,5,7,4,9,8)
    val list1 = list0.map(_*2);
    println(list1)
    val list2 = list0.filter(_ % 2 == 0)
    println(list2)
    //排序
    val list3 = list0.sorted
    println(list3)
    //反转
    val list4 = list3.reverse
    println(list4)

    //分组 4个一组
    val it = list0.grouped(4);
//    println(it.toBuffer)

    println("----------")

    //将Iterator 转 list
    val list5 = it.toList
    println(list5)

    //将多个list压扁成一个list
//    val list6 = list5.flatMap(_.toList)
    val list6 = list5.flatten
    println(list6)

    //先按空格切分再压平
    val lines = List("hello java","hello scala","hello python")
//    val words = lines.map(_.split(" "))
//    println(words)
//    val words2 = words.flatten
//    println(words2)
    val words = lines.flatMap(_.split(" "))
    println(words)

    //并行求和
    //和线程有关，每个线程计算一部分
    val sum = list0.par.sum
    println(sum)

    //按照特定的顺序进行聚合
    val sum3 = list0.reduce(_+_)
    println(sum3)

    //并行的方式进行聚合
    val sum4 = list0.par.reduce(_-_)
    println(sum4)

    println("------------")
    //折叠（有初始值，无特定顺序）
    val res = list0.par.fold(10)(_+_)
    println(res)

    println("------------")
    //折叠（有初始值，有特定顺序）
    val res2 = list0.par.foldLeft(10)(_-_)
    println(res2)
    println("------------------")

    //聚合
    val list7 = List(List(1,2,3),List(3,4,5),List(2),List(0))
    val res3 = list7.flatten.reduce(_+_)
    println(res3)

    val res4 = list7.aggregate(0)(_+_.sum,_+_)
    println(res4)
    println("-------------------")



    val l1 = List(4,5,6,7)
    val l2 = List(1,2,3,4)

    //求并集
    val res5 = l1 union l2
    println(res5)

    //求交集
    val res6 = l1 intersect l2
    println(res6)

    //求差集
    val res7 = l1 diff l2
    println(res7)
  }
}
```

<span id="practice2"></span>

## 练习2 wordConunt

```scala
object WordCount {
  def main(args: Array[String]): Unit = {
    val lines = List("hello java hello python","hello scala","hello scala hello java hello scala")
    val wordMap = lines.flatMap(_.split(" ")).map((_,1))
    //分组
    val group = wordMap.groupBy(_._1)

    //求和
//    val res = group.map(x => (x._1,x._2.size))
    val grouped = group.mapValues(_.size)

    //排序
    var res = grouped.toList.sortBy(_._2)

    //升序

    res = res.reverse
    println(res)
  }
}

```

<span id="constructor"></span>

## 构造器

```scala
package com.wjy.object2scala

class Person {
  private var id : Int = _
  private var name : String = _
  private var age : Int = _

  //辅助构造函数
  def this(id : Int,name : String,age : Int){
    this()
    this.id = id
    this.name = name
    this.age = age
  }

  //辅助构造函数
  def this(name : String,age : Int){
    this()
    this.name = name
    this.age = age
  }
  override def toString = s"Person($id, $name, $age)"
}

//不用val也不用var修饰的构造参数，只能在本类中调用,其他类想调用，得用方法，且默认val修饰，值不可改变
class Person2(id : Int,var name : String){
  var age : Int = _
  def this(id : Int, name : String, age : Int){
    this(id,name)
    this.age = age
  }

  def getId()={
    id
  }

  override def toString = s"Person2($age, $name)"
}

object Main{
  def main(args: Array[String]): Unit = {
    val p = new Person(12,"zs",13)
    println(p)

    val p2 = new Person2(1,"aa")
    println(p2)
    println(p2.getId)
  }
}

```

<span id="singleton"></span>

## 单例模式

```scala
/**
  * scala中没有静态方法和静态字段，但可以用object关键字加类名的语法结构实现同样的功能
  * 1.工具类：存放常量和工具方法
  * 2. 实现单例模式
  */
object SingletonDemo {
  println("执行静态方法")

  private var i = 5

  val array = new ArrayBuffer[String]()

  while (i > 0){
    array += i + "-->"
    i-=1
  }

}

object Singleton {
  def main(args: Array[String]): Unit = {
    //对s和s2的操作，都是对同一静态类的操作
    val s = SingletonDemo
    println(s.array)
    s.array.remove(0)
    val s2 = SingletonDemo
    println(s2.array)
  }
}
```

<span id="bsdx"></span>

## 伴生对象

```scala
/**
  * 与类名相同并且用object修饰的对象叫做伴生对象
  * 类和其伴生对象之间可以互相访问私有的方法和属性
  */
class Dog {
  private var name : String = "金毛"

  def printName={
    println(name + Dog.CONSTANT)
  }

}

object Dog {
  private val CONSTANT = "你好啊"
  def main(args: Array[String]): Unit = {
    val dog = new Dog
    println(dog.name)
    dog.printName
  }
}
```

<span id="applyUnapply"></span>

## apply和unapply

```scala
/**
  * apply 方法通常称为注入方法，在伴生对象里做一些初始化操作
  * apply 的参数列表不需要和构造器的参数列表统一
  * unapply 方法通常称为提取方法，使用upapply方法来提取固定数量的对象
  * unapply 方法会放回一个序列（Option），内部生成一个Some对象来存放一些值
  * apply方法和unpaaly方法会被隐式调用
  */
class ApplyUnapply(val name : String, var age : Int) {

}

object ApplyUnapply {
  def apply(name : String, age : Int) = new ApplyUnapply(name, age)

  def unapply(applyUnapply: ApplyUnapply) = {
    if(applyUnapply == null){
      None
    } else {
      Some(applyUnapply.name, applyUnapply.age)
    } 
  }
}

object ApplyUnapplyTest {
  def main(args: Array[String]): Unit = {
    val applyUnapply = ApplyUnapply("zs",11)
    applyUnapply match {
      case ApplyUnapply("zs", age) => println(s"age: $age")
      case _ => println("No match nothing")
    }
  }
}
```

<span id="private"></span>

## private关键字

```scala
/**
  * 类名前加private表示包访问权限，指定包及其子包可以访问，不指明默认单前包
  * 构造器参数列表前加private（其他类初始化不报错，运行时报错） 或 辅助构造函数 前加private 是指伴生对象的权限，只有伴生对象才能访问
  */
private[wjy] class PrivateDemo private() {



  //私有字段,单前类和伴生对象可以访问
  private var name = "zs"

  private def this(name : String){
    this()
    this.name = name
  }

  //私有方法,单前类和伴生对象可以访问
  private def printName() = {
    println(name)
    println(age)
    printAge()
  }

  //私有字段,仅单前类可以访问
  private[this] val age = 12

  //私有方法,仅单前类可以访问
  private[this] def printAge() = {
    println(age)
  }

}

object PrivateDemo {
  def main(args: Array[String]): Unit = {
    val p = new PrivateDemo("aa")
    println(p.name)
    p.printName()
  }
}
```

<span id="trait"></span>

## 特质、抽象类、继承、实现、重写

```scala
/**
  * 特质
  */
trait Bird{
  //声明一个没有值的字段
  val name : String

  //声明一个没有实现的方法
  def printName : String

  //声明一个实现的方法
  def fly = {
    println("I can fly")
  }
}

/**
  * 抽象类
  */
abstract class Animal{
  //声明一个没有值的字段
  val name : String
  val age : Int

  //声明一个没有实现的方法
  def printAge : Int

  //声明一个实现的方法
  def walk = {
    println("walk with foot")
  }
}

//class Student extends Bird{  //若就实现一个接口，用extends
class Student extends Animal with Bird{
  override val age: Int = 2

  override def printAge: Int = 2

  override val name: String = "张三"

  override def printName: String = name

  //可以重写特质实现了的方法
  override def fly: Unit = println("I can't fly")

  //可以重写抽象类的实现了的方法
  override def walk: Unit = println("yes i walk with my foot")
}

object ClassDemo {
  def main(args: Array[String]): Unit = {
    val student = new Student
    println(student.printName)
    println(student.printAge)
    student.fly
    student.walk
  }
}
```

<span id="match"></span>

## 模式匹配
<span id="matchList"></span>

### 匹配字符串、类型、数组、元素、集合

```scala
//模式匹配
//匹配字符串
object MatchStr {
  def main(args: Array[String]): Unit = {
    val arr = Array("zhangsan","lisi","wangwu","zhaoliu")
    val name = arr(Random.nextInt(arr.length))
    println(name)
    name match {
      case "zhangsan" => println("张三")
      case "lisi" => println("李四")
      case "wangwu" => println("王五")
      case _ => println("nothing match")
    }
  }
}

private[this] class  MatchTest{}

//匹配类型
object MatchType {
  def main(args: Array[String]): Unit = {
    val arr = Array("abc",100,3.14,true,new MatchTest)
    val element = arr(Random.nextInt(arr.length))
    element match {
      case str : String => println(s"mathc String $str")
      case int : Int => println(s"mathc Int $int")
      case bool : Boolean => println(s"mathc Boolean $bool")
      case matchTest : MatchTest => println(s"mathc String $matchTest")
      case _ : Any => println("nothing match")
    }
  }
}

//匹配数组、元组、集合
object MatchList {
  def main(args: Array[String]): Unit = {
    //匹配数组
    val arr = Array(1,2,3,4)
    arr match {
      case Array(2,x,y,_) => println(s"case1 $x $y")
      case Array(x,2) => println(s"case2 $x")
      case Array(x,2,y,z) => println(s"case2 $x $y $z")
      case _ => println("nothing match")
    }

    //匹配元组
    val tuple = (1,"2",3.14)
    tuple match {
      case (2,x,y) => println(s"case1 $x $y")
      case (1,x,3) => println(s"case2 $x")
      case (1,x,y) => println(s"case3 $x $y")
      case _ => println("nothing")
    }

    //匹配集合
    val list = List(1,3.14,"test")
    list match {
//      case 1 :: x => println(s"case1 $x")  //第一个匹配上后，会将x当为一个数组匹配成功
      case 1 +: x +: Nil => println(s"case2 $x")  //后面跟Nil可以防止将x当为一个数组 Nil空
      case List(1,x) => println(s"case3 $x")  //不会将x作为一个数组匹配
      case List(1,x,y,z) => println(s"case4 $x $y $z")
      case List(1,x,y) => println(s"case5 $x $y")
      case _ => println("nothing")
    }
  }
}
```

<span id="caseClass"></span>

### 样例类

```scala
object CaseClassDemo {
  def main(args: Array[String]): Unit = {
    val arr = Array(CheckTimeOutTask, SubmitTask("1001","task_1001"), HeartBeat(1000))
    arr(Random.nextInt(arr.length)) match {
      case CheckTimeOutTask => println("CheckTimeOutTask")
      case SubmitTask(port,taskName) => println(s"SubmitTask $port $taskName")
      case HeartBeat(100) => println(s"HeartBeat")
      case HeartBeat(time) => println(s"HeartBeat $time")
      case _ => println("nothing")
    }
  }
}

case class HeartBeat(time : Long)
case class SubmitTask(id : String, taskName : String)
case object CheckTimeOutTask
```

<span id="PartialFunction"></span>

### 偏函数

```ascala
/**
  * PartialFunction[A, B]，其中A是参数类型，B是返回值类型，PartialFunction（偏函数）常用作输入模式匹配
  */
object PartialFunctionDemo {
  def m1: PartialFunction[String,Int] = {
    case "one" => 1
    case "two" => 2
    case x => {
      println(x)
      0
    }
  }

  //m1的另一种写法
  def m2(num : String) : Int = num match {
    case "one" => 1
    case "two" => 2
    case _ => 0
  }

  def main(args: Array[String]): Unit = {
    println(m1("one"))
    println(m2("two"))
  }

}

```

<span id="currying"></span>

### 柯里化

```scala
/**
  * 柯里化,将多个参数转换为一个参数
  */
object Curry {
  //第一种声明柯里化方式
  def currying (x : Int)(y : Int) = x * y
  
  //第二种声明柯里化方式
  //currying的另一种写法
  def curry4(x : Int)  = (y : Int)  => x * y

  def curry2 (y : Int) = currying(2)(y)
  //等同于curry2
  def curry3 = currying(2)_


  //隐式固定值
  def m2 (x: Int)(implicit y : Int = 2) = x * y


  def main(args: Array[String]): Unit = {
    println(currying(5)(2))  //10
    val curry = curry4(5)
    println(curry(2))   //10
    println(curry2(5))  //10
    println(curry3(5))  //10

    println(m2(5))  //10
    println(m2(5)(3)) //15

    //设置一个隐式值(相同类型的隐式值只能设置一个，否则会报错，不知道你要用那个隐式值)
    implicit val x = 20
    println(m2(5))  //100

    val arr = Array(("one",1),("two",2),("three",3))
    println(arr.map(_._2).reduce(_+_))
    println(arr.foldLeft(0)(_+_._2))
  }
}
```
#### 柯里化的一个例子

```scala
object Context{
  implicit val a = "java"
  implicit val b = "python"
}

object CurryingDemo {
  def m1(str : String)(implicit name : String = "scala") = str + name

  def main(args: Array[String]): Unit = {
    println(m1("Hi ~ "))  //Hi ~ scala
    // Context 若放在同一个类中，Context单例要放在本类的上面,不然会找不到Context.a这个隐式变量，从而默认为scala
    import Context.a
    println(m1("Hi ~ "))  //Hi ~ java
//    import Context.b  //不能导入了，再导入就会有两个相同类型的隐式变量，系统不知道要用哪一个就会报错
  }
}

```

<span id="implicit"></span>

### 隐式转换和隐式参数

>能够丰富现有类库的功能，对类的方法进行增强

#### 隐式实现文件阅读

```scala
//实现隐式转换，将file隐式转换为new RichFile(file)
object MyRead{
  implicit def readFile(file : String) = new RichFile(file)
}

class RichFile(val file : String){
  def read() = Source.fromFile(file).mkString
}

object RichFile {
  def main(args: Array[String]): Unit = {
    val file = "fileName"
    //显示调用方法
    val result = new RichFile(file).read()
    println(result)

    //隐式调用
    import MyRead.readFile
    val result2 = file.read()
    println(result2)
  }
}
```

#### 隐式实现比较两个自定义对象

```scala
//定义隐式转换
object ImplictContext{
  implicit object OrderingGirl extends Ordering[Girl]{
    override def compare(x: Girl, y: Girl): Int = x.faceValue - y.faceValue
  }
}

class Girl(val name : String,val faceValue : Int){
  override def toString = s"Girl($name, $faceValue)"
}

class Goddess[T : Ordering](val v1 : T, val v2 : T){
  def chose(implicit ordering: Ordering[T]) = if(ordering.gt(v1,v2)) v1 else v2
}

object Goddess {
  def main(args: Array[String]): Unit = {
    val g1 = new Girl("lili",92)
    val g2 = new Girl("jingjing", 93)
    //导入隐式转换
    import ImplictContext.OrderingGirl
    println(new Goddess[Girl](g1,g2).chose)
  }
}
```

<span id="fanxing"></span>
### 泛型
[B <: A] UpperBound 上界：B类型的上界是A类型，即B类型的父类是A类型
[B >: A] LowerBound 下界：B类型的下界是A类型，即B类型的子类是A类型
[B <% A] ViewBound 表示B类型要转换成A类型，需要一个隐式转换函数
[B : A] ContextBound 需要一个隐式转换的值
[-A, +B] 

> [-A] 逆变，作为参数类型。如果A是T的子类，那么C[T] 是 C[A]的子类
> [+B] 协变，作为返回类型。如果B是T的子类，那么C[T] 是 B[T]的子类

#### UpperBound  

>[B <: A] UpperBound 上界：B类型的上界是A类型，即B类型的父类是A类型  

```scala
/**
  * 上界 UpperBound
  */
class Goddess2(val name : String, val faceValue : Int) extends Comparable[Goddess2]{
  override def compareTo(o: Goddess2): Int = {
    Integer.compare(this.faceValue,o.faceValue)
  }

  override def toString: String = s"($name, $faceValue)"
}

//T是Comparable[T]的子类
class UpperBoundDemo[T <: Comparable[T]] {
  def select(first : T, second : T) = if(first.compareTo(second) < 0 ) second else first
}

object UpperBoundDemo {
  def main(args: Array[String]): Unit = {
    val u = new UpperBoundDemo[Goddess2]
    val g1 = new Goddess2("zs",100)
    val g2 = new Goddess2("ls",120)
    val res = u.select(g1,g2)
    println(res)  //(ls, 120)
  }
}
```

#### ViewBound  

>[B <% A] ViewBound 表示B类型要转换成A类型，需要一个隐式转换函数  

```scala
object ViewBoundDemoContext{
  //将Girl2隐式转换为Ordered
  implicit val selectGirl = (g : Girl2) => new Ordered[Girl2] {
    override def compare(that: Girl2): Int = {
      if(Integer.compare(g.faceValue,that.faceValue) == 0)
        -Integer.compare(g.age,that.age)
      else
        Integer.compare(g.faceValue,that.faceValue)
    }
  }
}

class Girl2(val name : String, val age : Int, val faceValue : Int){
  override def toString = s"Girl2($name, $age, $faceValue)"
}

class ViewBoundDemo[T <% Ordered[T]] {
  def select(first : T, second : T) = if(first > second) first else second
}

object ViewBoundDemo{
  def main(args: Array[String]): Unit = {
    val zs = new Girl2("zs",18,100)
    val ls = new Girl2("ls",22,100)
    //导入隐式转换将T转为Ordered[T]
    import ViewBoundDemoContext.selectGirl
    val view = new ViewBoundDemo[Girl2]
    val res = view.select(zs,ls)
    println(res)  //Girl2(zs, 18, 100)
  }
}
```

#### ContextBound  

>[B : A] ContextBound 需要一个隐式转换的值  

```scala
class Girl3(val name : String, val age : Int, val faceValue : Int){
  override def toString = s"Girl3($name, $age, $faceValue)"
}

object ContextBoundContext{
  //设置一个隐式的比较方法
  implicit object OrderingGirl extends Ordering[Girl3]{
    override def compare(x: Girl3, y: Girl3): Int = Integer.compare(x.faceValue, y.faceValue)
  }
}

class ContextBoundDemo[T : Ordering] {
  def select(first : T, second : T) = {
    //定义一个隐式的比较方法
    val order : Ordering[T] = implicitly[Ordering[T]]
    if(order.gteq(first, second)) first else second
  }
}

object ContextBoundDemo{
  def main(args: Array[String]): Unit = {
    //导入隐式比较方法
    import ContextBoundContext.OrderingGirl
    val c = new ContextBoundDemo[Girl3]
    val zs = new Girl3("zs",18,120)
    val ls = new Girl3("ls",22,100)
    val res = c.select(zs, ls)
    println(res)
  }
}

```