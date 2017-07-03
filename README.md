
<h2 id='obj'>Hello GitHub<h2>	
	```js
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
	```
[返回顶部](#obj) 
	


    
