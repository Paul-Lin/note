### 1.优先使用面向表达式编程
> **语句 VS 表达式 **
> 语句是可以执行的东西，表达式是可以求值的东西。

Scala的一些控制块也是表达式。这意味着如果这个控制结构是有分支的，那么每个分支也必须被计算为一个值。if语句就是个极佳的例子。if语句检查条件表达式，然后根据表达式的值返回其中一个分支的结果。

代码清单如下:
``` scala
if(true) 5 else "hello" // result is 5
if(false) 5 else "hello" // result is "hello"
```
如上所见，Scala的if是个表达式。我们的第一个if块返回5，也就是表达式true的结果。第二个if块返回hello,也就是表达式false结果。同样可在Java表达。

Java代码清单如下:
``` java
String x=true ? "true string" : "false string";
```
因此Java的if块和?:表达式的区别在于if不被运算为一个值，Java你不能把if块结果赋值给一个变量。而Scala统一了?:和if块的概念，所以在Scala里没有?:语法，你只需要用if块就够了。
** Scala绝大部分语句都返回其最后一个表达式的值作为结果 **

### 2.方法和模式匹配
用Java编程时，有个常用的实践是每个方法都有一个返回点。这意味着如果方法里有某种条件逻辑，开发者会创建一个变量存放最终的返回值。方法执行的时候，这个变量会被更新为方法要返回的值。每个方法最后一行都会是一个return语句。

Java惯用法代码清单：一个return语句
``` scala
def createErrorMessage(errorCode : Int) : String = {
	var result : String = _
    errorCode match{
    	case 1 =>
        	result = "Network Failure"
        case 2 =>
        	result = "I/O Failure"
        case _ =>
        	result = "Unknown Error"
    }
    return result
}
```
如上所见，result用来存放最终结果。代码流过一个模式匹配，相应设置出错字符串，然后返回结果。我们可以用模式匹配提供的面向表达式语法稍微改进一下代码。

事实上，模式匹配上返回一个值，类型为所有case语句返回的值的公共超类。如果所有模式都没有匹配到，模式匹配将会抛出异常，确保我们要么得到返回值要么出错。

使用面向表达式模式匹配技巧重写的createErrorMessage
``` scala
def createErrorMessage(errorCode : Int) : String = {
	val result= errorCode match {
    	case 1 => "Network Failure"
        case 2 => "I/O Failure"
        case 3 => "Unknown Error"
    }
    return result
}
```
1.我们把result变量改成了val,让类型推导来判断类型                       。因为我们不在需要赋值后改变result的值，模式匹配应该能够判断唯一的值（和类型）。我们不仅减少了代码的大小和复杂度，我们还增加了程序的不变性。

> 不变性:对象或变量赋值后就不再改变状态。
> 可变性:对象或变量在其生命周期中能够改变或操纵。

2.去掉了case语句里的所有赋值。case语句的最后一个表达式结果就是case语句的"结果"。我们可以在每个case语句里嵌套更深的逻辑，只要在最后的能得到某种形式的表达式结果就行。如果我们不小心忘了返回结果，或者返回结果不对，编译器也会警告我们。

实际上，用Scala开发时，开发者喜欢用最后一句表达式作为返回值。对于createErrorMessage方法，我们完全可以去掉result这个中间变量。

代码清单如下：
``` scala
def createErrorMessage(errorCode : Int) : String = match{
	case 1 => "Network Failure"
    case 2 => "I/O Failure"
    case _ => "Unknown Error"
}
```
> ** Scala规约 **
> equals和hashCode方法应该实现为如果x \== y, 则x.## == y.##，那么hashCode和equals应该总是成双成对实现。
>
>
>
>以下9个都是scala.AnyVal的子类型
>
 数字类型      |  非数字类型
 ----------   | ---------
 scala.Double | scala.Unit
 scala.Float  | scala.Boolean
 scala.Long   | 
 scala.Int    | 
 scala.Char   | 
 scala.Short  | 
 scala.Byte   | 
 
> 那些继承自java.lang.Object的"标准"对象则都是scala.AnyRef的子类。scala.AnyRef可以看做java.lang.Object的别名。

**Any,AnyRef,AnyVal之间具体关系如下**

![](./classhierarchy.png)
> **实现判等时候，一般推荐确保如下的约束**
> * 如果两个对象相等，它们的散列值应该也相等;
> * 一个对象的散列值在对象的生命周期中不应该变化;
> * 在把对象发送到另一个JVM时，应该用两个JVM里都有的属性来相等。

### 3.用None不用null
Scala在标准库里提供了scala.Option类，鼓励大家在一般编程时尽量不要使用null。Option可以视作一个容器，里面要么有东西，要么什么都没有。Option通过两个子类来实现此含义: *Some* 和 *None*。

Some表示容器里有且仅有一个东西。

None表示空容器，有点类似List的Nil含义。

在Java和其他允许null的语言里，null经常作为一个占位符用于返回值，表示非致命的错误，或者表示一个变量未被初始化。

Scala里，你可以用Option的None子类来代表这个意思，反过来用Option的Some子类代表一个初始化了的变量或者非致命(non-fatal)的变量状态。

代码清单如下：
``` scala
var x : Option[String] = None // 创建未初始化的字符串字面量

x.get	// 访问未初始化的变量导致抛出异常

x.getOrElse("default")	 // 使用带默认值的方法访问

x = Some("Now Initialized")	// 用字符串初始化x

x.get	// 访问初始化后的变量成功

x.getOrElse("default")	// 没有使用默认值
```
**不包含任何值的对象Option用None对象来构建，包含一个值的Option用Some工厂方法来创建**

Option类中的get和getOrElse方法:

* get方法会尝试访问Option里保存的值,如果Option是空的则抛出异常。

* getOrElse也访问Option存放的值，有则返回，否则返回其参数(作为默认值)。


**应该尽量使用getOrElse而不是get**


Option工厂的应用 代码清单如下:
``` scala
var x : Option[String] = Option(null) // 创建一个None对象

x = Option("Initialized")	// 创建一个Some对象
```
如果输入是null,Option工厂方法会创建一个None对象，如果输入是初始化的值，则会创建一个Some对象。

#### Option高级技巧
Option最重要的技巧是可以被当做集合看待。这意味着你可以对Option使用标准的map,flatMap,foreach等方法，还可以用在for表达式里。

* 创建对象或返回默认值
代码清单如下：
``` scala
def getTemporaryDirectory(tmpArg:Option[String]):java.io.File={
    tmpArg.map(name => new java.io.File(name))
      .filter(_ isDirectory)
      .getOrElse(new java.io.File(System getProperty("java.io.tmpdir")))
}
```
* 如果变量已初始化则执行代码块
代码清单如下:
``` scala
def getForEach(): Unit ={
    val username:Option[String]=Option(null)
    for(name <- username){
      println("Here never run!")
    }

    val nickname=Option("Paul")
    for(name <- nickname){
      println("Nickname: "+name)
    }
  }
```
* 多态判等实现
一般来说，最好避免在需要深度判等情况下使用多态。Scala语言自身就出于这个原因不再支持case class的子类继承。

代码清单如下:
``` scala
trait InstantaneousTime {
  val repr:Int
  override def equals(other:Any)=other match{
    case that:InstantaneousTime =>
      if(this eq that){
        true
      }else{
        (that.## == this.##
          && (repr == that.repr))
      }
    case _ => false
  }
  override def hashCode()=repr.##
}

trait Event extends InstantaneousTime{
  val name:String
  override def equals(other:Any)=other match{
    case that:Event=>
      if(this eq that){
        true
      }else{
        (repr == that.repr
          && (name == that.name))
      }
    case _ => false
  }
}

object TimeEvent{
  def main(args: Array[String]) {
    val x=new InstantaneousTime {
      override val repr: Int = 2
    }
    val y=new Event{
      override val name: String = "TestEvent"
      override val repr: Int = 2
    }
    println(x == y)	// true
    println(y == x)	// false
  }
}
```
**Q&A: 为什么打印出来为true?**
旧的类使用旧的判等方法，因此没检查新的name字段，我们需要在修改基类里最初的判等实现，以便考虑到子类可能希望修改判等的实现方法

解决方法：在Scala里，有个scala.Equals特质能帮我们修复这个问题。Equals特质定义了一个canEqual方法，可以和标准的equals方法串联起来使用。通过让equals方法的other参数有机会直接造成判断失败，canEqual方法使子类可以跳出其父类的判等实现。为此只需要在我们的子类里覆盖canEqual方法，注入我们想要的任何判断标准。

代码清单如下：
``` scala
trait InstantaneousTime extends Equals{
  val repr:Int

  override def canEqual(other:Any)=other.isInstanceOf[InstantaneousTime]

  override def equals(other:Any)=other match{
    case that:InstantaneousTime =>
      if(this eq that){
        true
      }else{
        (that.## == this.##
          && (that canEqual this)
          && (repr == that.repr))
      }
    case _ => false
  }
  override def hashCode()=repr.##
}

trait Event extends InstantaneousTime{
  val name:String
  override def canEqual(other:Any)=other.isInstanceOf[Event]

  override def equals(other:Any)=other match{
    case that:Event=>
      if(this eq that){
        true
      }else{
        (repr == that.repr
          && (name == that.name))
      }
    case _ => false
  }
}

object TimeEvent{
  def main(args: Array[String]) {
    val x=new InstantaneousTime {
      override val repr: Int = 2
    }
    val y=new Event{
      override val name: String = "TestEvent"
      override val repr: Int = 2
    }
    println(x == y)	// false
    println(y == x)	// false
  }
}
```

这里我们做的第一件事是在InstaneousTime里实现canEqual，当other对象也是InstantaneousTime时返回true。然后我们在equals实现里考虑到other对象的canEqual结果。最后，Event类里覆盖canEqual方法，使Event只能和其他的Event做判等。

**在覆盖父类的判等方法时，同时覆盖canEqual方法**
> canEqual方法是个控制杆，允许子类跳出父类的判等实现。这样子类就可以安全地覆盖父类的equals方法，而避免父类与子类的判等方法对相同的两个对象给出不同的结果。


