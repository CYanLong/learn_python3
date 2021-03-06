# learn-python3
----
&emsp;&emsp;这是我初学Python时写的一套Python基础示例程序.主要基于廖雪峰老师的<a href="http://www.liaoxuefeng.com/wiki/0014316089557264a6b348958f449949df42a6d3a2e542c000">Python3教程</a>和&lt;&lt;<a href="https://book.douban.com/subject/1440658/">深入理解Python</a>&gt;&gt;.&emsp;感谢!

下面是这些示例程序的目录总结:
---
###&emsp;Chapter1：容器/集合/Collection
&emsp;&emsp;&emsp;1.字典/哈希表/dictionary/map         

&emsp;&emsp;&emsp;2.链表/list            	 

&emsp;&emsp;&emsp;3.无序表/set/tuple

&emsp;&emsp;&emsp;4.格式化字符串和字符串连接

---
###&emsp;Chapter2:Python函数和函数式编程

&emsp;&emsp;&emsp;1.Python参数(*args, **kw ..)     

&emsp;&emsp;&emsp;2.(多)返回值 - tuple      

&emsp;&emsp;&emsp;3.函数式编程&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;1).高阶函数(map,reduce,filter)

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;2).匿名函数(lambda表达式)

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;3).闭包(Closure)

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;4).装饰器(decorator)

<br/>

> 闭包(**Closure**)：

&emsp;&emsp;在python中，**函数也是对象**，函数名作为对此对象的引用。像其他数据结构一样，函数也可以赋值给变量，并且我们可以在函数中定义对象，将对象作为参数和将对象作为返回值。
	
&emsp;在这里，我们举一个例子来说明：
	
	def make_printer(msg):
		def printer():
			print(msg)
		return printer

	printer = make_printer('Foo!')
	printer() #Foo			


&emsp;&emsp;闭包是指 `内部函数(nested function)` **访问** 其 `外围函数(enclosing)`作用域的变量，并且外围函数已经执行完毕。

&emsp;&emsp;当 `make_printer` 被调用，一个新的栈帧入栈，`printer` 函数作为其常量，`msg` 的值作为局部变量被保存。然后创建并返回了函数 `printer` 。**因为函数 `printer` 引用了`msg` 变量，当 `make_printer` 函数返回(return)之后它依旧存活(kept alive)。**


&emsp;&emsp;这里面有两个关键点：存在内部函数，并且内部函数访问了外围局部变量。只有同时满足这两点，才称为闭包。

<br/>
> 装饰器(**decorator**):

&emsp;&emsp;首先，装饰器是一种设计模式。在**不改变原有代码**的基础上，将其功能模块进行包装(wrapper)，构建出更加复杂的功能模块。通常，增强了的功能模块会调用被增强的(即原有的)功能逻辑。`AOP(Aspect Oriented Programming)` 就是使用这种编程思想。

&emsp;&emsp;从技术上讲，Python的装饰器是使用闭包来实现的。并提供了简洁的语法糖支持。

&emsp;&emsp;装饰器函数接收函数作为参数，并且在内部通过定义 `wrapper` 函数来实现增强的逻辑。通常情况下，这个函数会调用原函数，并且，这个函数的参数列表应该和被增强的函数保持一致。最终，我们将这个增强了的函数(wrapper)作为返回值。

	def decorator(func):
		def wrapper(*args, **kwargs):
			maybe some code..
			func(*args, **kwargs)
			maybe some code...
		return wrapper
	
	wrapper_origin_func = decorator(origin_func)
	wrapper_origin_func(*args, **kwargs)


语法糖支持：
	
	@decorator1(args)
	@decorator2
	def func(): pass
	
	#is equivalent to:
	def func(): pass
	func = decorator1(args)(decorator2(func))


&emsp;关于装饰器带参数的解释：
	
	def decorator1(args):
		def real-decorator(func):
			def wrapper(*args, **kwargs):
				some code..
				func(*args, **kwargs)
				some code access args
			return wrapper
		return real-decorator	

---
###&emsp;Chapter3:面向对象(Oritented-Object)
&emsp;&emsp;&emsp;1.继承和多态(extends and polymorphic)     

&emsp;&emsp;&emsp;2.成员(mumber)

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;1).类成员

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;2).实例成员

&emsp;&emsp;&emsp;3.访问权限 

&emsp;&emsp;&emsp;4.专有方法(special method)

&emsp;&emsp;&emsp;5.动态修改类和slots.

&emsp;&emsp;&emsp;6.Python中的类型(type)

&emsp;&emsp;&emsp;7.元类(metaclass)

---
###Chapter4:协程 -- yield

&emsp;&emsp;我们考虑自己来实现一个数字生成器(此生成器是广义的)，即 `range()` 函数的功能。

&emsp;&emsp;首先，我们借助 `list` 通过定义最简单，最直接的函数来实现。
	
	def MyRange(n):
		num, list = 0, []
		for num < n:
			list.append(num)
			num += 1
		return list

&emsp;上面的代码借助了 `list` 对象简单粗暴的将所有可能的值都加载到内存中。这种实现方式当n很大时对内存的消耗极高甚至会发生内存溢出。为此，我们可以定义一个迭代器类，通过维护两个变量值而不是整个元素来实现**延迟计算。**

<br/>


> 迭代器(**Iterator**)
	
&emsp;&emsp;迭代器是访问容器对象(Contrain Object)元素的一种方式。

- 迭代器访问与传统的for循环索引并无优势或区别。
- 迭代器最大的功劳是提供了一个统一的访问容器对象的接口。
- 迭代器的另一个优点也是它不要求你事先准备好整个迭代过程中所有的元素。迭代器仅仅在迭代至某个元素时才计算该元素，而在这之前或之后，元素可以不存在或者被销毁。这个特点被称为**延迟计算或惰性求值(Lazy evaluation)**。

&emsp;&emsp;在Python中,与迭代器有关的内建函数有 `iter()` 和 `next()`。与可迭代对象有关的特有方法(Special method)有 `__iter__` ，`__next__`。

- 实现了 `__iter__` 特有方法，我们可以说这个对象是 `可迭代的`。

- `__next__` 特有方法用来实现具体的迭代逻辑。

- 这两个方法缺一不可，若没有实现 `__iter__` ，会报 `object is not iterable` 的 `TypeError`；若只实现了`__iter__`而没有实现`__next__`，会报 `non-iterator of type 'the ObjectName' `。	

- `iter(object)` 函数会调用参数对象的 `__iter__` 方法并作为其返回值。(一般情况下还是返回参数对象本身)。

- `next(object)`函数会调用一次对象的 `__next__` 方法。

- 在 `__next__` 的实现中通过抛出(raise) `StopIteration` 异常来定义迭代末尾。

- 迭代器结构是为了统一对容器对象（或者说看似是容器对象，应为有些迭代器并不维护整个容器元素）的访问。比如说 **`for-in` 语句可以作用于任何看似容器的对象(list, tuple)，就得益于迭代器。** `for-in` 语句的实现就是首先调用 `iter()`得到可迭代对象，再循环调用 `next()` 来得到值，直到迭代到末尾(raise StopIterator)。 `for-in` 语句会自动检测到`StopIteration` 异常来正常的结束循环，而 `next` 方法会使程序抛出这个异常。


 &emsp;&emsp;定义迭代器类固然可行，但这种实现方式烦琐且复杂。在Python中，还有更好的实现方式。(然而，在Java中，这是唯一的实现方式)。

<br/>
> 生成器(**Generator**)

&emsp;&emsp;当生成器函数被调用时，它不会执行函数体的内容，而是返回一个生成器对象。这个生成器对象具有与迭代器对象相同的语意，即可以作用于 `next()`，`iter()`， `for-in` 语句。当我们将返回的这个生成器对象作用于 `next()` 语句时，才开始执行函数，直到遇到 `yield` 语句停止（挂起）执行并返回 yielded 的值。当再次调用 `next()` 时，从上一次 `yield` 处接着执行。如此循环执行下去，直到没有可 `yield` 的值则抛出(raise) StopIteration 表明所有的值都以生成。


- list的列表解析器(list comprehensions)和生成器表达式(Generator Comprehensions).

&emsp;&emsp;创建一个新的列表可以使用列表解析和生成器表达式,这两种看起来一样的语法在实现上有着本质的不同。

&emsp;&emsp;**list Comprehensions在使用之前已经将所有的值都计算出来并加载进内存。**
	
	list = [x **x for x in range(5)]
	print(list) # 0,2,9,16,25

&emsp;&emsp;**而Generator Comprehensions本质上并没有使用list，而是定义generator的一种更加简单的形式。只是其语法与list Comprehensions相似，并且在行为上看起来像list而已。**

	list = (x ** for x in range(5))
	print(list) # <generator object <genexpr> at 0x01FB1C00>
	
	#equivalent to:
	def Range5():
		x = 0
		while x < 5:
			yield x**x
			x += 1

<br/>

>协程(**Coroutine**)：
协程是为非抢占式多任务产生子程序的计算机程序组件。从技术上讲，协程就是你可以暂停执行的函数。

将东西发送回暂停了的生成器。
send()方法,我们不仅可以暂停生成器，而且能够传递值到生成器暂停的地方。



