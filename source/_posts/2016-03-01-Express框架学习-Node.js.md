---
layout: post
date: 2016-03-01
title: Express框架学习
categories: Node.js
---


query基本用法
---
query是一个可获取客户端get请求路径参数的对象属性，包含着被解析过的请求参数对象，默认为{}。

```javascript
	var express = require('express');
	var app = express();
	 
	app.get("*", function(req, res) {
	    console.log(req.query.参数名);
	    res.send("测试query属性!");
	});
	app.listen(80);
```

通过req.query获取get请求路径的对象参数值。

格式：req.query.参数名；请求路径如下示例：

例1： /search?n=Lenka

	req.query.n  // "Lenka"


例2： /shoes?order=desc&shoe[color]=blue&shoe[type]=converse


	req.query.order  // "desc"
	 
	req.query.shoe.color  // "blue"
	 
	req.query.shoe.type  // "converse"


param基本用法
---
和属性query一样，通过req.param我们也可以获取被解析过的请求参数对象的值。

格式：req.param("参数名")；请求路径如下示例：

例1： 获取请求根路径的参数值，如/?n=Lenka，方法如下：

```javascript
	var express = require('express');
	var app = express();
	 
	app.get("/", function(req, res) {
	    console.log(req.param("n")); //Lenka
	    res.send("使用req.param属性获取请求根路径的参数对象值!");
	});
	 
	app.listen(80);
```
例2：我们也可以获取具有相应路由规则的请求对象，假设路由规则为 /user/:name/，请求路径/user/mike,如下：

```javascript
	app.get("/user/:name/", function(req, res) {
	    console.log(req.param("name")); //mike
	    res.send("使用req.param属性获取具有路由规则的参数对象值!");
	});
```
PS：所谓“路由”，就是指为不同的访问路径，指定不同的处理方法。

看了上面的示例，试一试使用req.param属性解析一个请求路径对象，并获取请求参数值。


params基本用法
---

和param相似，但params是一个可以解析包含着有复杂命名路由规则的请求对象的属性。

格式：req.params.参数名；

例1. 如上课时请求根路径的例子，我们就可以这样获取，如下：

```javascript
	var express = require('express');
	var app = express();
	 
	app.get("/user/:name/", function(req, res) {
	    console.log(req.params.name); //mike
	    res.send("使用req.params属性获取具有路由规则的参数对象值!");
	});
	 
	app.listen(80);
```

查看运行结果，和param属性功能是一样的，同样获取name参数值。

例2：当然我们也可以请求复杂的路由规则，如/user/:name/:id，假设请求地址为：/user/mike/123，如下：

	app.get("/user/:name/:id", function(req, res) {
	    console.log(req.params.id); //"123"
	    res.send("使用req.params属性复杂路由规则的参数对象值!");
	});

	
post请求路径名称
---
例如http://localhost:3000/users/register?mobile=12533671&password=12345678
在路由中这么获得

```javascript

	router.post("/register", function (req,res,next)
	{
	    var restResult = new RestResult();
	    var mobile = req.body.mobile;
	    var password = req.body.password;
	
	
	    //findOne方法,第一个参数数条件,第二个参数是字段投影,第三那个参数是回调函数
	    UserEntity.findOne({mobile:mobile}, "_id", function (err, user)
	    {
	        if (err){//查询异常
	            restResult.errorCode = restResult.SERVER_EXCEPTION_ERROR_CODE;
	            restResult.errorReason = "服务器异常";
	            res.send(restResult);
	            return;
	        }
	        if (user)//手机号已注册
	        {
	            restResult.errorCode = restResult.BUSINESS_ERROR_CODE;
	            restResult.errorReason = "手机号已注册";
	            res.send(restResult);
	            return;
	        }
	
	        var registerUser = new UserEntity({mobile:mobile,password:password});
	        //调用实体的实例保存方法
	        registerUser.save(function (err, row)
	        {
	            if (err){
	                restResult.errorCode = restResult.SERVER_EXCEPTION_ERROR_CODE;
	                restResult.errorReason = "服务器异常";
	                res.send(restResult);
	                return;
	            }
	
	            res.send(restResult);//返回成功结果
	        });
	
	
	    });
	});
	
```