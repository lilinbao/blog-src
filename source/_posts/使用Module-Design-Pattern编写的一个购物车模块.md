title: "使用Module Design Pattern编写的一个购物车模块"
date: 2015-10-31 14:35:05
alias: 
tags: 
 - Javascript
 - 语言学习
 - jQuery
categories: 
 - Lab

---

## 前言

看了javascript design pattern，忍不住动手自己写了个购物车的模块，权当是加深印象吧，正好周末有的是时间。
The Module Pattern，网上资料少之又少，看看还是不怎么理解，不过再回来翻看，似乎挺有趣的。模块模式，也译为模组模式，是一种通用的对代码进行模块化组织与定义的方式。这里所说的模块（Modules），是指实现某特定功能的一组方法和代码。
模块模式使用了以下几种概念： 
### 闭包和立即执行的匿名函数
模块模式使用了 JavaScript 的一个特性，即闭包（Closures）。
``` javascript 
;(function(){
	//...dosomething here...
	// return something...
})()

```
在闭包中，可以定义私有变量和函数，外部无法访问它们，从而做到了私有成员的隐藏和隔离。而通过返回对象或函数，或是将某对象作为参数传入，在函数体内对该对象进行操作，就可以公开我们所希望对外暴露的公开的方法与数据。
而在上面的代码中，我们定义了一个匿名的函数，并用括号包括起来，这个括号是函数的入口，我是这样理解的，如果函数名后面加上圆括号就表示立即调用（执行）这个函数里面的代码（花括号部分的代码），也叫Immediately-Invoked Function Expression（IIFE）
这是模块模式的基本形式

### 输入参数

Javascript没有块作用域，只有函数作用域，也就是说在一个函数内一个表达式要使用一个变量，那它首先是在离它最近的块中找，找不到则往外一层找，若还是找不到则看看全局作用域下有没有定义，若没有则报typeReferenceErr.另外，Javascript也非常有意思，比如变量的定义一般都是“被提前”到函数的开始第一句，但还是在原地初始化（若有）。
看看下面的代码：
``` javascript

"use strict"
var name = "linbao";
var people = {
	name : "linbaolee",
	proxy : {
		name : "boyce",
		getName : function(){ return this.name;}
	}
}
console.log("people.name = " + people.name);//
console.log("people.proxy.name() = " + people.proxy.getName());
var test = people.proxy.getName;
console.log(test.call(people));
console.log(test());
		
```
JavaScript 有一个特性叫做隐式全局变量（implied globals），当使用一个变量名时，JavaScript 解释器将反向遍历作用域链来查找变量的声明，如果没有找到，就假定该变量是全局变量。这种特性使得我们可以在闭包里随处引用全局变量，比如 jQuery 或 window。然而，这是一种不好的方式。

考虑模块的独立性和封装，对其它对象的引用应该通过参数来引入。如果模块内需要使用其它全局对象，应该将这些对象作为参数来显式引用它们，而非在模块内直接引用这些对象的名字。以 jQuery 为例，若在参数中没有输入 jQuery 对象就在模块内直接引用 $ 这个对象，是有出错的可能的。正确的方式大致应该是这样的：
``` javascript
;(function (q, w) {
  // q is jQuery
  // w is window
  // 局部变量及代码
  // 返回
})(jQuery, window);
```

相比隐式全局变量，将引用的对象作为参数，使它们得以和函数内的其它局部变量区分开来。这样做还有个好处，我们可以给那些全局对象起一个别名，比如上例中的 "q"。现在看看你的代码，是否没有经过对 jQuery 的引用就到处都是"$"？

### 模块输出
有时我们不只是要使用全局变量，我们也要声明和输出模块中的对象，这可以通过匿名函数的 return 语句来达成，而这也构成了一个完整的模块模式
``` javascript 
var MODULE = (function () {
    var my = {},
        privateVariable = 1;
 
    function privateMethod() {
        // ...
    }
 
    my.moduleProperty = 1;
    my.moduleMethod = function () {
        // ...
    };
 
    return my;
}());

```
这段代码声明了一个变量 MODULE，它带有两个可访问的属性：moduleProperty 和 moduleMethod，其它的代码都封装在闭包中保持着私有状态。参考上文提过的参数输入，我们还可以通过参数引用其它全局变量。

#### 输出简单对象
很多时候我们 return 一个对象作为模块的输出，比如上例就是。
另外，使用对象直接量（Object Literal Notation）来表达 JavaScript 对象是很常见的。比如：var x = { p1: 1, p2: "2", f: function(){ /*... */ } }
很多时候我们都能见到这样的模块化代码：
``` javsscript
  var private_variable = 1;
  function private_method() { /*...*/ }
 
  var my = {
    property1: 1,
    property2: private_variable,
    method1: private_method,
    method2: function () {
        // ...
    }
  };
  return my;
}());
```

另外，对于简单的模块化代码，若不涉及私有成员等，其实也可以直接使用对象直接量来表达一个模块：
```javscript
var Widget1 = {
  name: "who am i?",
  settings: {
    x: 0,
    y: 0
  },
  call_me: function () {
    // ...
  }
};
```

#### 输出函数
有时候我们希望返回的并不是一个对象，而是一个函数。有两种需求要求我们返回一个函数，一种情况是我们需要它是一个函数，比如 jQuery，它是一个函数而不是一个简单对象；另一种情况是我们需要的是一个“类”而不是一个直接量，之后我们可以用 "new" 来实例它。目前版本的 JavaScript 并没有专门的“类”定义，但它却可以通过 function 来表达。
``` javascript
var Cat = (function () {
  // 私有成员及代码 ...
 
  return function(name) {
    this.name = name;
    this.bark = function() { /*...*/ }
  };
}());
 
var tomcat = new Cat("Tom");
tomcat.bark();
```
为什么不直接定义一个 function 而要把它放在闭包里呢？简单点的情况，确实不需要使用 IIFE 这种形式，但复杂点的情况，在构造我们所需要的函数或是“类”时，若需要定义一些私有的函数，就有必要使用 IIFE 这种形式了。
另外，在 ECMAScript5中，提出了 Object.create() 方法。这时可以将一个对象视作“类”，并使用 Object.create() 进行实例化，不需使用 "new"。
### Revealing Module Pattern输出对象直接量
前面已经提到一种形式是输出对象直接量（Object Literal Notation），而 Revealing Module Pattern 其实就是这种形式，只是做了一些限定。这种模式要求在私有范围内中定义变量和函数，然后返回一个匿名对象，在该对象中指定要公开的成员。参见下面的代码：
``` javascript
var MODULE = (function () {
  // 私有变量及函数
  var x = 1;
  function f1() {}
  function f2() {}
 
  return {
    public_method1: f1,
    public_method2: f2
  };
}());
```

## 模块模式的变化

### 扩展
上面的举例都是在一个地方定义模块，如果我们需要在数个文件中分别编写一个模块的不同部分该怎么办呢？或者说，如果我们需要对已有的模块作出扩展该怎么办呢？其实也很简单，将模块对象作为参数输入，扩展后再返回自己就可以了。比如：
```javascript 
var MODULE = (function (my) {
  my.anotherMethod = function () {
    // added method...
  };
 
  return my;
}(MODULE));
```
上面的代码为对象 MODULE 增加了一个 "anotherMethod" 方法。

### 松耦合扩展（Loose Augmentation）
上面的代码要求 MODULE 对象是已经定义过的。如果这个模块的各个组成部分并没有加载顺序要求的话，其实可以允许输入的参数为空对象，那么我们将上例中的参数由 MODULE 改为 MODULE || {} 就可以了：
``` javascript
var MODULE = (function (my) {
  // add capabilities...
  return my;
}(MODULE || {}));
```

### 紧耦合扩展（Tight Augmentation）
与上例不同，有时我们要求在扩展时调用以前已被定义的方法，这也有可能被用于覆盖已有的方法。这时，对模块的定义顺序是有要求的。
```javascript
var MODULE = (function (my) {
  var old_moduleMethod = my.moduleMethod;
 
  my.moduleMethod = function () {
    // 方法重载
    // 可通过 old_moduleMethod 调用以前的方法...
  };
 
  return my;
}(MODULE));
```

### 克隆与继承（Cloning and Inheritance）
```javascript
var MODULE_TWO = (function (old) {
    var my = {},
        key;
 
    for (key in old) {
        if (old.hasOwnProperty(key)) {
            my[key] = old[key];
        }
    }
 
    var super_moduleMethod = old.moduleMethod;
    my.moduleMethod = function () {
        // override method on the clone, access to super through super_moduleMethod
    };
 
    return my;
}(MODULE));
```
有时我们需要复制和继承原对象，上面的代码演示了这种操作，但未必完美。如果你可以使用 Object.create() 的话，请使用 Object.create() 来改写上面的代码：
``` javascript
var MODULE_TWO = (function (old) {
  var my = Object.create(old);
 
  var super_moduleMethod = old.moduleMethod;
  my.moduleMethod = function () {
    // override method ...
  };
 
  return my;
}(MODULE));
```
### 各种形式的混合
以上介绍了常见的几种模块化形式，实际应用中有可能是这些形式的混合体。比如：
```javascript
var UTIL = (function (parent, $) {
    var my = parent.ajax = parent.ajax || {};
 
    my.get = function (url, params, callback) {
        // ok, so I'm cheating a bit :)
        return $.getJSON(url, params, callback);
    };
 
    // etc...
 
    return parent;
}(UTIL || {}, jQuery));
```

## 实践大于一切
好了，讲了这么多，动手实践一下。下面就来开发一个购物车模块。这个模块会保留自己一部分私有的成员变量与方法，保证数据的安全，同时也给外界提供一套可用的API，包括添加购物车，更新，删除购物车等功能：
```javascript
/*
* cartModule.js 
* date : 2015-10-20 22:40
* author : Linbao
* required : jQuery.js
* useage - HTML markup at least required an container and its class name is content
* Module API : 
* - addToCart() // add an item to cart
* - deleteById(id) // delete an item from cart, id must be spcefic
* - itemCount() // return total number of item in the cart
* - total() // caculate total amount in the cart
* - populateCart() // populate all item infomation to html with formatted template
*/
+

"use strict"
var cartModule = (function(){

	var cart = [];
	function addItemToCart(product){
		console.log(product)
		cart.push(product);
		if(cart.length > 1){
			for(var i = 0; i < cart.length; i++){
				for(var j = i+1; j < cart.length; j++){
					if(cart[i].id === cart[j].id && cart[i].colorCode === cart[j].colorCode && cart[i].sizeCode === cart[j].colorCode){
						cart[i].num += cart[j].num;
						cart.splice(j,1);
					}
				}
			}
		}
	}
	function deleteItemById(id,num){
		//console.log('process to delete ...')
		if(!num) {//delete whole item
			for(var i = 0; i < cart.length; i++ ){
				if(cart[i].id == id){
					cart.splice(i,1);
				}
			}
		}
		if(id && typeof id !== 'Number') {parseInt(id);}
		for(var i = 0; i < cart.length; i++){
			if(cart[i].id == id){
				if(cart[i].num >= 2){
					cart[i].num --;
					//console.log('num --')
				}else{
					cart.splice(i,1);
					//console.log('item has been totally remove from your cart');
				}
			}
		}
		console.log('end delete item from cart ')
	}
	function printOutCartConsole(){
		console.log("The total item in your cart is "+ cart.length);
		for(var i in cart){
			console.log('product ' + cart[i].id 
				+ ', it\'s color is ' + cart[i].color + ', size is ' + cart[i].size 
				+ ' price is ' + cart[i].price +' num is ' + cart[i].num 
				+ "\n");
		}
		outPut2Html(cart);
	}
	function getTotalCartAmount(){
		var totalAmount = 0.00;
		for(var i = 0; i < cart.length; i++){
			totalAmount += cart[i].price * cart[i].num;
		}
		return totalAmount;
	}
	function count(){
		var itemNum = 0;
		for(var i in cart){
			itemNum += cart[i].num;
		}
		return itemNum;
	}
	function output2Html(obj){
		if(!obj) obj = cart;
		var template = "<span class='id'>$id$</span>"+
					   "<span class='color'>$color$</span>"+
					   "<span class='num'>$num$</span>"+
					   "<span class='price'>$price$</span>";
		var wrap = "<div class='cart' id='cart' ></div>";
		$('.content').html(wrap);
		for(var i = 0; i < obj.length; i++){
			var c = "<div class='item' id='"+obj[i]['id']+"'>";
			c += strReplace(template,obj[i]);
			c += '</div>';
			$('.cart').append(c);
			var btnDel = $("<button class='delItem' id='delItem'>delete</button>");
			btnDel.bind('click',function(){
				var self = $(this);
				var id = self.parent().attr('id');
				cartModule.deleteById(id);
			});
			$('.item').append(btnDel);
		}
	}
	function strReplace(str,item){
		return str.replace(/\$\w+\$/gi, function(matches){		
			var value = item[matches.replace(/\$/g,'')];
			return !typeof(value) ? '': value;
		});
	}
	return {
		addToCart : function(){
			var test = Math.random() * 100000;
			var productId = 1//(parseInt(test)) % 100;
			var colorCode = 'AXD';
			var sizeCode = 'L';
			var price = Math.floor(((test % 7) * 100)) / 100;
			var num = 1;
			
			addItemToCart({
				'id' : productId,
				'color' : colorCode,
				'size' : sizeCode,
				'price' : price,
				'num' : num
			});
			this.populateCart();
		},
		deleteById : function(id){
			deleteItemById(id,1);
			this.populateCart();
		},
		itemCount : function(){
			//console.log(count());
			return count();
		},
		total : function(){
			var total = this.getTotalCartAmount();
			var sum = this.count();
			console.log('There are '+ sum + ' item(s) in your cart, total amount is ' + total);
			return total;
		},
		populateCart : function(){
			output2Html();
		}
	}
})()
```

{%blockquote Chen-Zhiqiang http://my.oschina.net/chenzhiqiang/blog/129783 JavaScript 模块化编程 - Module Pattern%}
参考文献1
{%endblockquote%}

{%blockquote David Flanagan http://baike.baidu.com/link?url=z8w5MQ7w5UUBKNowDYiXV4S6KGW0sJBZCzFqxIKHCOoh9mon7hnFzOOekV4m7Dx0LczXed8vZnsv56FO3XXYZ_ Javascript权威指南 %}
参考文献2
{%endblockquote%}