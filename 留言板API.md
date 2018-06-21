# 项目实战3-留言板API

留言板API对留言板App提供数据服务。依据留言板产品设计，此API提供如下数据数据服务：

* 用户注册
* 用户登录
* 发布留言
* 评论留言
* 获取所有留言
* 获取指定留言的所有评论

### 元数据

账户

```
{
	"id": 1,
	"username": "tom1",
	"password": "1234",
	"access_token": "e1fa63bed5f5eff45c9147ff27fcef28",
	"updatedAt": "2018-06-07T05:08:04.452Z",
	"createdAt": "2018-06-07T05:08:04.452Z"
}
```

消息

```
{
	"id": 10,
	"title": "xxx3",
	"content": "yyy3",
	"createdAt": "2018-06-07T05:25:47.000Z",
	"updatedAt": "2018-06-07T05:25:47.000Z",
	"messageUserId": 1,
	"images": [],
	"message_user": {
		"id": 1,
		"username": "tom1"
	}
}
```

评论

```
{
	"id": 2,
	"content": "xxx",
	"createdAt": "2018-06-07T05:32:59.000Z",
	"updatedAt": "2018-06-07T05:32:59.000Z",
	"messageId": 10,
	"messageUserId": 1,
	"message": {
		"id": 10,
		"title": "xxx3"
	},
	"message_user": {
		"id": 1,
		"username": "tom1"
	}
}
```


### 错误代码

|错误代码|内容|
|:---|:---|
|10001|系统错误|
|10002|用户名错误|
|10003|密码错误|
|10004|access_token无效|
|10005|TodoID无效|
|10006|参数无效|
|10007|用户名已存在|
|10008|MessageID无效|

错误返回值：

```
{
    success:false,
    errorCode:10001,
    errorMessage:'系统错误'
}
```

### 接口描述

注册

请求链接：`/api/register`

请求方式：`POST`

|参数名称|参数描述|
|:---|:---|
|username|用户名|
|password|密码|

```
{
    "success": true,
    "data": {
        "id": 2,
        "username": "tom1",
        "password": "1234",
        "access_token": "16e088f3d62c4c132e8728ef79eaf353",
        "updatedAt": "2018-06-06T08:05:13.664Z",
        "createdAt": "2018-06-06T08:05:13.664Z"
    }
}
```

登录

请求链接：`/api/login`

请求方式：`POST`

|参数名称|参数描述|
|:---|:---|
|username|账户名|
|password|密码|

```
{
    "success": true,
    "data": {
        "id": 2,
        "username": "tom1",
        "password": "1234",
        "access_token": "8e58260409dc169009b87800dfe5128f",
        "createdAt": "2018-06-06T08:05:13.000Z",
        "updatedAt": "2018-06-06T08:11:18.442Z"
    }
}

```

发留言

请求链接：`/api/postMessage`

请求方式：`POST`

|参数名称|参数描述|
|:---|:---|
|access_token|登录令牌|
|title|消息标题|
|content|消息内容|
|image1-9|图片Base64链接|

返回值：

```
{
    "success": true,
    "data": {
        "id": 10,
        "title": "xxx3",
        "content": "yyy3",
        "createdAt": "2018-06-07T05:25:47.000Z",
        "updatedAt": "2018-06-07T05:25:47.000Z",
        "messageUserId": 1,
        "images": [],
        "message_user": {
            "id": 1,
            "username": "tom1"
        }
    }
}
```

发布对指定消息的评论

请求路径：`/api/postComment`

请求方式：`POST`

|参数名称|参数描述|
|:---|:---|
|access_token|登录令牌|
|messageID|消息ID|
|content|评论内容|

返回值：

```
{
    "success": true,
    "data": {
        "id": 2,
        "content": "xxx",
        "createdAt": "2018-06-07T05:32:59.000Z",
        "updatedAt": "2018-06-07T05:32:59.000Z",
        "messageId": 10,
        "messageUserId": 1,
        "message": {
            "id": 10,
            "title": "xxx3"
        },
        "message_user": {
            "id": 1,
            "username": "tom1"
        }
    }
}
```


获取全部消息

请求地址：`/api/allMessages`

请求方式：`POST`

|参数名称|参数描述|
|:---|:---|
|access_token|登录令牌|

返回值：

```
{
    "success": true,
    "data": [
        {
            "id": 3,
            "title": "xxx3",
            "content": "yyy3",
            "createdAt": "2018-06-07T05:08:55.000Z",
            "updatedAt": "2018-06-07T05:08:55.000Z",
            "messageUserId": 1,
            "images": [],
            "message_user": {
                "id": 1,
                "username": "tom1"
            }
        },
        {
            "id": 6,
            "title": "xxx3",
            "content": "yyy3",
            "createdAt": "2018-06-07T05:22:16.000Z",
            "updatedAt": "2018-06-07T05:22:16.000Z",
            "messageUserId": 1,
            "images": [],
            "message_user": {
                "id": 1,
                "username": "tom1"
            }
        },
        {
            "id": 7,
            "title": "xxx3",
            "content": "yyy3",
            "createdAt": "2018-06-07T05:22:46.000Z",
            "updatedAt": "2018-06-07T05:22:46.000Z",
            "messageUserId": 1,
            "images": [],
            "message_user": {
                "id": 1,
                "username": "tom1"
            }
        },
        {
            "id": 8,
            "title": "xxx3",
            "content": "yyy3",
            "createdAt": "2018-06-07T05:23:05.000Z",
            "updatedAt": "2018-06-07T05:23:05.000Z",
            "messageUserId": 1,
            "images": [],
            "message_user": {
                "id": 1,
                "username": "tom1"
            }
        },
        {
            "id": 9,
            "title": "xxx3",
            "content": "yyy3",
            "createdAt": "2018-06-07T05:23:43.000Z",
            "updatedAt": "2018-06-07T05:23:43.000Z",
            "messageUserId": 1,
            "images": [],
            "message_user": {
                "id": 1,
                "username": "tom1"
            }
        },
        {
            "id": 10,
            "title": "xxx3",
            "content": "yyy3",
            "createdAt": "2018-06-07T05:25:47.000Z",
            "updatedAt": "2018-06-07T05:25:47.000Z",
            "messageUserId": 1,
            "images": [],
            "message_user": {
                "id": 1,
                "username": "tom1"
            }
        },
        {
            "id": 4,
            "title": "xxx3",
            "content": "yyy3",
            "createdAt": "2018-06-07T05:09:57.000Z",
            "updatedAt": "2018-06-07T05:09:57.000Z",
            "messageUserId": null,
            "images": [],
            "message_user": null
        },
        {
            "id": 5,
            "title": "xxx3",
            "content": "yyy3",
            "createdAt": "2018-06-07T05:11:13.000Z",
            "updatedAt": "2018-06-07T05:11:13.000Z",
            "messageUserId": null,
            "images": [],
            "message_user": null
        }
    ]
}
```

获取指定消息的评论内容

请求地址：`/api/allComments`

请求方式：`POST`

|参数名称|参数描述|
|:---|:---|
|access_token|登录令牌|
|messageID|消息ID|

返回值：

```
{
    "success": true,
    "data": [
        {
            "id": 1,
            "content": "xxx",
            "createdAt": "2018-06-07T05:32:14.000Z",
            "updatedAt": "2018-06-07T05:32:15.000Z",
            "messageId": 10,
            "messageUserId": 1,
            "message_user": {
                "id": 1,
                "username": "tom1"
            }
        },
        {
            "id": 2,
            "content": "xxx",
            "createdAt": "2018-06-07T05:32:59.000Z",
            "updatedAt": "2018-06-07T05:32:59.000Z",
            "messageId": 10,
            "messageUserId": 1,
            "message_user": {
                "id": 1,
                "username": "tom1"
            }
        }
    ]
}
```


### Sequelize配置

1，定义User数据模型

```
export default (sequelize, DataTypes)=>{
    return sequelize.define('message_user',{
        username:DataTypes.STRING,
        password:DataTypes.STRING,
        access_token:DataTypes.STRING,
    });
}
```

2，定义Message数据模型

```
export default (sequelize, DataTypes)=>{
    return sequelize.define('message',{
        title:DataTypes.STRING,
        content:DataTypes.STRING,
    });
}
```

3，定义Image数据模型

```
export default (sequelize, DataTypes)=>{
    return sequelize.define('image',{
        url:DataTypes.STRING,
    });
}
```

4，定义Comment数据模型

```
export default (sequelize, DataTypes)=>{
    return sequelize.define('comment',{
        content:DataTypes.STRING,
    });
}
```

5，定义数据关系

```
const Comment = sequelize.import('./Comment.js');
const Message = sequelize.import('./Message.js');
const Image = sequelize.import('./Image.js');
const User = sequelize.import('./User.js');


User.hasMany(Message);
Message.belongsTo(User);

Message.hasMany(Image);

Message.hasMany(Comment);
Comment.belongsTo(Message);

User.hasMany(Comment);
Comment.belongsTo(User);
```


### DataServer实现

1，注册接口，实现文件`register.js`

```
import sequelize from '../data-model/data-base';
import getToken from '../utils/getToken';

const User = sequelize.model('message_user');

export default async (req,res)=>{
    try {
        const { username , password } = req.body;

        if(!username || !password){
            res.json({
                success:false,
                errorCode:10006,
                errorMessage:'参数无效'
            })

            return;
        }

        const users = await User.findAll({where:{username}});

        if (users.length != 0) {
            res.json({
                success:false,
                errorCode:10007,
                errorMessage:'用户名已存在'
            })

            return;
        }

        const access_token = getToken(username);

        const user = await User.create({
            username,
            password,
            access_token
        });

        res.json({
            success:true,
            data:user
        })


    } catch (error) {
        res.json({
            success:false,
            errorCode:10001,
            errorMessage:'系统错误'
        })
    }
}
```

2，登录接口，实现文件`login.js`

```
import sequelize from '../data-model/data-base';
import getToken from '../utils/getToken';

const User = sequelize.model('message_user');

export default async (req,res)=>{
    try {
        const { username , password } = req.body;

        if(!username || !password){
            res.json({
                success:false,
                errorCode:10006,
                errorMessage:'参数无效'
            })

            return;
        }

        const users = await User.findAll({where:{username}});

        if (users.length == 0) {
            res.json({
                success:false,
                errorCode:10002,
                errorMessage:'用户名错误'
            })

            return;
        }

        const user = users[0];

        if(user.password != password){
            res.json({
                success:false,
                errorCode:10003,
                errorMessage:'密码错误'
            })

            return;
        }

        const access_token = getToken(username);

        user.access_token = access_token;

        await user.save();

        res.json({
            success:true,
            data:user
        })



        


    } catch (error) {
        res.json({
            success:false,
            errorCode:10001,
            errorMessage:'系统错误'
        })
    }
}
```

3，发布留言接口，实现文件`postMessage.js`

```
import saveImage from '../utils/save-image';
import sequelize from '../data-model/data-base';

const Message = sequelize.model('message');
const User = sequelize.model('message_user');
const Image = sequelize.model('image');

export default async (req,res)=>{

    try {
        console.log(req.body);
        console.log(req.files);

        const {access_token,title,content} = req.body;

        if(!access_token || !title || !content){

            res.json({
                success:false,
                errorCode:10006,
                errorMessage:'参数无效'
            })

            return;
        }

        const user = await User.findOne({where:{access_token}});

        if (!user) {
            res.json({
                success:false,
                errorCode:10004,
                errorMessage:'access_token无效'
            })
            return;
        }

        const message = await Message.create({
            title,
            content
        });

        await user.addMessage(message);

        if (req.files) {
            const imageSavePromises = req.files.map((file)=>{
                return saveImage(file.buffer);
            });
            const imageURLs = await Promise.all(imageSavePromises);
            const imagePromises = imageURLs.map((url)=>{
                return Image.create({url});
            });
            const images = await Promise.all(imagePromises);
            await message.setImages(images);
    
        }

        // const body_length = Object.keys(req.body).length;
        
        // if(body_length>3){
        //     const imageSavePromises = [];
        //     for(let i = 0;i<body_length-3;i++){
        //         const dataString = req.body[`image${i}`];
        //         const array = dataString.split(',');
        //         console.log(array[0]);
        //         const buffer =  new Buffer(array[1], 'base64');
        //         imageSavePromises.push(saveImage(buffer))
        //     }

        //     const imageURLs = await Promise.all(imageSavePromises);

        //     const imageCreatePromises = imageURLs.map((url)=>{
        //         console.log(url);
        //         return Image.create({url});
        //     });

        //     const images = await Promise.all(imageCreatePromises);

        //     await message.setImages(images);
        // }
        
        const m = await Message.findOne({
            where:{id:message.id},
            include:[
                {
                    model:Image,
                    attributes:['id','url']
                },
                {
                    model:User,
                    attributes:['id','username']
                }
            ],
        });

        res.json({
            success:true,
            data:m
        })








    } catch (error) {
        console.log(error)
        res.json({
            success:false,
            errorCode:10001,
            errorMessage:'系统错误'
        })
    }
}
```

4，发布评论接口，实现文件`postComment.js`

```
import saveImage from '../utils/save-image';
import sequelize from '../data-model/data-base';

const Message = sequelize.model('message');
const User = sequelize.model('message_user');
const Comment = sequelize.model('comment');

export default async (req,res)=>{

    try {
        console.log(req.body);

        const {access_token,messageID,content} = req.body;

        if(!access_token || !messageID || !content){

            res.json({
                success:false,
                errorCode:10006,
                errorMessage:'参数无效'
            })

            return;
        }

        const user = await User.findOne({where:{access_token}});

        if (!user) {
            res.json({
                success:false,
                errorCode:10004,
                errorMessage:'access_token无效'
            })
            return;
        }

        const message = await Message.findOne({where:{id:messageID}});

        if (!message) {
            res.json({
                success:false,
                errorCode:10008,
                errorMessage:'MessageID无效'
            })
            return;
        }



        const comment = await Comment.create({
            content
        });

        await user.addComment(comment);
        await message.addComment(comment);
 
        
        const m = await Comment.findOne({
            where:{id:comment.id},
            include:[
                {
                    model:Message,
                    attributes:['id','title']
                },
                {
                    model:User,
                    attributes:['id','username']
                }
            ],
        });

        res.json({
            success:true,
            data:m
        })








    } catch (error) {
        console.log(error)
        res.json({
            success:false,
            errorCode:10001,
            errorMessage:'系统错误'
        })
    }
}
```

5，获取所有留言接口，实现文件`allMessages.js`

```
import saveImage from '../utils/save-image';
import sequelize from '../data-model/data-base';

const Message = sequelize.model('message');
const User = sequelize.model('message_user');
const Image = sequelize.model('image');
const Comment = sequelize.model('comment');

export default async (req,res)=>{

    try {
        console.log(req.body);

        const {access_token} = req.body;

        if(!access_token){

            res.json({
                success:false,
                errorCode:10006,
                errorMessage:'参数无效'
            })

            return;
        }

        const user = await User.findOne({where:{access_token}});

        if (!user) {
            res.json({
                success:false,
                errorCode:10004,
                errorMessage:'access_token无效'
            })
            return;
        }
        
        const m = await Message.findAll({
            order:[
                ['id','DESC']
            ],
            include:[
                {
                    model:Image,
                    attributes:['id','url']
                },
                {
                    model:User,
                    attributes:['id','username']
                },
            ],
        });

        res.json({
            success:true,
            data:m
        })








    } catch (error) {
        console.log(error)
        res.json({
            success:false,
            errorCode:10001,
            errorMessage:'系统错误'
        })
    }
}
```

6，获取指定留言的所有评论接口，实现文件`allComments.js`

```
import saveImage from '../utils/save-image';
import sequelize from '../data-model/data-base';

const Message = sequelize.model('message');
const User = sequelize.model('message_user');
const Image = sequelize.model('image');
const Comment = sequelize.model('comment');

export default async (req,res)=>{

    try {
        console.log(req.body);

        const {access_token,messageID} = req.body;

        if(!access_token || !messageID){

            res.json({
                success:false,
                errorCode:10006,
                errorMessage:'参数无效'
            })

            return;
        }

        const user = await User.findOne({where:{access_token}});

        if (!user) {
            res.json({
                success:false,
                errorCode:10004,
                errorMessage:'access_token无效'
            })
            return;
        }


        const message = await Message.findOne({where:{id:messageID}});
        
        if (!message) {
            res.json({
                success:false,
                errorCode:10008,
                errorMessage:'MessageID无效'
            })
            return;
        }

        const comments = await message.getComments({
            include:[
                {
                    model:User,
                    attributes:['id','username']
                },
            ]
        })


        res.json({
            success:true,
            data:comments
        })








    } catch (error) {
        console.log(error)
        res.json({
            success:false,
            errorCode:10001,
            errorMessage:'系统错误'
        })
    }
}
```

