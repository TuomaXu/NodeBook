# Express

Express 是一个基于 Node.js 平台的极简、灵活的 web 应用开发框架，我们主要使用其请求路由功能。因node原生的http服务还是相对基础，不能满足我们对API服务的快速快发。

### 1，安装Express

按照HelloWorld工程创建方式，创建一个新工程。

在工程根目录通过npm安装Express框架

```
npm install express --save
```
![图片15](http://7u2rl9.com1.z0.glb.clouddn.com/nodebook15.png)

安装完成之后，我们对`index.js`代码进行修改

```
console.log('HelloWorld');
//引入Express框架
import express from 'express';
//通过Express框架创建一个ExpressApp对象
const app = express();
//开启监听服务
const server = app.listen(3000,()=>{
	console.log('开启成功，访问http://localhost:3000');
});

```
这个工程运行之后，如果访问会报错

![图片16](http://7u2rl9.com1.z0.glb.clouddn.com/nodebook16.png)

因为，我们没有在Express中设置任何请求的响应，所有无法访问任何资源。

### 2，静态文件服务

通过URL请求服务器上的静态资源文件，例如图片文件。

在工程根目录下创建一个`source`文件夹，在内存放几张图片，以便测试。

![图片17](http://7u2rl9.com1.z0.glb.clouddn.com/nodebook17.png)

修改`index.js`代码

```
console.log('HelloWorld');

//引入Express框架
import express from 'express';
//通过Express框架创建一个ExpressApp对象
const app = express();

//使用express自带的static中间件，对source目录提供静态文件服务
app.use(express.static('./source'));

//开启监听服务
const server = app.listen(3000,()=>{
	console.log('开启成功，访问http://localhost:3000');
});
```
运行`index.js`

```
babel-node index.js
```
现在我们就可以通过链接直接访问`source`下的文件

```
http://localhost:3000/1.png
```
![图片18](http://7u2rl9.com1.z0.glb.clouddn.com/nodebook18.png)

但是，我们一般不直接访问一个文件资源，而是在请求是添加一个虚拟路径，以让请求更好的分类，

我们希望用户用下面的地址请求图片文件

```
http://localhost:3000/source/1.png
```
Express的static中间件支持设置访问虚拟路径

```
console.log('HelloWorld');

//引入Express框架
import express from 'express';
//通过Express框架创建一个ExpressApp对象
const app = express();

//使用express自带的static中间件，对source目录提供静态文件服务
//添加虚拟路径即可
app.use('/source',express.static('./source'));

//开启监听服务
const server = app.listen(3000,()=>{
	console.log('开启成功，访问http://localhost:3000');
});
```
运行`index.js`


```
babel-node index.js
```
现在我们就可以通过链接直接访问`source`下的文件

```
http://localhost:3000/source/1.png
```
![图片19](http://7u2rl9.com1.z0.glb.clouddn.com/nodebook19.png)

一般在后台API设计是，都会对不同的请求添加响应的虚拟路径，以更方便和清晰的区分API服务。

### 3，GET请求

发送GET请求是，请求查询字符串通过`?`与请求url进行分离，多个参数使用`&`分离，例如：

```
http://localhost:3000/api/getPerson?age=10&type=A
```
在服务端，我们需要能够获取到用户提交的参数并做响应运算，返回用户所需的信息。

```
console.log('HelloWorld');

//引入Express框架
import express from 'express';
//通过Express框架创建一个ExpressApp对象
const app = express();

//使用app的get函数，配置请求路由和响应函数
//响应函数携带req和res两个对象
//req是请求对象，包含请求所携带的全部信息
//res是响应对象，通过对res操作，可以将服务端数据返回客户端
app.get('/api/getPerson', (req, res)=>{
	//通过req获取用户提交的参数
	//req中的query字段包含了用户通过GET请求提交的全部参数
	const query = req.query;
	console.log(query);
	//构造一个模拟数据返回客户端
	const per = {
		name:'Tom',
		age:10,
		type:'A'
	}
	//使用res的json方法，将对象进行JSON编码后返回
	res.json(per);
});

//开启监听服务
const server = app.listen(3000,()=>{
	console.log('开启成功，访问http://localhost:3000');
});
```
运行`index.js`

```
babel-node index.js
```
通过浏览器请求`http://localhost:3000/api/getPerson?age=10&type=A`

浏览器返回`{"name":"Tom","age":10,"type":"A"}`

![图片20](http://7u2rl9.com1.z0.glb.clouddn.com/nodebook20.png)

命令行打印`{ age: '10', type: 'A' }`

![图片21](http://7u2rl9.com1.z0.glb.clouddn.com/nodebook21.png)


### 4，POST请求-纯数据表单

我们构造一个添加Person的API接口，想接口提交用户的`name`,`age`和`type`。

我们在请求的时候，需要将提交的参数放入一个对象中

```
const person = {
	name:'Jhon',
	age:11,
	type:B
}
```
然后通过JSON对这个对象进行编码，将编码后的结果放入`body`字段。

通过浏览器地址栏无法模拟POST操作，我们通过PostMan这个软件模拟一个POST请求操作。

请求地址为:`http://localhost:3000/api/postPerson`

![图片22](http://7u2rl9.com1.z0.glb.clouddn.com/nodebook22.png)

![图片23](http://7u2rl9.com1.z0.glb.clouddn.com/nodebook23.png)

接下来，我们修改服务端的`index.js`代码，来响应这个请求。

```
console.log('HelloWorld');

//引入Express框架
import express from 'express';
//通过Express框架创建一个ExpressApp对象
const app = express();

//使用app的get函数，配置请求路由和响应函数
//响应函数携带req和res两个对象
//req是请求对象，包含请求所携带的全部信息
//res是响应对象，通过对res操作，可以将服务端数据返回客户端
app.get('/api/getPerson', (req, res)=>{
	//通过req获取用户提交的参数
	//req中的query字段包含了用户通过GET请求提交的全部参数
	const query = req.query;
	console.log(query);
	//构造一个模拟数据返回客户端
	const per = {
		name:'Tom',
		age:10,
		type:'A'
	}
	//使用res的json方法，将对象进行JSON编码后返回
	res.json(per);
});

//使用app的post函数来响应post请求
app.post('/api/postPerson',(req,res)=>{
	
	res.json({success:true});
})

//开启监听服务
const server = app.listen(3000,()=>{
	console.log('开启成功，访问http://localhost:3000');
});
```
运行`index.js`

```
babel-node index.js
```

通过postman发起一个请求。

![图片24](http://7u2rl9.com1.z0.glb.clouddn.com/nodebook24.png)

通过postman，可以看到，服务器返回了一个操作成功的数据对象。

在post请求中，客户端的请求中携带的参数不能向get请求中一样使用`query`进行获取，需要使用body-parser中间件处理后通过`body`字段获取。

安装body-parser

```
npm install body-parser --save
```
![图片25](http://7u2rl9.com1.z0.glb.clouddn.com/nodebook25.png)

安装完成之后，我们对代码进行修改

```
console.log('HelloWorld');

//引入Express框架
import express from 'express';
//通过Express框架创建一个ExpressApp对象
const app = express();

//引入body-parser
import bodyParser from 'body-parser';

//使用body-parser
app.use(bodyParser.json())

app.post('/api/postPerson',(req,res)=>{
	//使用body-parser之后，req中的body会包含用户提交的参数
	const body = req.body;
	//打印测试
	console.log(body);
	res.json({success:true});
})

//开启监听服务
const server = app.listen(3000,()=>{
	console.log('开启成功，访问http://localhost:3000');
});
```
运行`index.js`

```
babel-node index.js
```

使用postman发起相同的请求。

postman返回的信息和上次一致。

命令行打印出了body中的数据

![图片26](http://7u2rl9.com1.z0.glb.clouddn.com/nodebook26.png)

这样我们就可以获取POST请求时，用户提交的参数信息。

### 5，POST请求-带文件表单

POST不仅可以提交数据，还可以向服务器提交文件，例如上传图片的操作。

通过客户端提交的文件需要通过中间件multer处理，然后存放在req的`files`数组中

安装multer

```
npm install --save multer
```
![图片27](http://7u2rl9.com1.z0.glb.clouddn.com/nodebook27.png)

安装完成之后，我们修改代码，实现接受文件功能

```
console.log('HelloWorld');

//引入Express框架
import express from 'express';
//通过Express框架创建一个ExpressApp对象
const app = express();

//引入body-parser
import bodyParser from 'body-parser';
//使用body-parser
app.use(bodyParser.json());

//引入multer
import multer from 'multer';
//使用multer
app.use(multer().any());

app.post('/api/postPerson',(req,res)=>{

	const body = req.body;
	console.log(body);

	const files = req.files;
	console.log(files);
	
	res.json({success:true});
})

//开启监听服务
const server = app.listen(3000,()=>{
	console.log('开启成功，访问http://localhost:3000');
});

```
运行`index.js`

```
babel-node index.js
```

使用postman发起一个带图片的请求：

![图片28](http://7u2rl9.com1.z0.glb.clouddn.com/nodebook28.png)

点击send之后，post收到服务器的响应：

![图片29](http://7u2rl9.com1.z0.glb.clouddn.com/nodebook29.png)

命令行打印结果为：

![图片30](http://7u2rl9.com1.z0.glb.clouddn.com/nodebook30.png)

可以看到files数组中有一个对象，这个对象就是描述客户端提交的文件信息，如果客户端提交多个文件，这个数组中就会有多个对象。

```
{ 
	fieldname: 'image',
    originalname: '1.png',
    encoding: '7bit',
    mimetype: 'image/png',
    buffer: <Buffer 89 50 4e 47 0d 0a 1a 0a 00 00 00 0d 49 48 44 52 00 00 00 3d 00 00 00 3b 08 06 00 00 00 c8 3b 82 f4 00 00 0a a5 69 43 43 50 49 43 43 20 50 72 6f 66 69 ... >,
    size: 7819 }
```
这个是描述文件的对象，当服务器收到客户端上传的文件时，需要将文件保存在服务器。

这个对象中的`buffer`字段是文件的二进制数据，我们需要调用node的fs模块将数据写入到服务器的文件系统中。

将文件写入服务器之前，首先需要为文件创建一个全局唯一的文件名，因为如果文件名相同，后面写入的文件会覆盖之前的文件。

构建唯一文件名的方法很多，我们这里通过MD5对摘要算法做唯一处理，使用当前时间和一个随机数作为源数据进行MD5摘要，在小规模的文件系统中，次中方法可以认为是唯一的，但如果文件系统的数据过大，需要使用更高级的机制进行文件名命名，这里不再展开讲解。

node中`crypto`是专门处理数据的工具，其中包含了MD5摘要处理。此工具为node环境自带，无需再次安装。

```
//引入crypto工具
import crypto from 'crypto';
//创建HASH计算器，制定为md5算法
const hash = crypto.createHash('md5');
//获取当前时间
const date = new Date();
//获取随机数
const random = Math.random();
//设置源数据
hash.update(date+ random);
//获取摘要值,参数hex表示以16进制显示内容
const digest = hash.digest('hex');
```
有了唯一文件名，我们就可以使用`fs`工具，将客户端上传的文件写入服务端文件系统，此工具为node自带，无需安装。

```
console.log('HelloWorld');

//引入Express框架
import express from 'express';
//通过Express框架创建一个ExpressApp对象
const app = express();

//引入fs文件操作对象
import fs from 'fs';
//引入crypto工具
import crypto from 'crypto';

//引入body-parser
import bodyParser from 'body-parser';
//使用body-parser
app.use(bodyParser.json());

//引入multer
import multer from 'multer';
//使用multer
app.use(multer().any());

app.post('/api/postPerson',(req,res)=>{

	const body = req.body;
	console.log(body);

	const files = req.files;
	console.log(files);
	//从files数组中获取文件对象
	const imageFile = files[0];
	//从文件对象中获取文件二进制数据
	const buffer = imageFile.buffer;
	//通过随机数和当前时间生成文件名

	//创建HASH计算器，制定为md5算法
	const hash = crypto.createHash('md5');
	//获取当前时间
	const date = new Date();
	//获取随机数
	const random = Math.random();
	//设置源数据
	hash.update(date+ random);
	//获取摘要值,参数hex表示以16进制显示内容
	const filename = hash.digest('hex');
	//配置文件路径
	const path = `./source/${filename}.png`;

	fs.writeFile(path,buffer,(err)=>{
	    if (err) {
	        console.log(err);
	    } else {
	        console.log('文件写入成功');
	        res.json({success:true});
	    }
	});
	
})

//开启监听服务
const server = app.listen(3000,()=>{
	console.log('开启成功，访问http://localhost:3000');
});
```
运行`index.js`

```
babel-node index.js
```

在通过postman发起一个提交文件的请求，

请求成功之后，我们便可以在工程文件夹下的`source`路径下，看到保存的文件。

![图片31](http://7u2rl9.com1.z0.glb.clouddn.com/nodebook31.png)

