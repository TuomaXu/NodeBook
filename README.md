# API服务

API(Application Public Interface)是移动应用开发中的数据基础。所有移动应用中的数据都是通过API获取，用户输入的数据也通过API提交到服务器进行验证或保存。

在做移动应用开发时，首先需要做API的构建工作。通常使用制式的文档对API进行描述。

例如，登录API的描述

请求地址：`http://host:port/api/login`

请求方式：`POST`

请求参数：

|参数名 | 参数说明 |
| :--- | :--- |
| name | 登录名 |
| pwd | 密码 |

返回值：

```
{
	success:true,
	data:{
		uid:1001
		token:xxxx
	}
}
```

其中主要包括

* 请求地址
* 请求方式
* 请求参数
* 返回值

通过API接口，APP便可以和服务器进行通信。

所以我们本章先学习如何搭建API服务。


我们在构建API服务是使用以下技术

* [Nodejs](http://nodejs.cn)
* [Express](http://www.expressjs.com.cn)
* [Sequelize](https://itbilu.com/nodejs/npm/VkYIaRPz-.html)
