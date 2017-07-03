[This的用法](#this)   
		当普通函数不是任何对象的属性时，其中的`this`指向`window`。     
		在函数中`this`到底取何值，是在**函数真正被调用执行的时候确定的**。		
		因为`this`的取值是执行上下文环境的一部分，每次调用函数，都会产生一个新的执行上下文华环境。  
1. 构造函数之`this`  <br>
```js
	function Foo() {
		  this.name = "zhangsan"
		  this.age = 29
		  console.log(this)
		}
		var f1 = new Foo()
		console.log(f1)
```
 
	


    
