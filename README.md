
<h2 id='obj'>Hello GitHub<h2>
* [定义私有属性](#obj)
	*	


# 面向对象	

## 类和对象

- 对象是计算机语言对问题域中事物的描述，它通过属性和方法分别对应事物所具有的静态属性和动态属性。
- 类是用来描述同一类型的对象的一个**抽象概念**。类中定义了这一类对象所具有的静态和动态属性。
- 类可以看成是一类对象的模板，对象可以看成是类的一个具体实例。

## 基于原型的对象创建方式

使用`new`关键字后，意味着做了如下四件事：

1. 创建一个新的对象，这个对象的类型是`Object`
2. 设置这个新的对象的内部、可访问性和`_proto_`属性为构造函数（指`prototype.construtor`所指向的构造函数）中设置的
3. 执行构造函数，当`this`关键字被提及的时候，使用新创建的对象的属性
4. 返回新创建的对象

## 对象数据所具有的特性


对象里目前存在的属性描述符有两种主要形式：***数据描述符***和***存取描述符***。

**数据描述符：**

`configurable`

> 当且仅当该属性的`configurable`为`true`时，该属性描述符才能够被改变，也能够被删除。默认为`false`。

`enumerable`

> 当且仅当该属性的`enumerable`为`true`时，该属性才能够楚翔在对象的枚举属性中。默认为`false`。

`writable`

> 当且仅当该属性的`writable`为`true`时，该属性才能被赋值运算符改变。默认为`false`。

**存取描述符：**

`get`

> 一个给属性提供`getter`的方法，如果没有`getter`则为`undefined`。该方法返回值被用作属性值。默认为`undefined`。

`set`

> 一个给属性提供`setter`的方法，如果没有`setter`则为`undefined`。该方法将接受唯一参数，并将该参数的新值分配给该属性。默认为`undefined`。

## 定义私有属性
		
		
		function Person(name, age) {
		  var _name = name
		  this.age = age
		  this.setName = function(name) {
		    _name = name
		  }
		  this.getName = function() {
		    return _name
		  }
		}
		
		var p1 = new Person("zhangsan", 21)
		console.log(p1)
		

以上代码，`Person` 类有一个 `name` 属性和一个 `age` 属性。我们并没有直接将 `name` 属性添加到 `this` 身上，而是赋给了 `Person` 函数内部的 `_name` 属性。 根据函数的作用域关系，外部是无法访问函数内部定义的变量，因此可以让 `name` 属性变成私有。同时，如果希望外部能够获取 `name` 属性的值，添加 `getName` 和 `setName` 方法，通过方法的暴露来提供内部变量的访问。除了以上的方式，我们还可以使用 `Object.defineProperty()` 来定义私有属性。


		function Person(name, age) {
		  var _name = name
		  this.age = age
		  Obejct.definProperty(this, "name", {
		    get() {
		      return _name
		    },
		    set(name) {
		      _name = name
		    }
		  })
		}


## 原型链

对象的`__proto__`属性指向的是构建这个对象的构造函数的`prototype`。



访问对象的属性时，首先在对象的内部寻找，当未找到该属性时，在该对象的原型上寻找。这个过程称为原型链。



		
		//父类
		function Person(name, age) {
		  this.name = name
		  this.age = age
		  this.sayName = function () {
		    console.log(this.name)
		  }
		}
		//子类
		function Student(name, age) {
		  this.constructor(name, age)//通过父类的构造器对属性进行初始化
		  this.sex = "male"
		}
		Student.prototype = new Person()//子类通过原型的方式获得父类对象中的属性
		var stu = new Student("zhangsan", 30)
		stu.sayName() 


### 构造函数中的属性和原型中的属性的区别

把属性定义在原型中比定义在构造函数中消耗的内存更小，因为在内存中一个类的原型只有一个，写在原型中的行为可以被所有实例共享，实例化的时候并不会在内存中再复制一份，因此，如果没有特殊原因，一般把属性写到类中，而行为写到原型中。

### 隐式原型

隐式原型：`__proto__`是对象的一个属性，即每个对象都有一个`__proto__`属性，也称为隐式原型。

		
		function Person() {}
		var p1 =new Person() 
		console.log(p1.__proto__ === Person.prototype)
		

每一个对象都有一个`__proto__`属性，指向创建该对象的函数的`prototype`

## 对象继承

### 1. 对象冒充

> 让父类的构造函数成为子类的方法，然后调用该子类的方法，通过`this`关键字给所有的属性和方法赋值。


		//父类
		function Person(name, age){
		  this.name = name
		  this.age = age
		  this.sayName = function() {
		    console.log(this.name)
		  }
		}
		
		//子类
		function Student(name, age) {
		  this.obj = Person
		  this.obj(name, age)
		  delete this.obj
		  this.sex = 'male'
		}	


>  `call`和`apply`方法, 其本质也是对象冒充

		
		//父类
		function Person(name, age) {
		  this.name = name
		  this.age = age
		  this.sayName = function() {
		    console.log(this.name)
		  }
		}
		
		//子类
		function Child(name, age) {
		  //Person.apply(this, [name, age])
		  Person.call(this, name, age)
		}
		var c = new Child("zhangsan", 21)
		console.log(c.name)
		

使用`call`和`apply`方法实现的继承，其本质就是改变函数上下文中`this`的指向。这两者的区别在于参数的不同。

对象冒充继承方式的缺点在于，每个对象都会保存相同的方法。这种方式会造成内存的浪费。



`call()`、`apply()`、`bind()`三个方法都可以指定调用函数的对象

三者的区别是：
		
		
		var x = 10
		function fn(a, b, c) {
		  console.log(this.x, a, b, c)
		}
		
		fn()//10
		
		var obj = {
		  x: 20
		}
		
		fn.call(obj, 1, 2, 3)
		fn.apply(obj, [1, 2, 3])
		
		var newFn = fn.bind(obj)
		
		newFn()
		

`call()`和`apply()`会调用函数，`bind()`会返回一个新的函数



### 2. 原型链的方式

> 使子类原型对象指向父类的实例以实现继承，即重写类的原型


		//父类
		function Person(name, age) {
		  this.name = name
		  this.age = age
		  this.sayName = function() {
		    console.log(this.name)
		  }
		}
		
		//子类
		function Student(name, age) {
		  this.constructor(name, age)//通过父类的构造器对属性进行初始化
		  this.sex = "male"
		}
		Student.prototype = new Person()//子类通过原型的方式获得父类对象中的属性
		var stu = new Student("zhangsan", 30)
		stu.sayName()


缺点：无法修改`Student.prototype.constructor`

### 3. 混合方式

> 混合call和原型链

		function Parent(name, age) {
		  this.name = name
		  this.age = age
		}
		
		Parent.prototype.sayName = function() {
		  console.log(this.name)
		}
		
		function Child(name, age) {
		  Parent.call(this, name, age)
		}
		
		Child.prototype = Object.create(Parent.prototype)
		//Child.prototype = new Parent()
		var c = new Child("zhangsan", 12)
		c.sayName()
		

`Object.create(Father.prototype)`等价于`new Father()`

缺点：代码冗余



## ES6创建类

通过`class`关键字创建类。其本质与ES5相同

		class Grandpa {
			constructor(gender) {
				this.gender = gender
			}
		
			sayGender() {
				console.log(this.gender)
			}
		}
		
		class Father extends Grandpa {
			constructor(name, age, gender) {
				super(gender)
				this.name = name
				this.age = age
			}
		
			sayAge() {
				console.log(this.age)
			}
		}
		
		class Child extends Father {
			constructor(name, age, gender) {
				super(name, age, gender)
			}
		
			sayName() {
				console.log(this.name)
			}
		}
		
		var c1 = new Child("zhangsan1", 22, "female")
		console.log(c1)
		



# 执行上下文与作用域

作用域也称为词法作用域

## 执行上下文环境

>  每次调用函数时都会产生执行上下文。

>  执行上下文用来保存函数执行时需要的数据。

>  上下文访问的数据依赖于作用域。

在js代码真正运行之前，浏览器已经做了一些“准备工作“，其中就包括对变量的声明，变量赋值是在赋值语句执行的时候进行的。

**准备工作中完成了哪些工作：**

1. 变量： 变量的声明，默认为`undefined`
2. this：赋值
3. 函数声明：赋值

这三种数据的准备情况我们称之为”执行上下文“或者”执行上下文环境“

**函数中的变量：**

如果在函数中，除了以上数据之外，还会有其他的数据，如：`arguments`

函数每被调用一次，都会产生一个新的执行上下文环境，因为不同的调用可能就会有不同的参数。

		
		var a = 10
		
		function fn() {
			console.log(a) //	a是自由变量
		} //	函数创建时，就确定了a要取值的作用域
		
		function bar(f) {
			var a = 20
			f()
		}
		
		bar(fn)
		

**说明：**定义函数时，就已经确定了函数体内部变量的作用域

**总结：**

- 全局的上下文环境数据内容：

| 普通变量（包括函数表达式），如：var a = 10; | 声明（默认赋值为`undefined`） |
| :-------------------------: | :------------------: |
|   函数声明，如：function fn() {}   |          赋值          |
|            this             |          赋值          |

- 局部的上下文环境数据内容（函数体）包含以上部分

|     参数     |  赋值  |
| :--------: | :--: |
| arguments  |  赋值  |
| 自由变量的取值作用域 |  赋值  |

## 执行上下文栈

执行全局代码时，会产生一个全局上下文环境，每次调用函数时又会产生一个函数上下文环境，当函数调用完成时，这个函数上下文环境以及其中的数据都会被销毁，在重新回到全局上下文环境，**处于活动状态的执行上下文环境只有一个**，实际上，就是一个压栈和出栈的过程。
		
		
		//1. 进入全局上下文环境
		var a = 10
		
		var fn = function(y) {
			var c = 5
			console.log(y + c)
		}
		
		var bar = function(x) {
			var b = 5
			fn(x + b) //进图fn函数上下文环境
		}
		
		bar(10) //2. 进入bar函数上下文环境
		

1. 在执行代码之前，首先将创建全局上下文环境

   | 全局上下文环境 |           |
   | ------- | --------- |
   | a       | undefined |
   | fn      | undefined |
   | bar     | undefined |
   | this    | window    |



2. 代码执行，当代码执行到`bar()`方法时，全局上下文环境中的变量都在执行过程中被赋值。

   | 全局上下文环境 |          |
   | ------- | -------- |
   | a       | 10       |
   | fn      | function |
   | bar     | function |
   | this    | window   |



3. 调用`bar`函数，进入函数内部，在执行函数体语句之前，会创建一个新的执行上下文环境。

   | bar函数-执行上下文环境 |           |
   | ------------- | --------- |
   | b             | undefined |
   | x             | 10        |
   | arguments     | [10]      |
   | this          | window    |

4. 将这个执行上下文环境压栈，设置活动状态

5. 代码执行到`fn(x + b)`时，进入`fn`函数，在执行函数体语句之前，创建`fn`函数的执行上下文环境，并压栈，设置活动状态

6. `fn`函数执行完毕，通过`fn`函数产生的执行上下文环境出栈，并且被销毁（使用完成会销毁，释放内存，**正常情况下如此（闭包）**）

7. `bar`函数执行完毕，通过`bar`函数产生的执行上下文环境出栈，并且销毁

## 作用域

除了全局作用域之外，每个函数都会创建自己的作用域，作用域在函数定义时就已经确定了。而不是在函数调用时确定的。

		
		var a = 10,
			b = 20
		
		function fn(x) {
			var a = 100,
				c = 300
		
			function bar(x) {
				var a = 1000,
					d = 4000
			}
			bar(100)
			bar(200)
		}
		
		fn(10)
		console.log(a)
		

**说明：**作用域只是一个“地盘”，一个抽象的概念，其中没有变量，要通过作用域对应的执行上下文环境来获取变量的值，在同一个作用域下，不同的调用会产生不同的执行上下文环境，继而产生不同的变量的值。

**自由变量：**在A作用域中使用的变量x，却没有在A作用域中声明（即在其他作用域中声明的），对于A作用域来说，x就是一个自由变量。

		
		var x = 10
		function fn() {
		  console.log(x)//这里的x就是一个自由变量
		}
		

**作用域链：**

1. 先在当前作用域查找x，如果有则获取并结束，如果没有则继续。
2. 如果当前作用域是全局作用域，则证明x未定义，结束，否则继续
3. （不是全局作用域，那就是函数作用域）将创建该函数的作用域作为当前作用域
4. 调转到第一步





# this的用法

当普通函数不是任何对象的属性时，其中的`this`指向`window`。

在函数中`this`到底取何值，是在**函数真正被调用执行的时候确定的**。因为`this`的取值是执行上下文环境的一部分，每次调用函数，都会产生一个新的执行上下文华环境。

## 1. 构造函数之`this`


		function Foo() {
		  this.name = "zhangsan"
		  this.age = 29
		  console.log(this)
		}
		
		var f1 = new Foo()
		console.log(f1)


**说明：**

1. 如果函数作为构造函数使用，那么其中的`this`就代表它`new`出来的对象
2. 如果函数作为普通函数来使用，那么其中的`this`就指`window`

		
		function Foo() {
		  this.name = "zhangsan"
		  this.age = 29
		  console.log(this)
		}
		Foo()
		console.log(window.name)
		console.log(window.age)
		

## 2. 函数作为对象的一个属性

如果函数作为对象的一个属性时，并且作为对象的一个属性被调用时，函数中的`this`指向该对象

		
		var obj = {
		  x: 10,
		  fn: function() {
		    console.log(this.x)
		  }
		}
		obj.fn()
		
		var obj1 = {
		  x: 20
		}
		obj1.fn = obj.fn
		obj1.fn()
		

## 3. 函数用`call`或`apply`调用

当一个额函数被`call`或`apply`调用时，`this`的值就是传入的对象的值。

		
		var test = "Tony"
		function doSomeThing() {
		  console.log(this.test)
		}
		var obj = {
		  test: "Tom"
		}
		doSomeThing()
		doSomeThing().call(obj)
		

## 4. 全局&普通函数的调用

在全局环境下，`this`指向的是`window`对象

		console.log(this === window)//true
		

普通函数调用时，其中的`this`也是指向的`window`对象

		var x = 10
		var fn = function() {
		  console.log(this)//window
		  console.log(this.x)
		}
		fn()
		

注意一下情况：虽然函数`f`是在`obj`对象内部定义的，但是他仍然是一个普通函数，`this`指向的还是`window`对象


		var x = 20
		var obj = {
			x: 100,
			fn: function() {
				function f() {
					console.log(this)
					console.log(this.x)
				}
				f()
			}
		}
		
		obj.fn()


## 5. 构造函数`prototype`

`this`代表的是当前对象的值，不仅仅是在构造函数的`prototype`，即便是在整个原型链中，`this`还是代表当前对象的值

		
		var name = "lisi"
		
		function Fn(name, age) {
			this.name = name
			this.age = age
		}
		Fn.prototype.sayName = function() {
			console.log(this.name)
		}
		var f1 = new Fn("zhangsan", 21)
		console.log(f1)
		f1.sayName()
		

# 闭包

**闭包：**闭包是一个抽象概念，是一种设计思想。一个好的设计，会让使用者根本不会发现其存在，但仍然能使用它。闭包延长了局部变量的作用域，使外部能够访问内部的变量。

**闭包的体现：**把函数当做返回值。把函数当做参数。

没有任何变量指向的数据称为垃圾数据。语言中的GC（垃圾回收器）会回收垃圾数据。

**闭包的作用：**

- 隐藏数据
- 模块化（在外部操作内部变量，所有的模块化操作都是基于闭包）
- 延长函数内部数据的作用域范围

**缺点：**闭包会导致内存溢出。

## 1. 函数作为返回值

		
		function fn() {
			var max = 10
			return function bar() {
				console.log(max)
			}
		}
		
		var f1 = fn()
		var max = 0
		f1() //10
		

**示例说明：**局部变量在函数执行完成之后未被销毁

## 2. 函数作为参数被传递

		
		var max = 10
		var fn = function() {
			console.log(max)
		};
		
		(function(f) {
			var max = 100
			f()//10
		}(fn))
		

**示例说明：**自由变量在函数声明时已经确定了其作用域

## 例子：
		
		
		function fn() {
			var max = 1000
		
			return function bar() {
				console.log(max)
			}
		}
		
		var f1 = fn()
		var max = 0
		f1()//1000
		





# `Symbol`

通过`for in`无法遍历到对象的`Symbol`类型属性

## 1. Symbol是什么

`Symbol`是`ES6`中新添加的一种基本数据类型。每一个`Symbol()`生成的指都是独一无二的。

在`js`中，我们可以随意的给对象添加属性或方法，这种操作对象的方式，很容易会产生属性名冲突的情况。利用`Symbol`这个特点，可以给对象添加独一无二的属性。

## 2. 如何生成一个`Symbol`值

`Symbol()`是一个函数，如果我们想要得到一个`Symbol`值，只需调用该函数即可

		
		const s1 = Symbol()
		const s2 = Symbol()
		
		console.log(s1)//Symbol()
		console.log(s2)//Symbol()
		console.log(s1 === s2)//false
		

`s1`和`s2`就是两个`Symbol`值。每次`Symbol`函数调用，都会得到一个不同的`Symbol`值。

在控制台打印`s1,s2`，控制台会输出两个`Symbol()`字符串，我们无法区分到底谁是`s1`，谁是`s2`。因此，在调用`Symbol`函数时,我们通常会传递一个字符串类型的参数,用来描述创建的`Symbol`值.

		
		const s1 = Symbol("s1")
		const s2 = Symbol("s2")
		console.log(s1, s2)//Symbol(s1) Symbol(s2)
		

这时,我们就可以根据描述来区分不同的值了.

注意: `Symbol`函数的参数只是表示对当前`Symbol`值的描述,相同参数的`Symbol`函数返回值依然是不相同的.

		var s1 = Symbol("foo")
		var s2 = Symbol("foo")
		console.log(s1 === s2)//false
		

## 3.` Symbol.for`   , `Symbol.keyFor`

`Symbol()`每次调用都会返回一个新的属性值.有时,我们希望重新使用同一个`Symbol`值,就可以通过`Symbol.for`方法来创建.该方法接受一个字符串作为参数.

		
		var s1 = Symbol.for("foo")//没有找到描述为foo的Symbol值,则创建新的Symbol值
		var s2 = Symbol.for("foo")//找到描述为foo的Symbol值,则返回
		
		console.log(s1 === s2) //true
		

以上代码中,`s1`和`s2`都是`Symbol`值.而且是通过**相同参数(foo)的Symbol.for()**生成的.实际上就是同一个值.

> 比较: `Symbol()`和`Symbol.for()`都会产生`Symbol`值,前者每次都产生新的`Symbol`值.后者在创建`Symbol`值之前,会在全局环境中搜索注册的`Symbol`值,如果不存在才会创建新的.

`Symbol.keyFor()`用来返回`Symbol`值的注册信息.

		
		var s1 = Symbol.for("foo")
		console.log(Symbol.keyFor(s1))//"foo"
		

这个方法需要配合`Symbol.for()`使用,如果你使用`Symbol()`创建`Symbol`值.无论你传不传递参数,通过`Symbol.keyFor()`函数是查找不到它的注册信息的.

		
		var s2 = Symbol("s2")
		console.log(Symbol.keyFor(s2))// undefined
		

## 4. 使用`Symbol`来定义对象的属性

`ES5`的对象属性名都是字符串,这容易造成属性名的冲突.由于`Symbol`值的唯一性,这意味着我们可以使用`Symbol`值来作为对象的属性名,这样就可以保证对象不会出现同名的情况.

**使用`Symbol`作为对象的属性**

		
		var mySymbol = Symbol()
		
		//第一种写法
		var a = {}
		a[mySymbol] = "Hello!"
		
		//第二种写法
		var a = {
			[mySymbol]: "World!"
		}
		
		//第三种写法
		var a = {}
		Object.defineProperty(a, mySymbol, {
			value: "Hello!"
		})
		
		console.log(a)
		

注意,`Symbol`值作为对象属性名时,不能用点运算符.

`Symbol`类型还可以用于定义一组常量,保证这组常量的值都是不相等的.

		
		const color_red = Symbol()
		const color_blue = Symbol()
		
		function getComplement(color) {
			switch (color) {
				case color_red:
					console.log("color_red")
					break;
				case color_blue:
					console.log("color_blue")
					break;
				default:
					console.log("error")
			}
		}
		
		getComplement(color_red)
		

## 5. 遍历`Symbol`定义的属性

使用`Symbol`定义的属性,是无法在`for...in`,`Object.keys()`,`Object.getOwnPropertyNames()`方法中被返回的.

		
		const obj = {
			[Symbol("username")]: "zhangsan",
			[Symbol("password")]: "123",
			age: 21
		}
		
		for (let attr in obj) {
			console.log(attr) //age
		}
		
		console.log(Object.keys(obj)) //["age"]
		
		console.log(Object.getOwnPropertyNames(obj)) //["age"]
		

想要获取对象`Symbol`定义的属性,我们可以使用`Object.getOwnPropertySymbols()`

		
		console.log(Object.getOwnPropertySymbols(obj)) //[ Symbol(username), Symbol(password) ]
		

`Object.getOwnPropertySymbols()`方法返回一个数组,成员是当前对象的所有用作属性名的`Symbol`值.除了以上方法可以获取对象的`Symbol`属性以外,还可以使用`Reflect.ownKeys()`方法获取对象所有的属性:

		
		console.log(Reflect.ownKeys(obj)) //[ 'age', Symbol(username), Symbol(password) ]
		

这时,就可以获取对象的全部属性了

## 6. 内置的`Symbol`值

除了定义自己使用的`Symbol`值以外,ES6还提供了11个内置的Symbol值.

### Symbol.iterator

`Symbol.iterator`用来表示迭代器,只要对象实现这个接口,就可以使用`for...of`方式来遍历.已有的集合类型,数组,`Set`和`Map`,默认就实现了这个接口,因此他们可以通过`for...of`来遍历,除此以外,在ES6中,想要使用`for...of`遍历一个`Object`对象,就需要这个对象实现`Symbol.iterator`接口

**数组迭代器:**

		var arr = [1, 2, 3, 4]
		var arrIt = arr[Symbol.iterator]()
		console.log(arrIt.next()) //{ value: 1, done: false }
		console.log(arrIt.next()) //{ value: 2, done: false }
		console.log(arrIt.next()) //{ value: 3, done: false }
		console.log(arrIt.next()) //{ value: 4, done: false }
		console.log(arrIt.next()) //{ value: undefined, done: true }
		

**对象迭代器:**

		
		
		var obj = {
			name: "zhangsan",
			password: 123
		}
		Object.prototype[Symbol.iterator] = function() {
			let index = 0
			let keys = Reflect.ownKeys(this)
			return {
				next: () => {
					if (index < keys.length) {
						return {
							value: [keys[index], this[keys[index++]]],
							done: false
						}
					} else {
						return {
							value: undefined,
							done: true
						}
					}
				}
			}
		}
		
		var objIt = obj[Symbol.iterator]()
		console.log(objIt.next()) //{ value: [ 'name', 'zhangsan' ], done: false }
		console.log(objIt.next()) //{ value: [ 'password', 123 ], done: false }
		console.log(objIt.next()) //{ value: undefined, done: true }
		



# JSONP

**跨域:**

- 协议发生变化
- IP地址变化
- 端口号变化(两个服务)

`<link`,`<script>`,`<image>`都支持跨域访问,其请求类型为`get`请求.

`ajax`本质上不支持跨域

**同源策略:**浏览器的访问策略

## JSONP

**JSONP:**ajax用来解决跨域的解决方案.
		
		
		$.ajax{
		  url: "url",
		  data: {data},
		  dataType: "jsonp",
		  success(data) {
		   console.log(data)
		 }
		}
		

**原理:**新创建一个`<script>`标签,通过这个标签进行跨域请求.

# session

`session`的本质是`cookie`,通过`session`来保存用户的登录状态.

`session`是后台针对状态记录的机制.

每次请求会自动携带`cookie`的信息.

由于`http`协议是一个无状态的协议,所以诞生`session`,`session`的本质是`cookie`,用来保存登录状态.

移动端没有`cookie`

# Webpack

npm的`start`是一个特殊的脚本名称，它的特殊性表现在，在命令行中使用`npm start`就可以执行相关命令，如果对应的此脚本名称不是`start`，想要在命令行中运行时，需要这样用`npm run {script name}`如`npm run build`，以下是执行`npm start`后命令行的输出显示



# React

## `React.createElement("h1", null, "hello world")`

创建`React`元素.

## DOM渲染

在一个页面中元素的`key`是唯一的

JSX中只支持单一表达式,不支持复合表达式.

## 定义组件(component)


		
		var HelloWorld = React.createClass({
		  render() {
		    return (
		    	<h1>hello world</h1>
		    )
		  }
		})
		
		console.log(HelloWorld)
		
		/*
		
		function (props, context, updater) {
			      // This constructor gets overridden by mocks. The argument is used
			      // by mocks to assert on what gets mounted.
		
			      if (false) {
			        process.env.NODE_ENV…
		*/
		
		ReactDOM.render(<HelloWorld />, app)
		

**注意:**

- 组件类的第一个字母**必须大写**，否则会报错，比如`HelloWorld`不能写成`helloWorld`。另外，组件类只能包含一个顶层标签，否则也会报错。
- 添加组件属性，有一个地方需要注意，就是 `class` 属性需要写成 `className` ，`for` 属性需要写成 `htmlFor` ，这是因为 `class` 和 `for` 是 JavaScript 的保留字。

## getInitialState()

`getInitialState() {}`是`React`组件提供的方法,可以通过这个方法对组件定义状态.

状态用来描述数据.

		
		var HelloWorld = React.createClass({
		  getInitialState() {
		    return {
		      count: 1
		    }
		  },
		  render() {
		    return (
		    	<h1>hello world {
		          this.state.count
		    	}</h1>
		    )
		  }
		})
		

**单项数据流:** 只能通过`state`的改变来改变DOM节点

## getDefaultProps()

props是不可变得. props的变化是state的变化引起的.

`getDefaultProps()`在函数第一次调用的时候执行且只执行一次.

**组件与组件之间的通信:**兄弟级的组件可以通过共同的父级组件来共享state.

- 组件的嵌套
- 组件与组件之间的通信

## 组件的嵌套

**容器组件:**包含其他组件的父组件.

- state用来描述自身组件的状态
- props用来描述其他组件的状态

		
		var Wrap = React.createClass({
			getInitialState() {
				return {
					val: ""
				}
			},
			handleChange(e) {
				this.setState({
					val: e.target.value
				})
			},
			render() {
				return (
					<div>
						<input type="text" onChange={this.handleChange}/>
						<H1Ele val={this.state.val}/>
					</div>
				)
			}
		})
		
		var H1Ele = React.createClass({
			getDefaultProps() {
				return {
					val: "init"
				}
			},
			render() {
				return <h1>{this.props.val}</h1>
			}
		})
		
		ReactDOM.render(<Wrap/>, app)
		

## ES7创建组件方式

		
		//引入react
		import React from "react"
		import ReactDOM from "react-dom"
		
		class Counter extends React.Component {
			constructor(props) {
				super(props)
					//state 放在constructor内部
				this.state = {
					count: 0
				}
			}
		
			setCount() {
				this.setState({
					count: this.state.count + 1
				})
			}
		
			//ES7 设置默认props
			// static defaultProps = {
			// 	count: -2
			// }
		
			render() {
				return (
					<div>
						<ShowCount count={this.state.count}/>
						<Button onClick={() => {
							this.setCount()//使用箭头函数调用函数
						}} buttonName="点击计数"/>
					</div>
				)
			}
		}
		
		class ShowCount extends React.Component {
			constructor(props) {
				super(props)
			}
		
			render() {
				return (
					<h1>点击{this.props.count}次</h1>
				)
			}
		}
		
		class Button extends React.Component {
			constructor(props) {
				super(props)
			}
			render() {
				return (
					<input type="button" onClick={this.props.onClick} value={this.props.buttonName}/>
				)
			}
		}
		
		//设置默认props
		// Counter.defaultProps = {
		// 	count: -1
		// }
		
		ReactDOM.render((<Counter />), document.body)
		



## 无状态函数式组件

		function ShowCount(props) {
			return (
				<h1>点击{props.count}次</h1>
			)
		}
		



## 生命周期

在`componentWillMount`中调用`setState`方法`render`不会反复执行.

**ant design:**基于react的组件库

## 状态机(redux)

一个应用只有一个状态.

分为两个部分: 数据 + 行为

		
		import { createStore } from "redux"
		//reducers
		function fn(state = 0, action) {
		  switch(action.type) {
		    case "INCRMENT" : 
		      return state + 1;
		    default:
		      return state;
		  }	
		}
		
		const store = ceateStore(fn)
		
		store.subsctibe(() => console.log(store.getState()))
		
		store.dispatch({
		  type: "INCRMENT"
		})//触发行为
		console.log(store.getState())//获取状态
		

`action`用来描述行为.

每次`dispatch`的时候,`subscribe`中的回调函数被执行.



`connect`链接组件和状态.



`redux`规定不在以前的`state`上直接修改数据,而是添加一个新的数据.



`redux-thunk`是node中的一个中间件,当`action`返回一个函数时,`redux-thunk`将照执行该函数并添加`dispatch`参数.



# Vue

`{{}}`: 表达式



数组和字符串的原生命令不会修改原来的数组或字符串.

`this.$emit()`

# 性能优化

1. 减少HTTP请求次数
2. 使用CDN(Content Delivery Network, 内容分发网络)
3. 增加Expires Header
   - 缓存静态资源
   - 问题: 如何更新修改之后的文件
4. 压缩页面元素 
   - Gzip
5. 把样式表放在头部
6. 把脚本文件放在底部
7. 避免CSS表达式
8. 通过外部引入的方式加载文件
   - 因为写在HTML文件中的JS和CSS文件无法缓存
9. 减少DNS查询次数
10. 最小化JavaScript代码
11. 避免重定向
12. 删除重复的脚本文件
13. 配置ETags
    - ETags代表资源上一次修改的时间
14. 缓存Ajax


##  [返回顶部](#obj) 
	


    
