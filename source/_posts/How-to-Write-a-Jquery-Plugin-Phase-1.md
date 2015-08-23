title: "How to Write a Jquery Plugin Phase 1"
date: 2015-05-24 17:57:47
alias: 
tags: jquery
categories: 语言学习

---
周末，可是没有忘记一直想自己封装一个jquery的插件，然后像boostrap那样，选择一个元素然后调用自己的方法，什么事都不用管它自己就帮你做好所有的一切，创建包围，注册监听事件，完成包尾工作，一切都是那么地从容。。。
好吧，正好公司的项目也要用到一个关于文本的工具，一起来学一下吧
##准备工作
查阅一下编写一个jquery的基本要求：
1. 在JQuery命名空间下声明只声明一个单独的名称
2. 接受options参数，以便控制插件的行为
3. 暴露插件的默认设置 ，以便外面可以访问
4. 适当地将子函数提供给外部访问调用
5. 保持私有函数
6. 支持元数据插件

另外Jquery也提供了两在学重要的方法以便developer在扩展自己插件的时候使用：
``` javascript
jQuery.extend(object)      即 $.extend(object)，当需要合并参数时也是这个
jQuery.fn.extend(object)   即 $.fn.extend(object)
```
这2个API都是为了将自己编写的功能以插件的形式加入到 jquery 中.但是含义上是有区别的.
`$.extend(object)` 这个函数是用来扩展 jQuery 本身, 也就是扩展 `"$"`的
`$.fn.extend(object)` 这个函数用来为 jQuery 对象提供新的方法.所谓 jQuery 对象, 最常见的我们平时通过jQuery选择器获取的对象, 比如: `$("#id"), $(".class")` 等等.
##插件主函数体中的this关键字
这里的 this 是jQuery 对象, 也就是通过 $("#id"), $(".class") 之类的选择器获取的对象。
`注意：`
  - 与each中的`this`有些不同，`$.each`中的`this`是DOM对象，那是因为`$.each`会自动把`jQuery`对象转换为DOM对象
  - 在主函数最后记得用`return this;`,是为了实现 jQuery 的链式表达式. 也就是 `return this;` 之后, 可以接着调用其他 jQuery 函数.

##功能型插件(使用最多的jQuery插件)
有两种写法，大家可以根据自己的爱好来写
###方法定义在extend里：
```javascript
(function($) {  
    $.fn.extend({  
        readmore: function(options) {  
            var defaults = {  
                max_column: 2,  
            };  
            var options = $.extend(defaults, options);  
            var len = parseInt(options['max_column']);  
            return this.each(function() {  
                alert('linbao');  
            });  
        },
        functionA:function(){
        	//...
    	}  
    });  
})(jQuery);  
```
###方法定义在extend外：
```javascript
//create closure  
(function($){  
	//plugin default value definition
	$.fn.readmore.default = {
		max_column:4
	}
    //plugin definition  
    $.fn.readmore = function(options){  
        debug(this);  
        var opts = $.extend({},$.fn.hilight.defaults,options);  
    	return this.each(function(){  
        var $this = $(this);  
        //build element specific options  
        var o = $.meta ? $.extend({},opts,$this.data()) : opts;          
    }); 
    
    }; 
    //public function definition 
    $.fn.readmore.functionB = function(arg){
    	//...
    }
    //private function for debuggin  
    function debug($obj){  
        if(window.console && window.console.log){  
            window.console.log('readmore selection count :' + $obj.size());  
        }  
    }  
    //...  
    //end of closure  
})(jQuery);  
```
这两种方式编写的插件调用方式都一样
```javascript
$('div').readmore();
```
若要一更改插件的默认值
```javascript
$('div').readmore({max_column:5});
```
`注意：`
合并参数时, 使用了 $.extend(), 这个方法的作用是将所有参数 合并成一个大的json对象, 有相同的key时, 后面的参数覆盖前面的参数.
$.extend 的第一个参数是 {}, 之所以这样, 是因为合并后, 会破坏第一个参数, 所以不能将 defaults 放在第一个.
说明示例如下: (用chrome的console窗口可以看到输出结果)
```javascript
$(function(){
	var defaults = { "id": 1, "name": "test"};
    var options = { "sex": "man", "name": "test2" };
    console.log("合并前, 各个参数如下:");
    console.log(defaults);
    console.log(options);
    console.log("合并后, 各个参数如下:");
    $.extend(defaults, options);
    console.log(defaults); // 这里可以发现 defaults中的内容是 defaults和options合并后的结果
    console.log(options);
});
```
##插件中的公有及私有函数
虽然 javascript 不是纯粹的面向对象的语言, 但是通过其强大的闭包功能, 也能构造出类似其他面向对象语言的公有/私有函数.
上述两个插件中`functionA `与 `functionB`都是公有函数，可以被插件使用者所覆盖。
下面示例中的`functionC`是私有函数，使用者不可以更改
```javascript
//create closure  
(function($){  
	//plugin default value definition
	$.fn.readmore.default = {
		max_column:4
	}
    //plugin definition  
    $.fn.readmore = function(options){  
        debug(this);  
        var opts = $.extend({},$.fn.hilight.defaults,options);  
    	return this.each(function(){  
        var $this = $(this);  
        //build element specific options  
        var o = $.meta ? $.extend({},opts,$this.data()) : opts;          
    }); 
    
    }; 
    //public function definition 
    $.fn.readmore.functionB = function(arg){
    	//...
    }
    //private function definition
    var functionC = function(options){
    	//...
    }  
})(jQuery);  
```

`注意:`
要与`javascript`中定义类进行区别哦~~~

不断学习中。。。