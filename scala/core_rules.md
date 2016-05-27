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

**继承关系**
![](./classhierarchy.png)