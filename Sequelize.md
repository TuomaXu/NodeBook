# Sequelize

Sequelize是一个关系型数据库ORM框架，它基于Promise构建。支持MySQL、PostgreSQL、MariaDB、SQLite和MSSQL几种关系型数据库，功能非常强大。

ORM（Object Relational Mapping）即：对象关系映射，是随着面向对象的软件开发方法的发展而产生的一种技术。 。对象和关系数据是业务实体的两种表现形式，业务实体在内存中表现为对象，在数据库中表现为关系数据。对象（数据）之间存在关联或继承的，而ORM就是将这些关系映射出来，并通过系统业务逻辑（对象操作）实现对关系数据的管理。

在应用中，我们可能会将数据存放在数据库中。在关系型数据库中，操作和查询数据都要基于SQL语句，这样操作即不直观、又不利于数据管理。而通过ORM技术，将数据库中的关系数据（表结构）映成对象模型和关系模型，这样我们就可以像操作对象一样实现数据库的操作。这时，我们对数据增、删、改、查等操作，都是在ORM中抽像出的一些具体的方法而不再是SQL语句。ORM会自动完成数据层的操作，我们完全不用再写SQL语句，甚至不用关系使用的是MySQL还是其它类型的数据库。

### 1，安装MySQL

Sequelize支撑多种数据库，我们这里使用MySQL进行教学，因为在目前移动开发行业内，普遍采用MySQL数据库，因其开源免费且高效。

具体的安装过程不在本文展开，不熟悉的同学可以通过百度获取指导，完成安装。

创建一个DataBase，名字为`myDataBase`；

![图片32](http://7u2rl9.com1.z0.glb.clouddn.com/nodebook32.png)

#### 2，安装Sequelize

安装HelloWorld工程创建流程，创建一个新工程，工程名任意，但不能为`sequelize`，因后面需要安装`sequelize`框架，重名会出现错误，这里我们叫`demo1`。

安装Sequelize框架

```
npm install --save sequelize
```
安装mysql数据驱动，注意要安装mysql2

```
npm install --save mysql2
```

安装完成之后，我们便可以用Sequelize框架链接数据库。

### 3,链接数据库

Sequelize将设置初始化一个连接池，在应用作用域内，应该为每个数据库创建唯一一个实例。Sequelize()是一个构造函数，通过这个函数可以返回一个Sequelize实例：

```
import Sequelize  from 'sequelize';

const sequelize = new Sequelize(
  'my_db,   // 数据库名
  'root',   // 用户名
  'secret', // 用户密码
  {
    'dialect': 'mysql',  // 数据库使用mysql
    'host': 'localhost', // 数据库服务器ip
    'port': 3306         // 数据库服务器端口
  }
);
```
我们编写`index.js`代码进行操作

```
console.log('HelloWorld');
//引入Sequelize框架
import Sequelize  from 'sequelize';
//通过Sequelize构造方法创建Sequelize实例并链接数据库
const sequelize = new Sequelize(
  'myDataBase',
  'root',
  '',
  {
    'dialect': 'mysql',  // 数据库使用mysql
    'host': 'localhost', // 数据库服务器ip
    'port': 3306         // 数据库服务器端口
  }
);

//测试链接是否成功
//sequelize提供一个authenticate()函数测试链接是否成功
//sequelize中所有异步操作都为Promise封装
sequelize
  .authenticate()
  .then(()=>{
    console.log('链接成功');
  })
  .catch((err)=>{
    console.error('链接失败', err);
  });
```

运行结果为：

![图片33](http://7u2rl9.com1.z0.glb.clouddn.com/nodebook33.png)

### 4，定义对象模型（定义表）

模型是数据库表结构到对象的一种映射关系。可以通过Sequelize实例的sequelize.define()方法来定义模型，其语法结构如下

```
 sequelize.define('name', {attributes})
```
我们来定义一个`user`模型，储存字段为`name`和`age`。

```
const User = sequelize.define('user',{
	name:Sequelize.STRING,
	age:Sequelize.INTEGER,
})
```
常用的数据类型有:

```
Sequelize.STRING                      // VARCHAR(255)
Sequelize.STRING(1234)                // VARCHAR(1234)
Sequelize.TEXT                        // TEXT
Sequelize.INTEGER                     // INTEGER
Sequelize.DOUBLE                      // DOUBLE
Sequelize.DATE                        // DATETIME
Sequelize.BOOLEAN                     // TINYINT(1)

```
模型定义好之后，需要和数据库进行同步操作，Sequelize会根据定义的模型在数据库中创建响应的表。

```
//调用此方法同步模型到数据库中
sequelize.sync();
```

我们编写代码运行测试同步结果

```
console.log('HelloWorld');

import Sequelize  from 'sequelize';

const sequelize = new Sequelize(
  'myDataBase',
  'root',
  '',
  {
    'dialect': 'mysql',  // 数据库使用mysql
    'host': 'localhost', // 数据库服务器ip
    'port': 3306         // 数据库服务器端口
  }
);

//定义模型
const User = sequelize.define('user',{
	name:Sequelize.STRING,
	age:Sequelize.INTEGER,
})

sequelize
  .authenticate()
  .then(() => {
    console.log('链接成功');
    	//同步模型
		return sequelize.sync();
  })
	.then(()=>{
		console.log('模型同步成功');
	})
  .catch(err => {
    console.error('发生错误', err);
  });
```

![图片34](http://7u2rl9.com1.z0.glb.clouddn.com/nodebook34.png)

我们看数据库中的表是什么样的：

```
mysql> show tables;
+----------------------+
| Tables_in_mydatabase |
+----------------------+
| users                |
+----------------------+
1 row in set (0.02 sec)
```
可以看出Sequelize在数据库中创建了一个名为`users`的表。

```
mysql> desc users;
+-----------+--------------+------+-----+---------+----------------+
| Field     | Type         | Null | Key | Default | Extra          |
+-----------+--------------+------+-----+---------+----------------+
| id        | int(11)      | NO   | PRI | NULL    | auto_increment |
| name      | varchar(255) | YES  |     | NULL    |                |
| age       | int(11)      | YES  |     | NULL    |                |
| createdAt | datetime     | NO   |     | NULL    |                |
| updatedAt | datetime     | NO   |     | NULL    |                |
+-----------+--------------+------+-----+---------+----------------+
5 rows in set (0.02 sec)
```
我们发现，这边表中除了我们定义的`name`和`age`字段之外，自动添加了三个字段`id`，`createdAt `和`updatedAt `。所以我们在定义模型是，无需在定义这三个字段。

### 5，对象模型操作（增、删、改）

通过Sequelize获取的模型对象实例列是一个DAO（Data Access Object）对象，这些对象会拥有许多操作数据库表的实例对象方法，这个对象会有create()、find()、update()、destroy()等对象操作方法，这些方法会对应的生成INSERT、SELECT、UPDATE、DELETE等SQL语句

#### 增：create操作

create操作是指创建数据记录，我们可通过Model.create()方法添加一条数据：

```
User.create({
  name: '张三',
  age: '30'
})
.then((user)=>{
  // 创建成功，会将创建的对象返回
  console.log(user);//原始对象包含很多内容
  console.log(JSON.stringify(user));//经过JSON编码后的对象只保存查询内容
}).catch((err)=>{
  // 出错了
  console.log(err);	
})
```
原始user打印结果

```
{
  dataValues: 
   { id: 1,
     name: '张三',
     age: '30',
     updatedAt: 2017-08-17T08:16:23.132Z,
     createdAt: 2017-08-17T08:16:23.132Z },
  _previousDataValues: 
   { name: '张三',
     age: '30',
     id: 1,
     createdAt: 2017-08-17T08:16:23.132Z,
     updatedAt: 2017-08-17T08:16:23.132Z },
  _changed: 
   { name: false,
     age: false,
     id: false,
     createdAt: false,
     updatedAt: false },
  _modelOptions: 
   { timestamps: true,
     validate: {},
     freezeTableName: false,
     underscored: false,
     underscoredAll: false,
     paranoid: false,
     rejectOnEmpty: false,
     whereCollection: null,
     schema: null,
     schemaDelimiter: '',
     defaultScope: {},
     scopes: [],
     hooks: {},
     indexes: [],
     name: { plural: 'users', singular: 'user' },
     omitNull: false,
     sequelize: 
      Sequelize {
        options: [Object],
        config: [Object],
        dialect: [Object],
        models: [Object],
        modelManager: [Object],
        connectionManager: [Object],
        importCache: {},
        test: [Object],
        queryInterface: [Object] },
     uniqueKeys: {},
     hasPrimaryKeys: true },
  _options: 
   { isNewRecord: true,
     _schema: null,
     _schemaDelimiter: '',
     attributes: undefined,
     include: undefined,
     raw: undefined,
     silent: undefined },
  hasPrimaryKeys: true,
  __eagerlyLoadedAssociations: [],
  isNewRecord: false 
 }
```
经过JSON编码后的user打印结果：

```
{"id":1,"name":"张三","age":"30","updatedAt":"2017-08-17T08:16:23.132Z","createdAt":"2017-08-17T08:16:23.132Z"}
```

在执行增加操作时，自动创建的字段其值也会自动填充。

####  改：update操作

update操作是指修改数据记录，我们可通过Model.update()方法来修改已经存在的数据记录：

```
User.update({age:30}, {where:{id:1}})
.then(()=>{
  // 修改成功
  console.log('修改成功')
})
.catch((err)=>{
  // 出错了
  console.log(err);	
})
```
#### 删：destroy操作

destroy操作是指删除已存在的数据记录，我们可通过Model.destroy()方法来删除数据：

```
User.destroy({where:{id:1}})
.then(()=>{
  // 删除成功
  console.log('删除成功');
})
.catch((err)=>{
  // 出错了
  console.log(err);	
})
```

### 6，模型查询

find操作是指查询已存在的数据记录，通过Model.findAll(options)方法查找所有数据，通过`options`配置查询条件。常用查询条件：

```
{
	where:{},//一个描述查询限制范围（WHERE条件）的对象
	attributes:[],//要查询的属性（字段）列表数组
	order:[],//可以依次提供多组列名，每一组包含两个元素，第一个是排序字段名，第二个是排序方式
	limit:number,
	offset:number,
}
```

where限制条件构造有三种方式：

* 通过指定属性查找
* 查询时使用字符串替换
* 查询指定范围

```
//查询age等于30的记录
const options = {
	where:{
		age:30
	}
}

//查询age大于30的记录
const options = {
	where:['id > ?',30]
}


//查询age为30，31，32的记录
const options = {
	where:{
		id:[30,31,32]
	}
}
```

```
const options = {
	where:{
		age:30
	}
}
User.findAll(options)
.then((users)=>{
	//查询结果为一个数组
	console.log(JSON.stringify(users));
})
```

输出结果为:

```
[
	{
		"id":1,"name":"张三",
		"age":30,
		"createdAt":"2017-08-17T08:16:23.000Z",
		"updatedAt":"2017-08-17T08:21:50.000Z"
	}
]
```

通过attributes可以配置查询显示哪些字段或者为字段重命名

```
//通过attributes配置
//查询结果中显示name和age两个字段且age字段重命名为年龄
const options = {
	attributes:['name',['age','年龄']]
}

User.findAll(options)
.then((users)=>{
	//查询结果为一个数组
	console.log(JSON.stringify(users));
})
```
输出结果为：

```
[{"name":"张三","年龄":30}]
```

通过order可以配置查询结果排序规则

```
//首先以age升序排列，如age相同以id降序排列
const options = {
	order: [['age','ASC'],['id', 'DESC']],
	attributes:['id','name',['age','年龄']]
}

User.findAll(options)
.then((users)=>{
	//查询结果为一个数组
	console.log(JSON.stringify(users));
})

```
输出结果为：

```
[
	{"id":2,"name":"李四","年龄":30},
	{"id":1,"name":"张三","年龄":30},
	{"id":3,"name":"王五","年龄":31}
]
```

### 7，模型关系

模型（Model）之间存在各种各样的关系，如：一对一(One-To-One ）、一对多（One-To-Many）等。模型间的关系本质上是对其代表的数据库中表之间的关系描述，通过这些关系可以实现数据库中表之间主/外键约束的创建。查询时也可以基于这些关系，生成在数据库中执行的连接查询或复合查询SQL语句。

简单来说，两个数据之间有关系就表示通过一个数据可以找到另一个数据。例如生活中的暗恋关系，A暗恋B，那么通过A可以找到B，但是B并不知道谁在暗恋他，这是单向关系。而恋爱关系是双向的，如果A，B是恋人，那么通过其中一个人就可以找到另一个人。同时关系还有老师和学生们，通过老师可以找到他所有的学生，通过学生能找打他们唯一的老师，这是一对多关系即一个老师对应多个学生。同样，在一个学校内，有N个老师和M个学生，通过一个学生可以找打其上课的老师们，通过一个老师可以找到其听课的学生们，这是多对多关系

在Sequelize的模型也同样可以设置这样的关系

* 一对一
* 一对多
* 多对多

在设置关系之前，我们首先要明确两个概念**源对象**和**目标对象**。例如，在暗恋关系中，A暗恋B，那么A就是源对象，B就是目标对象。通过源对象可以找到目标对象，这个方向不能混淆。

#### 一对一关系

一对一关系就是通过一个对象可以找到唯一的另一个对象，这个关系是单向的。这里我们使用朋友圈的业务举例子：

通过朋友圈的信息，可以找到发送这条信息的用户，且这个用户是唯一的。这样我就可以可以说，朋友圈信息和用户是一对一的关系，即通过一条朋友圈信息可以找到一个用户。这里的朋友圈信息是源对象，用户是目标对象。

在创建关系之前，我们首先要创建两个对象，然后在为这两个对象设置关系。

```
const User = sequelize.define('user',{
	name:Sequelize.STRING,
});

const Message = sequelize.define('message',{
	text:Sequelize.STRING,
});
```
Sequelize的模型中提供了一个`belongsTo()`函数设置一对一的关系。

```
源对象.belongsTo(目标对象);
```

index.js文件

```
console.log('HelloWorld');
//引入Sequelize框架
import Sequelize  from 'sequelize';
//通过Sequelize构造方法创建Sequelize实例并链接数据库
const sequelize = new Sequelize(
  'myDataBase',
  'root',
  '',
  {
    'dialect': 'mysql',  // 数据库使用mysql
    'host': 'localhost', // 数据库服务器ip
    'port': 3306         // 数据库服务器端口
  }
);

//定义模型
const User = sequelize.define('user',{
	name:Sequelize.STRING,
});

const Message = sequelize.define('message',{
	text:Sequelize.STRING,
});

//设置关系
Message.belongsTo(User);
//同步数据，force:true表示删除之前的表
sequelize.sync({force:true});
```
运行代码，显示通过成功之后，我们查看数据的变化。

```
mysql> show tables;
+----------------------+
| Tables_in_mydatabase |
+----------------------+
| messages             |
| users                |
+----------------------+
2 rows in set (0.02 sec)
```
数据库中有两个表`messages `和`users `分别和两个实体关联。

```
mysql> desc messages;
+-----------+--------------+------+-----+---------+----------------+
| Field     | Type         | Null | Key | Default | Extra          |
+-----------+--------------+------+-----+---------+----------------+
| id        | int(11)      | NO   | PRI | NULL    | auto_increment |
| text      | varchar(255) | YES  |     | NULL    |                |
| createdAt | datetime     | NO   |     | NULL    |                |
| updatedAt | datetime     | NO   |     | NULL    |                |
| userId    | int(11)      | YES  | MUL | NULL    |                |
+-----------+--------------+------+-----+---------+----------------+
5 rows in set (0.00 sec)

```

```
mysql> desc users;
+-----------+--------------+------+-----+---------+----------------+
| Field     | Type         | Null | Key | Default | Extra          |
+-----------+--------------+------+-----+---------+----------------+
| id        | int(11)      | NO   | PRI | NULL    | auto_increment |
| name      | varchar(255) | YES  |     | NULL    |                |
| createdAt | datetime     | NO   |     | NULL    |                |
| updatedAt | datetime     | NO   |     | NULL    |                |
+-----------+--------------+------+-----+---------+----------------+
4 rows in set (0.00 sec)
```
我们发现，在`message`表中多了一个`userId`的外键，和``users`表中的`id`字段关联。

为`Message`模型设置了一对一关系之后，`Message`对象会自动添加两个方法

```
message.setUser();
message.getUser();
```
通过这两个方法，我们可以将

我们先创建两个`user`

```
User.create({
  name: '张三',
});

User.create({
  name: '李四',
});
```

查询数据库：

```
mysql> select * from users;
+----+--------+---------------------+---------------------+
| id | name   | createdAt           | updatedAt           |
+----+--------+---------------------+---------------------+
|  1 | 张三   | 2017-08-18 02:51:48 | 2017-08-18 02:51:48 |
|  2 | 李四   | 2017-08-18 02:51:48 | 2017-08-18 02:51:48 |
+----+--------+---------------------+---------------------+
2 rows in set (0.00 sec)
```

构造一个添加朋友圈消息的函数：

```
//函数传入 发送朋友圈消息的userId和内容
function addMessage(userId,text){
  //首先同时做两件事，
  //1，通过传入的userId找到对应user对象
  //2，通过传入的text创建一个message对象
  Promise.all([
    User.findAll({where:{id:userId}}),
    Message.create({text:text})
  ])
  .then((results)=>{
  	//当两个异步操作都完成时，通过返回结果，获取user对象和message对象
    const users = results[0];
    const user = users[0];
    const message = results[1];
	//调用setUser函数，为message对象添加user
    return message.setUser(user);
  })
  .then((message)=>{
    console.log(message);
  })
  .catch((err)=>{
    console.error(err);
  })
}
```
在`index.js`文件中调用此函数：

```
console.log('HelloWorld');
//引入Sequelize框架
import Sequelize  from 'sequelize';
//通过Sequelize构造方法创建Sequelize实例并链接数据库
const sequelize = new Sequelize(
  'myDataBase',
  'root',
  '',
  {
    'dialect': 'mysql',  // 数据库使用mysql
    'host': 'localhost', // 数据库服务器ip
    'port': 3306         // 数据库服务器端口
  }
);

//定义模型
const User = sequelize.define('user',{
	name:Sequelize.STRING,
});

const Message = sequelize.define('message',{
	text:Sequelize.STRING,
});

//设置关系
Message.belongsTo(User);
//同步数据
sequelize.sync();

function addMessage(userId,text){
  Promise.all([
    User.findAll({where:{id:userId}}),
    Message.create({text:text})
  ])
  .then((results)=>{
    const users = results[0];
    const user = users[0];
    const message = results[1];

    return message.setUser(user);
  })
  .then((message)=>{
    console.log(message);
  })
  .catch((err)=>{
    console.error(err);
  })
}

addMessage(1,'测试消息1');
```
查询数据库记录：

```
mysql> select * from messages;
+----+---------------+---------------------+---------------------+--------+
| id | text          | createdAt           | updatedAt           | userId |
+----+---------------+---------------------+---------------------+--------+
|  3 | 测试消息1      | 2017-08-18 03:00:43 | 2017-08-18 03:00:46 |      1 |
+----+---------------+---------------------+---------------------+--------+
1 row in set (0.00 sec)
```
通过`setXXX()`函数我们可以为设置关系的对象添加具体关系。

同样，通过`getXXX()`函数我们可以通过一个已经添加过关系的对象，找到相应关系对象。

我们现在通过一条朋友圈记录找到其发送者，构造一个函数，输入参数为`messageId`，通过Promise封装异步操作，返回对应的User对象

```
function getMessageUser(messageId){
  return new Promise((callBack,reject)=>{
  	//findOne函数查找数据仅会返回一个对象
  	//findAll函数查找数据会返回一个数组，数组中包含对象，一个或多个
    Message.findOne({where:{id:messageId}})
    .then((message)=>{
    	//通过getUser()函数查询与message有关系的对象
      return message.getUser();
    })
    .then((user)=>{
    	//查询成功，返回user
    	//通过Promise返回
      callBack(user);
    })
    .catch((err)=>{
      reject(err);
    })
  })
}
```

在`index.js`文件中调用测试

```
console.log('HelloWorld');
//引入Sequelize框架
import Sequelize  from 'sequelize';
//通过Sequelize构造方法创建Sequelize实例并链接数据库
const sequelize = new Sequelize(
  'myDataBase',
  'root',
  '',
  {
    'dialect': 'mysql',  // 数据库使用mysql
    'host': 'localhost', // 数据库服务器ip
    'port': 3306         // 数据库服务器端口
  }
);

//定义模型
const User = sequelize.define('user',{
	name:Sequelize.STRING,
});

const Message = sequelize.define('message',{
	text:Sequelize.STRING,
});

//设置关系
Message.belongsTo(User);
//同步数据
sequelize.sync();


function getMessageUser(messageId){
  return new Promise((callBack,reject)=>{
    Message.findOne({where:{id:messageId}})
    .then((message)=>{
      return message.getUser();
    })
    .then((user)=>{
      callBack(user);
    })
    .catch((err)=>{
      reject(err);
    })
  })
}

getMessageUser(3)
.then((user)=>{
  console.log(JSON.stringify(user));
})
.catch((err)=>{
  console.error(err);
})
```
输出结果：

```
{"id":1,"name":"张三","createdAt":"2017-08-18T02:51:48.000Z","updatedAt":"2017-08-18T02:51:48.000Z"}
```

设置关系的对象除了可以使用`getXXX()`函数来获取相关联的对象，还可以在查询源对象时，在查询结果中包含目标对象。

首先我们先添加几条记录，方便演示

```
addMessage(1,'测试消息2');
addMessage(2,'测试消息3');
addMessage(2,'测试消息4');
```

数据库内容为：

```
mysql> select * from messages;
+----+---------------+---------------------+---------------------+--------+
| id | text          | createdAt           | updatedAt           | userId |
+----+---------------+---------------------+---------------------+--------+
|  3 | 测试消息1     | 2017-08-18 03:00:43 | 2017-08-18 03:00:46 |      1 |
|  4 | 测试消息2     | 2017-08-18 03:30:17 | 2017-08-18 03:30:19 |      1 |
|  5 | 测试消息3     | 2017-08-18 03:30:17 | 2017-08-18 03:30:19 |      2 |
|  6 | 测试消息4     | 2017-08-18 03:30:17 | 2017-08-18 03:30:20 |      2 |
+----+---------------+---------------------+---------------------+--------+
4 rows in set (0.01 sec)
```

我们使用`include`字段进行查询条件配置：

```
const options = {
  order:['id'],//默认为升序规则
  attributes:['id','text'],
  //include对象中可以使用where, attributes，limit等字段
  include:[
    {
      model:User,//指定包含的关系对象
      attributes:['id','name'],
    },
  ]
};
```
通过使用上述的查询配置项，在查询Message对象是，会将User作为其字段包含在每条Message对象中。

首先构造一个查询函数：

```
function getMessagesIncludeUser(){
  return new Promise((callBack,reject)=>{
    const options = {
      order:['id'],//默认为升序规则
      attributes:['id','text'],
      include:[
        {
          model:User,
          attributes:['id','name'],
        },
      ]
    };

    Message.findAll(options)
    .then((messages)=>{
      callBack(messages);
    })
    .catch((err)=>{
      reject(err);
    })
  })
}
```

调用此函数进行测试

```
console.log('HelloWorld');
//引入Sequelize框架
import Sequelize  from 'sequelize';
//通过Sequelize构造方法创建Sequelize实例并链接数据库
const sequelize = new Sequelize(
  'myDataBase',
  'root',
  '',
  {
    'dialect': 'mysql',  // 数据库使用mysql
    'host': 'localhost', // 数据库服务器ip
    'port': 3306         // 数据库服务器端口
  }
);

//定义模型
const User = sequelize.define('user',{
	name:Sequelize.STRING,
});

const Message = sequelize.define('message',{
	text:Sequelize.STRING,
});

//设置关系
Message.belongsTo(User);
//同步数据，force:true表示删除之前的表
sequelize.sync();

function getMessagesIncludeUser(){
  return new Promise((callBack,reject)=>{
    const options = {
      order:['id'],//默认为升序规则
      attributes:['id','text'],
      include:[
        {
          model:User,
          attributes:['id','name'],
        },
      ]
    };

    Message.findAll(options)
    .then((messages)=>{
      callBack(messages);
    })
    .catch((err)=>{
      reject(err);
    })
  })
}

getMessagesIncludeUser()
.then((messages)=>{
  console.log(JSON.stringify(messages));
})
.catch((err)=>{
  console.error(err);
})
```

运行结果为：

```
[ 
  {"id":3,"text":"测试消息1","user":{"id":1,"name":"张三"}},
  {"id":4,"text":"测试消息2","user":{"id":1,"name":"张三"}},
  {"id":5,"text":"测试消息3","user":{"id":2,"name":"李四"}},
  {"id":6,"text":"测试消息4","user":{"id":2,"name":"李四"}}
]
```
可以看到，user作为一个字段被包含在了message对象中。

有一个问题需要注意：设置`Message.belongsTo(User)`之后，`Message`对象会添加`setUser()`和`getUser()`方法，但`User`对象不会添加，而且在查询`Message`时可以`inculde User`，但是查询`User`时不能`inculde Message`。

如果想通过User对象找到Message对象，需要添加`User.belongsTo(Message)`关系，这两个关系不冲突，可以同时添加。

#### 一对多

一对多的关系和一对一的类似，不同的是，在一对多关系中，通过一个源对象可以查找到多个目标对象。例如朋友圈消息和其中的的图片便是一对多的关系，通过一个朋友圈消息对象可以查找到和其匹配的多个图片对象。

我们就用这个案例讲解一对多的实现。

首先我们需要创建一个`Image`模型来储存图片信息，在`Image`模型中，保存图片的链接地址。

```
const Image =sequelize.define('image',{
  url:Sequelize.STRING,
});
```

然后同`hasMany()`函数设置一对多关系

```
源对象.hasMany(目标对象);
```

```
Message.hasMany(Image);
```

运行完整代码测试结果：

```
console.log('HelloWorld');
//引入Sequelize框架
import Sequelize  from 'sequelize';
//通过Sequelize构造方法创建Sequelize实例并链接数据库
const sequelize = new Sequelize(
  'myDataBase',
  'root',
  '',
  {
    'dialect': 'mysql',  // 数据库使用mysql
    'host': 'localhost', // 数据库服务器ip
    'port': 3306         // 数据库服务器端口
  }
);

//定义模型
const User = sequelize.define('user',{
	name:Sequelize.STRING,
});

const Message = sequelize.define('message',{
	text:Sequelize.STRING,
});

const Image =sequelize.define('image',{
  url:Sequelize.STRING,
});

//设置关系
Message.belongsTo(User);

Message.hasMany(Image);
//同步数据，force:true表示删除之前的表
sequelize.sync();
```

查看数据库变化：
```
mysql> show tables;
+----------------------+
| Tables_in_mydatabase |
+----------------------+
| images               |
| messages             |
| users                |
+----------------------+
3 rows in set (0.00 sec)
```
增加了一个`images`表。

查看`images`表结构

```
mysql> desc images;
+-----------+--------------+------+-----+---------+----------------+
| Field     | Type         | Null | Key | Default | Extra          |
+-----------+--------------+------+-----+---------+----------------+
| id        | int(11)      | NO   | PRI | NULL    | auto_increment |
| url       | varchar(255) | YES  |     | NULL    |                |
| createdAt | datetime     | NO   |     | NULL    |                |
| updatedAt | datetime     | NO   |     | NULL    |                |
| messageId | int(11)      | YES  | MUL | NULL    |                |
+-----------+--------------+------+-----+---------+----------------+
5 rows in set (0.00 sec)
```
在`images`表中，除了我们定义的`url`和系统自动添加的字段外，还多了一个`messageId `的外键和`messages`表中的`id`关联。

在关系设置完成之后，Sequelize会在源对象中添加对目标对象的操作方法：

```
setImages();//此方法会先删除之前的关系，然后设置新关系，输出参数为数组
addImages();//此方法不会删除之前的关系，单纯添加新关系，输出参数为数组
addImage();//输出参数为一个对象
getImages();
getImage();
```

构造一个发送带图片朋友圈消息的方法：

```
function addMessageWithImages(userId,text,images){
  //将异步操作返回的Promise对象放入此数组
  const promises = [];
  promises.push(User.findOne({where:{id:userId}}));
  promises.push(Message.create({text:text}));
  for (let i = 0; i < images.length; i++) {
    const url = images[i];
    const promise = Image.create({url:url})
    promises.push(promise);
  }
  //使用Promise.all()函数，一起执行所有异步操作
  Promise.all(promises)
  .then((results)=>{
    //全部异步操作都完成后，会触发此回调
    const user = results[0];
    const message = results[1];
    const images = [];
    for (let i = 2; i < results.length; i++) {
      images.push(results[i]);
    }
    return Promise.all([
      message.setImages(images),
      message.setUser(user),
    ]);
  })
  .then(()=>{
    console.log('操作成功');
  })
  .catch((err)=>{
    console.error(err);
  })
}
```

编写代码测试此函数：

```
console.log('HelloWorld');
//引入Sequelize框架
import Sequelize  from 'sequelize';
//通过Sequelize构造方法创建Sequelize实例并链接数据库
const sequelize = new Sequelize(
  'myDataBase',
  'root',
  '',
  {
    'dialect': 'mysql',  // 数据库使用mysql
    'host': 'localhost', // 数据库服务器ip
    'port': 3306         // 数据库服务器端口
  }
);

//定义模型
const User = sequelize.define('user',{
	name:Sequelize.STRING,
});

const Message = sequelize.define('message',{
	text:Sequelize.STRING,
});

const Image =sequelize.define('image',{
  url:Sequelize.STRING,
});

//设置关系
Message.belongsTo(User);
Message.hasMany(Image);
//同步数据，force:true表示删除之前的表
sequelize.sync();

function addMessageWithImages(userId,text,images){
  //将异步操作返回的Promise对象放入此数组
  const promises = [];
  promises.push(User.findOne({where:{id:userId}}));
  promises.push(Message.create({text:text}));
  for (let i = 0; i < images.length; i++) {
    const url = images[i];
    const promise = Image.create({url:url})
    promises.push(promise);
  }
  //使用Promise.all()函数，一起执行所有异步操作
  Promise.all(promises)
  .then((results)=>{
    //全部异步操作都完成后，会触发此回调
    const user = results[0];
    const message = results[1];
    const images = [];
    for (let i = 2; i < results.length; i++) {
      images.push(results[i]);
    }
    return Promise.all([
      message.setImages(images),
      message.setUser(user),
    ]);
  })
  .then(()=>{
    console.log('操作成功');
  })
  .catch((err)=>{
    console.error(err);
  })
}

const images = [
  '1.png',
  '2.png',
  '3.png',
]
addMessageWithImages(1,'测试消息image3',images);
```
查看数据库变化：

```
mysql> select * from messages;
+----+--------------------+---------------------+---------------------+--------+
| id | text               | createdAt           | updatedAt           | userId |
+----+--------------------+---------------------+---------------------+--------+
|  3 | 测试消息1          | 2017-08-18 03:00:43 | 2017-08-18 03:00:46 |      1 |
|  4 | 测试消息2          | 2017-08-18 03:30:17 | 2017-08-18 03:30:19 |      1 |
|  5 | 测试消息3          | 2017-08-18 03:30:17 | 2017-08-18 03:30:19 |      2 |
|  6 | 测试消息4          | 2017-08-18 03:30:17 | 2017-08-18 03:30:20 |      2 |
|  7 | 测试消息image3     | 2017-08-18 06:36:08 | 2017-08-18 06:36:10 |      1 |
+----+--------------------+---------------------+---------------------+--------+
5 rows in set (0.00 sec)
```

```
mysql> select * from images;
+----+-------+---------------------+---------------------+-----------+
| id | url   | createdAt           | updatedAt           | messageId |
+----+-------+---------------------+---------------------+-----------+
|  1 | 1.png | 2017-08-18 06:36:08 | 2017-08-18 06:36:10 |         7 |
|  2 | 2.png | 2017-08-18 06:36:08 | 2017-08-18 06:36:10 |         7 |
|  3 | 3.png | 2017-08-18 06:36:08 | 2017-08-18 06:36:10 |         7 |
+----+-------+---------------------+---------------------+-----------+
3 rows in set (0.00 sec)
```
我们看到，数据已经存入数据库。

下面我们通过查询方法，查询所有`Message`记录并包含`user`和`image`关系：

```
function getAllMessage(){
  return new Promise((callBack,reject)=>{
    const options = {
      order:['id'],//默认为升序规则
      attributes:['id','text'],
      include:[
        {
          model:User,
          attributes:['id','name'],
        },
        {
          model:Image,
          attributes:['id','url'],
        }
      ]
    };

    Message.findAll(options)
    .then((messages)=>{
      callBack(messages);
    })
    .catch((err)=>{
      reject(err);
    })
  })
}
```

编写代码测试运行结果：

```
console.log('HelloWorld');
//引入Sequelize框架
import Sequelize  from 'sequelize';
//通过Sequelize构造方法创建Sequelize实例并链接数据库
const sequelize = new Sequelize(
  'myDataBase',
  'root',
  '',
  {
    'dialect': 'mysql',  // 数据库使用mysql
    'host': 'localhost', // 数据库服务器ip
    'port': 3306         // 数据库服务器端口
  }
);

//定义模型
const User = sequelize.define('user',{
	name:Sequelize.STRING,
});

const Message = sequelize.define('message',{
	text:Sequelize.STRING,
});

const Image =sequelize.define('image',{
  url:Sequelize.STRING,
});

//设置关系
Message.belongsTo(User);
Message.hasMany(Image);
//同步数据，force:true表示删除之前的表
sequelize.sync();

function getAllMessage(){
  return new Promise((callBack,reject)=>{
    const options = {
      order:['id'],//默认为升序规则
      attributes:['id','text'],
      include:[
        {
          model:User,
          attributes:['id','name'],
        },
        {
          model:Image,
          attributes:['id','url'],
        }
      ]
    };

    Message.findAll(options)
    .then((messages)=>{
      callBack(messages);
    })
    .catch((err)=>{
      reject(err);
    })
  })
}

getAllMessage()
.then((messages)=>{
  console.log(JSON.stringify(messages));
})
.catch((err)=>{
  console.error(err);
})
```
运行结构结构化展示
```
[
  {
    "id":3,
    "text":"测试消息1",
    "user":{
      "id":1,
      "name":"张三"
    },
    "images":[]
  },
  {
    "id":4,
    "text":"测试消息2",
    "user":{
      "id":1,
      "name":"张三"
    },
    "images":[]},
  {
    "id":5,
    "text":"测试消息3",
    "user":{
      "id":2,
      "name":"李四"
    },
    "images":[]},
  {
    "id":6,
    "text":"测试消息4",
    "user":{
      "id":2,
      "name":"李四"
    },
    "images":[]},
  {
    "id":7,
    "text":"测试消息image3",
    "user":{
      "id":1,
      "name":"张三"
    },
    "images":[
      {"id":1,"url":"1.png"},
      {"id":2,"url":"2.png"},
      {"id":3,"url":"3.png"}
    ]
  }
]
```
#### 多对多

多对多的关系要稍复杂一些，例如在学校中，有很多老师同时也有很多学生。通过一个老师可以找到其所教的学生，通过一个学生可以找到所上课的老师。这样有交叉关系的联系，我们使用多对多描述。

多对多的关系是双向的，我们不分源对象和目标对象，我们只要设置的两个对象之间的关系，即可以通过A找到B，也能通过B找到A

我们这里用一个学生选课管理的小系统来讲解多对多的关系。

创建教室对象和学生对象

```
const Teacher = sequelize.define('teacher',{
  name:Sequelize.STRING,
});

const Student = sequelize.define('student',{
  name:Sequelize.STRING,
});
```
模型对象定位完成之后，我们便可以设置模型直接的关系，多对多的关系设置通过

```
模型A.belongsToMany(模型B,{through:'关系表名'})
```
关系需要双向设定，通过新建一个关系表来记录其关系。

```
Teacher.belongsToMany(Student,{through:'TeacherStudent'});
Student.belongsToMany(Teacher,{through:'TeacherStudent'});
```

我们看设置关系之后数据库的变化：

```
mysql> show tables;
+----------------------+
| Tables_in_mydatabase |
+----------------------+
| TeacherStudent       |
| images               |
| messages             |
| students             |
| teachers             |
| users                |
+----------------------+
6 rows in set (0.00 sec)
```
在添加`students `和`messages `之外自动添加了`TeacherStudent `来记录多对多关系。

我们看一下`TeacherStudent `表结构

```
mysql> desc TeacherStudent;
+-----------+----------+------+-----+---------+-------+
| Field     | Type     | Null | Key | Default | Extra |
+-----------+----------+------+-----+---------+-------+
| createdAt | datetime | NO   |     | NULL    |       |
| updatedAt | datetime | NO   |     | NULL    |       |
| teacherId | int(11)  | NO   | PRI | NULL    |       |
| studentId | int(11)  | NO   | PRI | NULL    |       |
+-----------+----------+------+-----+---------+-------+
4 rows in set (0.00 sec)
```
这个表中有两个外键分别和`students `和`messages `中的`id`字段关联。

关系设定之后，Sequelize会对对象添加函数：

对`Teacher`对象添加：

```
getStudents(),
setStudents(),
addStudent(),
addStudents()
```

对`Student`对象添加：

```
getTeachers(),
setTeachers(),
addTeacher(),
addTeachers()
```
添加一些学生和老师用来进行测试：

```
Teacher.create({name:'教师A'});
Teacher.create({name:'教师B'});
Teacher.create({name:'教师C'});
```

```
mysql> select * from teachers;
+----+---------+---------------------+---------------------+
| id | name    | createdAt           | updatedAt           |
+----+---------+---------------------+---------------------+
|  7 | 教师A   | 2017-08-18 07:29:03 | 2017-08-18 07:29:03 |
|  8 | 教师B   | 2017-08-18 07:29:03 | 2017-08-18 07:29:03 |
|  9 | 教师C   | 2017-08-18 07:29:03 | 2017-08-18 07:29:03 |
+----+---------+---------------------+---------------------+
3 rows in set (0.00 sec)
```

```
Student.create({name:'学生1'});
Student.create({name:'学生2'});
Student.create({name:'学生3'});
Student.create({name:'学生4'});
Student.create({name:'学生5'});
Student.create({name:'学生6'});
Student.create({name:'学生7'});
Student.create({name:'学生8'});
```

```
mysql> select * from students;
+----+---------+---------------------+---------------------+
| id | name    | createdAt           | updatedAt           |
+----+---------+---------------------+---------------------+
|  1 | 学生2   | 2017-08-18 07:27:51 | 2017-08-18 07:27:51 |
|  2 | 学生1   | 2017-08-18 07:27:51 | 2017-08-18 07:27:51 |
|  3 | 学生3   | 2017-08-18 07:27:51 | 2017-08-18 07:27:51 |
|  4 | 学生4   | 2017-08-18 07:27:51 | 2017-08-18 07:27:51 |
|  5 | 学生5   | 2017-08-18 07:27:51 | 2017-08-18 07:27:51 |
|  6 | 学生6   | 2017-08-18 07:27:51 | 2017-08-18 07:27:51 |
|  7 | 学生7   | 2017-08-18 07:27:51 | 2017-08-18 07:27:51 |
|  8 | 学生8   | 2017-08-18 07:27:51 | 2017-08-18 07:27:51 |
+----+---------+---------------------+---------------------+
8 rows in set (0.00 sec)
```


我们构建一个选课函数，通过输入学生ID和教师ID：

```
function selectTeacher(studentId,teacherId){
  Promise.all([
    Teacher.findOne({where:{id:teacherId}}),
    Student.findOne({where:{id:studentId}})
  ])
  .then((results)=>{
    const tea = results[0];
    const stu = results[1];
    
    return stu.addTeacher(tea);  
  })
  .then(()=>{
    console.log('添加成功');
  })
  .catch(()=>{
    console.log('添加失败');
  })
}
```

调用函数加入测试关系：

```
selectTeacher(1,7);
selectTeacher(1,8);
selectTeacher(2,9);
selectTeacher(2,8);
selectTeacher(3,9);
selectTeacher(4,9);
selectTeacher(5,9);
selectTeacher(6,7);
selectTeacher(7,7);
selectTeacher(8,7);
```

```
mysql> select * from TeacherStudent;
+---------------------+---------------------+-----------+-----------+
| createdAt           | updatedAt           | teacherId | studentId |
+---------------------+---------------------+-----------+-----------+
| 2017-08-18 07:37:43 | 2017-08-18 07:37:43 |         7 |         1 |
| 2017-08-18 07:37:44 | 2017-08-18 07:37:44 |         7 |         6 |
| 2017-08-18 07:37:44 | 2017-08-18 07:37:44 |         7 |         7 |
| 2017-08-18 07:37:44 | 2017-08-18 07:37:44 |         7 |         8 |
| 2017-08-18 07:37:43 | 2017-08-18 07:37:43 |         8 |         1 |
| 2017-08-18 07:37:43 | 2017-08-18 07:37:43 |         8 |         2 |
| 2017-08-18 07:37:43 | 2017-08-18 07:37:43 |         9 |         2 |
| 2017-08-18 07:37:43 | 2017-08-18 07:37:43 |         9 |         3 |
| 2017-08-18 07:37:44 | 2017-08-18 07:37:44 |         9 |         4 |
| 2017-08-18 07:37:44 | 2017-08-18 07:37:44 |         9 |         5 |
+---------------------+---------------------+-----------+-----------+
10 rows in set (0.00 sec)
```
添加完测试数据之后，我们进行多对多关系查找：

通过一个学生查询其所选的教师：

```
function findTeachersByStudent(studentId){
  return new Promise((callBack,reject)=>{
    Student.findOne({where:{id:studentId}})
    .then((student)=>{
      return student.getTeachers({
        attributes:['id','name']
      });
    })
    .then((teachers)=>{
      callBack(teachers);
    })
    .catch((err)=>{
      reject(err);
    })
  })
}
```

通过一个教师查询选其的学生：

```
function findStudentByTeacher(teacherId){
  return new Promise((callBack,reject)=>{
    Teacher.findOne({where:{id:teacherId}})
    .then((teacher)=>{
      return teacher.getStudents({
        attributes:['id','name']
      });
    })
    .then((student)=>{
      callBack(student);
    })
    .catch((err)=>{
      reject(err);
    })
  })
}
```



#### 关系嵌套


通过Sequelize可以处理嵌套关系

例如：一个`Message`包含一个`User`记录，多个`Image`记录，多个`Comment`记录，记录对象中还包含一个`User`记录。

```
{
	id:1
	text:'测试消息1',
	user:{
		id:1
		name:'Tom'
	},
	image:[
		{
			id:1,
			path:'1.png',
		},
		{
			id:2,
			path:'2.png',
		}
	],
	comment:[
		{
			id:1,
			text:'评论1',
			user:{
				id:2
				name:'Jhon'
			}
		},
		{
			id:2,
			text:'评论2',
			user:{
				id:1
				name:'Tom'
			}
		}
	]
}
```

这样的数据可以可以在移动端应用的页面中进行显示，类似微信朋友圈或者QQ空间。

根据这样的需求，我们需要定义如下模型

```
const User = sequelize.define('user',{
	name:Sequelize.STRING,
});

const Message = sequelize.define('message',{
	text:Sequelize.STRING,
});

const Image =sequelize.define('image',{
  url:Sequelize.STRING,
});

const Comment  = sequelize.define('comment',{
  text:Sequelize.STRING,
});
```

为模型设置如下关系

```
Message.belongsTo(User);
Message.hasMany(Comment);
Comment.belongsTo(User);
```

运行代码，通过配置过程

```
sequelize.sync({force:true});
```
查看数据中的变化

```
mysql> desc messages;
+-----------+--------------+------+-----+---------+----------------+
| Field     | Type         | Null | Key | Default | Extra          |
+-----------+--------------+------+-----+---------+----------------+
| id        | int(11)      | NO   | PRI | NULL    | auto_increment |
| text      | varchar(255) | YES  |     | NULL    |                |
| createdAt | datetime     | NO   |     | NULL    |                |
| updatedAt | datetime     | NO   |     | NULL    |                |
| userId    | int(11)      | YES  | MUL | NULL    |                |
+-----------+--------------+------+-----+---------+----------------+
5 rows in set (0.00 sec)
```

```
mysql> desc users;
+-----------+--------------+------+-----+---------+----------------+
| Field     | Type         | Null | Key | Default | Extra          |
+-----------+--------------+------+-----+---------+----------------+
| id        | int(11)      | NO   | PRI | NULL    | auto_increment |
| name      | varchar(255) | YES  |     | NULL    |                |
| createdAt | datetime     | NO   |     | NULL    |                |
| updatedAt | datetime     | NO   |     | NULL    |                |
+-----------+--------------+------+-----+---------+----------------+
4 rows in set (0.00 sec)
```


```
mysql> desc comments;
+-----------+--------------+------+-----+---------+----------------+
| Field     | Type         | Null | Key | Default | Extra          |
+-----------+--------------+------+-----+---------+----------------+
| id        | int(11)      | NO   | PRI | NULL    | auto_increment |
| text      | varchar(255) | YES  |     | NULL    |                |
| createdAt | datetime     | NO   |     | NULL    |                |
| updatedAt | datetime     | NO   |     | NULL    |                |
| messageId | int(11)      | YES  | MUL | NULL    |                |
| userId    | int(11)      | YES  | MUL | NULL    |                |
+-----------+--------------+------+-----+---------+----------------+
6 rows in set (0.00 sec)
```


构建测试方法：

```
function addUser(name){
  User.create({name:name});
}
```

```
function addMessage(userId,text){
  Promise.all([
    User.findAll({where:{id:userId}}),
    Message.create({text:text})
  ])
  .then((results)=>{
    const users = results[0];
    const user = users[0];
    const message = results[1];

    return message.setUser(user);
  })
  .then((message)=>{
    console.log(message);
  })
  .catch((err)=>{
    console.error(err);
  })
}
```

```
function addComment(uid,messageId,text){
  Promise.all([
    User.findOne({where:{id:userId}}),
    Message.findOne({where:{id:messageId}}),
    Comment.create({text:text})
  ])
  .then((results)=>{
    const user = results[0];
    const message = results[1];
    const comment = results[2];
    comment.addUser(user);
    message.addComment(comment);
  })
}
```

通过关系嵌套，构造查询函数：

```
function getAllMessage(){
  return new Promise((callBack,reject)=>{
    const options = {
      order:['id'],//默认为升序规则
      attributes:['id','text'],
      include:[
        {
          model:User,
          attributes:['id','name'],
        },
        {
          model:Comment,
          attributes:['id','text'],
          include:[
            {
              model:User,
              attributes:['id','name'],
            }
          ]
        }
      ]
    };

    Message.findAll(options)
    .then((messages)=>{
      callBack(messages);
    })
    .catch((err)=>{
      reject(err);
    })
  })
}
```
通过测试函数添加测试数据

```
addUser('用户1');
addUser('用户2');
addUser('用户3');
```
查看数据库中数据变化

```
mysql> select * from users;
+----+---------+---------------------+---------------------+
| id | name    | createdAt           | updatedAt           |
+----+---------+---------------------+---------------------+
|  1 | 用户1   | 2017-08-28 01:18:21 | 2017-08-28 01:18:21 |
|  2 | 用户2   | 2017-08-28 01:18:21 | 2017-08-28 01:18:21 |
|  3 | 用户3   | 2017-08-28 01:18:21 | 2017-08-28 01:18:21 |
+----+---------+---------------------+---------------------+
3 rows in set (0.00 sec)
```

```
addMessage(1,'测试消息1');
addMessage(1,'测试消息2');
```

```
mysql> select * from messages;
+----+---------------+---------------------+---------------------+--------+
| id | text          | createdAt           | updatedAt           | userId |
+----+---------------+---------------------+---------------------+--------+
|  1 | 测试消息2     | 2017-08-28 01:19:54 | 2017-08-28 01:19:56 |      1 |
|  2 | 测试消息1     | 2017-08-28 01:19:54 | 2017-08-28 01:19:56 |      1 |
+----+---------------+---------------------+---------------------+--------+
2 rows in set (0.01 sec)

```

```
mysql> select * from comments;
+----+---------+---------------------+---------------------+-----------+--------+
| id | text    | createdAt           | updatedAt           | messageId | userId |
+----+---------+---------------------+---------------------+-----------+--------+
|  7 | 评论1   | 2017-08-28 01:24:01 | 2017-08-28 01:24:03 |         1 |      2 |
|  8 | 评论2   | 2017-08-28 01:24:01 | 2017-08-28 01:24:03 |         1 |      3 |
|  9 | 评论3   | 2017-08-28 01:24:01 | 2017-08-28 01:24:03 |         2 |      3 |
+----+---------+---------------------+---------------------+-----------+--------+
3 rows in set (0.01 sec)
```

通过查询方法，查询所有数据:

```
[ 
  {
    "id":1,
    "text":"测试消息2",
    "user":{
      "id":1,
      "name":"用户1"
    },
    "comments":[
      {
        "id":7,
        "text":"评论1",
        "user":{
          "id":2,
          "name":"用户2"
        }
      },
      {
        "id":8,
        "text":"评论2",
        "user":{
          "id":3,
          "name":"用户3"
        }
      }
    ]
  },
  {
    "id":2,
    "text":"测试消息1",
    "user":{
      "id":1,
      "name":"用户1"
    },
    "comments":[
      {
        "id":9,
        "text":"评论3",
        "user":{
          "id":3,
          "name":"用户3"
        }
      }
    ]
  }
]
```