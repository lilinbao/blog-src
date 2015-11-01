title: "使用jQuery validator 开发的一个表单字段验证插件"
date: 2015-09-11 01:07:59
alias: 总结与分享
tags:
- jquery
- 工具
- 笔记
categories: 

---

##前言
jQuery用着一直很爽，正好今天又要写点前端的东西了，总感觉要怎样怎样才是好啊，然后上网看了别人写的自己以前写的，总感觉那些风格都不像公司的这套新代码，最后还是放弃，还是来研究一下公司大牛们写JQ插件的风格，唉，想让自己的代码能万无一失审核通过，自己也着实下了番苦功夫啊，写的完全不一样，不过除了中间有点改动，也还算是一次运行成功，记录一下，有时间再慢慢优化

``` javascript
/*!
 * Login jQuery plugin
 * Copyright 2015 Linbao.
 * Licensed under apache MIT 
 */
 /*! jquery-1.7.2.min.js | License(https://jquery.org/license/) | www.jquery.com */
 /*! jQuery Form Validator | License(https://jquery.org/license/) | http://plugins.jquery.com/formvalidator/ */
+function($){
 if($.login == undefined){
	$.login = function(){
		this.init();
	}
	$.login.prototype = {
		init: function(){
			var self = this;
			self.actionForm = $('.login-form');
			self.btnSubmit  = $('#btn-submit');
			self.initValidator();
			self.actionForm.submit = function(){
				self.register();
			}
		},
		register: function(){
			var self = this;
			if(self.actionForm.valid()){
				self.actionForm.submit();
			}
		},
		initValidator: function(){
			var self = this;
			self.actionForm.validate({
				rules:{
					username:{
						required: true,
						minlength: 6,
						maxlength: 20
					},
					password: {
						required: true,
						minlength: 6,
						maxlength: 20
					}
				},
				messages:{
					username: {
						required: "Sorry, username is required, please enter the username!",
						minlength: "The username is required minlength length of 6! ",
						maxlength: "The username is too long"
					},
					password: {
						required: "Sorry, password is required, please enter the password!",
						minlength: "The password is required minlength of 6! ",
						maxlength: "The password is too long"
					}
				}
			})
		}
	}
}
$(document).ready(function(){
	var login = new $.login();
		
})
}(jQuery)
```
##几个重要的方法说明 
- `$(selector).validate({mapped value})` - 此方法是定义表单的验证规则，json格式，rules定义规则，message定义了表单验证过程中要显示的信息，其节点下的username,password对应表单当中的name
- `$(selector).valid()` - 硬编码触发表单验证
- `$.login.prototype = {}` - 定义一个名为login的js类