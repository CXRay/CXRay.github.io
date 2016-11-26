---
layout: post
date: 2016-03-02
title: Mongoose学习
categories: Node.js
---


Schema简述
---
Schema —— 一种以文件形式存储的数据库模型骨架，无法直接通往数据库端，也就是说它不具备对数据库的操作能力，仅仅只是数据库模型在程序片段中的一种表现，可以说是数据属性模型(传统意义的表结构)，又或着是“集合”的模型骨架。

那如何去定义一个Schema呢，请看示例：
```javascript
	var mongoose = require("mongoose");
	 
	var TestSchema = new mongoose.Schema({
	    name : { type:String },//属性name,类型为String
	    age  : { type:Number, default:0 },//属性age,类型为Number,默认为0
	    time : { type:Date, default:Date.now },
	    email: { type:String,default:''}
	});
```
基本属性类型有：字符串、日期型、数值型、布尔型(Boolean)、null、数组、内嵌文档等。

Model简述
---
Model —— 由Schema构造生成的模型，除了Schema定义的数据库骨架以外，还具有数据库操作的行为，类似于管理数据库属性、行为的类。

如何通过Schema来创建Model呢，如下示例：

	var db = mongoose.connect("mongodb://127.0.0.1:27017/test");
	 
	// 创建Model
	var TestModel = db.model("test1", TestSchema);
	
test1：数据库中的集合名称,当我们对其添加数据时如果test1已经存在，则会保存到其目录下，如果未存在，则会创建test1集合，然后在保存数据。

拥有了Model，我们也就拥有了操作数据库的金钥匙，在后面的课程中，我们就会学习使用Model来进行增删改查的具体操作，所以，一定要熟悉他的创建格式哟！

如果你想对某个集合有所作为，那就交给Model模型来处理吧，创建一个Model模型，我们需要指定：1.集合名称，2.集合的Schema结构对象，满足这两个条件，我们就会拥有一个操作数据库的金钥匙。

Entity简述
---
Entity —— 由Model创建的实体，使用save方法保存数据，Model和Entity都有能影响数据库的操作，但Model比Entity更具操作性。

使用Model创建Entity，如下示例：

```javascript
var TestEntity = new TestModel({
       name : "Lenka",
       age  : 36,
       email: "lenka@qq.com"
});
console.log(TestEntity.name); // Lenka
console.log(TestEntity.age); // 36
```
创建成功之后，Schema属性就变成了Model和Entity的公共属性了。

创建集合
---

为了方便后面课程的学习和提高您的学习效率，以下基础数据均和后面课程紧密相连，所以必须按照以下结构方式来定义，请勿修改(默认数据库为test，集合为test1)。

```javascript
var mongoose = require("mongoose");
var db = mongoose.connect("mongodb://127.0.0.1:27017/test");
var TestSchema = new mongoose.Schema({
    name : { type:String },
    age  : { type:Number, default:0 },
    email: { type:String },
    time : { type:Date, default:Date.now }
});
var TestModel = db.model("test1", TestSchema );
var TestEntity = new TestModel({
    name : "helloworld",
    age  : 28,
    email: "helloworld@qq.com"
});
TestEntity.save(function(error,doc){
  if(error){
     console.log("error :" + error);
  }else{
     console.log(doc);
  }
});

```

查询
---
find查询： obj.find(查询条件,callback);

```javascript
Model.find({},function(error,docs){
   //若没有向find传递参数，默认的是显示所有文档
});
 
Model.find({ "age": 28 }, function (error, docs) {
  if(error){
    console.log("error :" + error);
  }else{
    console.log(docs); //docs: age为28的所有文档
  }
});
```

find 过滤查询,属性过滤 find(Conditions,field,callback);

field省略或为Null，则返回所有属性。

	//返回只包含一个键值name、age的所有记录
	Model.find({},{name:1, age:1, _id:0}，function(err,docs){
	   //docs 查询结果集
	})
	
说明：我们只需要把显示的属性设置为大于零的数就可以，当然1是最好理解的，_id是默认返回，如果不要显示加上("_id":0)，但是，对其他不需要显示的属性且不是_id，如果设置为0的话将会抛异常或查询无果。

findOne查询

与find相同，但只返回单个文档，也就说当查询到即一个符合条件的数据时，将停止继续查询，并返回查询结果。

单条数据 findOne(Conditions,callback);

	TestModel.findOne({ age: 27}, function (err, doc){
	   // 查询符合age等于27的第一条数据
	   // doc是查询结果
	});

findOne方法，只返回第一个符合条件的文档数据。

findById

与findOne相同，但它只接收文档的_id作为参数，返回单个文档。

单条数据 findById(_id, callback);

	TestModel.findById('obj._id', function (err, doc){
	 //doc 查询结果文档
	});	
	
同样是单条数据，findById和findOne还是有些区别的。


Model保存方法
---
Model提供了一个create方法来对数据进行保存。下面我们来看一下示例：

```javascript
Model.create(文档数据, callback))

Model.create({ name:"model_create", age:26}, function(error,doc){
    if(error) {
        console.log(error);
    } else {
        console.log(doc);
    }
}); 
```

entity保存方法
---
刚刚学习了model的create方法，那接下来就开始学习基于entity的保存方法吧。如下示例：
```javascript
Entity.save(文档数据, callback))

var Entity = new Model({name:"entity_save",age: 27});
 
Entity.save(function(error,doc) {
    if(error) {
        console.log(error);
    } else {
        console.log(doc);
    }
});
```

model调用的是create方法，entity调用的是save方法，记住了嘛！

数据更新
---
学习了数据的保存，接下来我们就开始学习对数据的更新吧！

示例：obj.update(查询条件,更新对象,callback);
```javascript
var conditions = {name : 'test_update'};
//查询条件
var update = {$set : { age : 16 }};
//更新值
TestModel.update(conditions, update, function(error){
    if(error) {
        console.log(error);
    } else {
        console.log('Update success!');
    }
}).exec();
```

删除数据
---
有了数据的保存、更新，就差删除了，下面我们就来学习它吧！
```javascript
示例：obj.remove(查询条件,callback);

var conditions = { name: 'tom' };
 
TestModel.remove(conditions, function(error){
    if(error) {
        console.log(error);
    } else {
        console.log('Delete success!');
    }
});
```

大于小于
---

查询时我们经常会碰到要根据某些字段进行条件筛选查询，比如说Number类型，怎么办呢，我们就可以使用$gt(>)、$lt(<)、$lte(<=)、$gte(>=)操作符进行排除性的查询，如下示例：对类似age字段的数据进行筛选

```javascript
Model.find({"age":{"$gt":18}},function(error,docs){
   //查询所有nage大于18的数据
});
 
Model.find({"age":{"$lt":60}},function(error,docs){
   //查询所有nage小于60的数据
});
 
Model.find({"age":{"$gt":18,"$lt":60}},function(error,docs){
   //查询所有nage大于18小于60的数据
});
```

不等于、不包含
---
$ne(!=)操作符的含义相当于不等于、不包含，查询时我们可通过它进行条件判定，具体使用方法如下：$ne可以匹配单个值，也可以匹配不同类型的值。

	Model.find({ age:{ $ne:24}},function(error,docs){
	    //查询age不等于24的所有数据
	});
	 
	Model.find({name:{$ne:"tom"},age:{$gte:18}},function(error,docs){
	  //查询name不等于tom、age>=18的所有数据
	});
 
包含、等于
---
和$ne操作符相反，$in相当于包含、等于，查询时查找包含于指定字段条件的数据。具体使用方法如下：
	
	Model.find({ age:{ $in: 20}},function(error,docs){
	   //查询age等于20的所有数据
	});
	 
	 
	Model.find({ age:{$in:[20,30]}},function(error,docs){
	  //可以把多个值组织成一个数组
	}); 
	
or
---
$or操作符，可以查询多个键值的任意给定值，只要满足其中一个就可返回，用于存在多个条件判定的情况下使用，如下示例：
 
	Model.find({"$or":[{"name":"yaya"},{"age":28}]},function(error,docs){
	  //查询name为yaya或age为28的全部文档
	});

关键字段是否存在
---
$exists操作符，可用于判断某些关键字段是否存在来进行条件查询。如下示例：

	Model.find({name: {$exists: true}},function(error,docs){
	  //查询所有存在name属性的文档
	});
	 
	Model.find({telephone: {$exists: false}},function(error,docs){
	  //查询所有不存在telephone属性的文档
	});

对返回结果的数量进行限制
---
limit函数,在查询操作中，有时数据量会很大，这时我们就需要对返回结果的数量进行限制，那么我们就可以使用limit函数，通过它来限制结果数量。

限制数量：find(Conditions,fields,options,callback);

	Model.find({},null,{limit:20},function(err,docs){
	    console.log(docs);
	});
	
如果匹配的结果不到20个，则返回匹配数量的结果，也就是说limit函数指定的是上限而非下限。

skip函数和limit类似，都是对返回结果数量进行操作，不同的是skip函数的功能是略过指定数量的匹配结果，返回余下的查询结果。如下示例：

跳过数量：find(Conditions,fields,options,callback);

	Model.find({},null,{skip:4},function(err,docs){
	    console.log(docs);
	});
	
如果查询结果数量中少于4个的话，则不会返回任何结果。

排序
---
sort函数可以将查询结果数据进行排序操作，该函数的参数是一个或多个键/值对，键代表要排序的键名，值代表排序的方向，1是升序，-1是降序。

结果排序：find(Conditions,fields,options,callback);

	Model.find({},null,{sort:{age:-1}},function(err,docs){
	  //查询所有数据，并按照age降序顺序返回数据docs
	});
	
sort函数可根据用户自定义条件有选择性的来进行排序显示数据结果。

ObjectId
---

存储在mongodb集合中的每个文档（document）都有一个默认的主键_id，这个主键名称是固定的，它可以是mongodb支持的任何数据类型，默认是ObjectId。该类型的值由系统自己生成，从某种意义上几乎不会重复，生成过程比较复杂，有兴趣的朋友可以查看源码。

使用过MySQL等关系型数据库的友友们都知道，主键都是设置成自增的。但在分布式环境下，这种方法就不可行了，会产生冲突。为此，MongoDB采用了一个称之为ObjectId的类型来做主键。ObjectId是一个12字节的 BSON 类型字符串。按照字节顺序，一次代表：

4字节：UNIX时间戳
3字节：表示运行MongoDB的机器
2字节：表示生成此_id的进程
3字节：由一个随机数开始的计数器生成的值

	var mongoose = require('mongoose');
	 
	var tSchema = new mongoose.Schema({}); //默认_id:ObjectId类型

每一个文档都有一个特殊的键“_id”，这个键在文档所属的集合中是唯一的。

Schema添加属性值
---
前面我们已经讲述了如何定义一个Schema并赋予某些属性值,那能不能先定义后添加属性呢，答案是可以的，如下所示：

	var mongoose = require('mongoose');
	 
	var TempSchema = new mongoose.Schema;
	 
	TempSchema.add({ name: 'String', email: 'String', age: 'Number' });