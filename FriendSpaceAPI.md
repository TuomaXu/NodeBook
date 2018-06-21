# 项目实战4-FriendSpaceAPI

FriendSpaceAPI为FriendSpaceApp提供数据服务，依照产品设计，此API提供如下数据服务：

* 账户操作类
    * 注册账号
    * 登录账号
    * 修改密码
* 用户信息类
    * 初始化用户信息
    * 修改用户信息
    * 获取用户信息
* 消息操作类
    * 发布消息
    * 获取指定用户发布的消息
    * 获取Home页发布的消息
    * 删除消息
* 好友操作类
    * 通过昵称查询好友
    * 关注好友
    * 取消关注好友
    * 获取已关注好友列表


### 元数据

账户

```
{
	id:0
	email:'',
	password:'',
	access_token:'',
}
```

用户信息

```
{
	"id": 3,
	"nickname": "tom2",
	"image": "55aff85258799e536c099689b69d81e9.png",
	"sign": "wwww",
}
```

消息

```
{
	"id": 10,
	"content": "ceshi333",
	"createdAt": "2017-11-23T00:19:26.000Z",
	"images": [
	    {
	        "id": 2,
	        "url": "c2436882682da92fbcdbf56025cb4db8.png"
	    }
	],
	"user": {
	    "id": 2,
	    "nickname": "tom3",
	    "image": "b80f9ccf334cda880cde0a25c6b68ead.png"
	}
}
```

### 错误代码

|错误代码|内容|
|:---|:---|
|10001|用户名已存在|
|10002|用户名或密码错误|
|10003|access_token无效|
|10004|未初始化用户信息|
|10005|UserId无效|
|10006|旧密码错误|

### 请求基础地址

请求地址:`http://localhost:5000`


### API接口

**注册**

请求链接：`/api/register`

请求方式：`POST`

|参数名称|参数描述|
|:---|:---|
|email|用户名，邮箱|
|password|密码|

```
{
    "success": true,
    "data": {
        "access_token": "63b743fedb6dd5859f70b9f17d29f1a0"
    }
}
```

**登录**

请求链接：`/api/login`

请求方式：`POST`

|参数名称|参数描述|
|:---|:---|
|email|账户名，邮箱，此名用于登录|
|password|密码|

```
{
    "success": true,
    "data": {
        "access_token": "c512cff01724cbc2ef186a73a072d916",
        "userId": null,//用户信息，null，表示无用户信息
    }
}

```

**修改密码**

请求链接：`/api/changePassword`

请求方式：`POST`

|参数名称|参数描述|
|:---|:---|
|access_token|通信令牌|
|old_password|当前密码|
| new_password |新密码|

```
{
    "success": true
}
```

**初始化用户信息**

请求链接：`/api/createUser`

请求方式：`POST`

|参数名称|参数描述|
|:---|:---|
|nickname|用户名，此名用于显示在页面上|
|sign|个性签名|
|image|用户头像，png文件|

```
{
    "success": true,
    "data": {
        "id": 3,
        "nickname": "tom2",
        "sign": "wwww",
        "image": "55aff85258799e536c099689b69d81e9.png",
        "updatedAt": "2017-11-22T08:03:41.010Z",
        "createdAt": "2017-11-22T08:03:41.010Z"
    }
}
```

**获取用户信息**

请求链接：`api/getUser`

请求方式：`POST `

|参数名称|参数描述|
|:---|:---|
|access_token|通信令牌|
|userId|用户ID，可选参数，如不填或为0则获取登录用户信息|

```
{
    "id": 3,
    "nickname": "tom2",
    "image": "55aff85258799e536c099689b69d81e9.png",
    "sign": "wwww",
    "createdAt": "2017-11-22T08:03:41.000Z",
    "updatedAt": "2017-11-22T08:03:41.000Z"
}
```



**更新用户信息**

请求链接：`/api/updateUser`

请求方式：`POST`

|参数名称|参数描述|
|:---|:---|
|access_token|通信令牌|
|nickname|用户昵称|
|sign|用户签名|
|image|用户头像，png文件|

```
{
    "success": true,
    "data": {
        "id": 2,
        "nickname": "tom3",
        "image": "b80f9ccf334cda880cde0a25c6b68ead.png",
        "sign": "qqq",
        "createdAt": "2017-11-22T08:02:18.000Z",
        "updatedAt": "2017-11-23T01:08:53.715Z"
    }
}
```

**发Message**

请求链接：`/api/postMessage`

请求方式：`POST`

|参数名称|参数描述|
|:---|:---|
|access_token|通信令牌|
|content|消息内容|
|imageN|消息图片，N值为1-9，文件类型|

```
{
    "id": 12,
    "content": "ceshi444",
    "createdAt": "2017-11-23T00:22:24.000Z",
    "updatedAt": "2017-11-23T00:22:24.000Z",
    "userId": 2,
    "images": [
        {
            "id": 4,
            "url": "3eaa652014fdffd63f781c62deba1410.png"
        },
        {
            "id": 5,
            "url": "d911da87cbb0eac478b2747e512b560a.png"
        }
    ]
}
```

**删除Message**

请求链接：`/api/deleteMessage`

请求方式：`POST`

|参数名称|参数描述|
|:---|:---|
|access_token|通信令牌|
|messageId|消息ID|

```
{
    "success": true
}
```

**通过昵称查找用户**

请求链接：`/api/findUser`

请求方式：`POST`

|参数名称|参数描述|
|:---|:---|
|access_token|通信令牌|
|nickname|用户昵称，精确查找非模糊|


```
{
    "success": true,
    "data": [
        {
            "id": 2,
            "nickname": "tom3",
            "image": "b80f9ccf334cda880cde0a25c6b68ead.png",
            "sign": "qqq",
            "createdAt": "2017-11-22T08:02:18.000Z",
            "updatedAt": "2017-11-23T01:08:53.000Z"
        }
    ]
}
```

**关注好友**

请求链接：`/api/follow`

请求方式：`POST`

|参数名称|参数描述|
|:---|:---|
|access_token|通信令牌|
|userId|好友ID|

```
{
    "success": true
}
```


**取消关注好友**

请求链接：`/api/follow`

请求方式：`POST`

|参数名称|参数描述|
|:---|:---|
|access_token|通信令牌|
|userId|好友ID|

```
{
    "success": true
}
```

**获取好友列表**

请求链接：`/api/getFollow`

请求方式：`POST `

|参数名称|参数描述|
|:---|:---|
|access_token|通信令牌|

```
{
    "success": true,
    "data": [
        {
            "id": 3,
            "nickname": "tom2",
            "image": "55aff85258799e536c099689b69d81e9.png",
            "sign": "wwww",
            "createdAt": "2017-11-22T08:03:41.000Z",
            "updatedAt": "2017-11-22T08:03:41.000Z",
            "friendship": {
                "info": null,
                "createdAt": "2017-11-23T02:30:31.000Z",
                "updatedAt": "2017-11-23T02:30:31.000Z",
                "userId": 2,
                "followedId": 3
            }
        },
        {
            "id": 6,
            "nickname": "tom5",
            "image": "574a4d1004770d2743d72d35dd094f78.png",
            "sign": "wwww",
            "createdAt": "2017-11-23T02:29:46.000Z",
            "updatedAt": "2017-11-23T02:29:46.000Z",
            "friendship": {
                "info": null,
                "createdAt": "2017-11-23T06:20:19.000Z",
                "updatedAt": "2017-11-23T06:20:19.000Z",
                "userId": 2,
                "followedId": 6
            }
        }
    ]
}
```


**请求用户Message**

请求链接：`/api/getMessage`

请求方式：`POST `

|参数名称|参数描述|
|:---|:---|
|access_token|通信令牌|
|userId|可选参数，如有返回该userId下的Message|
|minId|可选参数，如有返回小于此messageId的Message|
|count|可选参数，默认返回20条|

```
{
    "success": true,
    "data": [
        {
            "id": 5,
            "content": "ceshi222",
            "createdAt": "2017-11-22T23:57:25.000Z",
            "updatedAt": "2017-11-22T23:57:25.000Z",
            "userId": 2,
            "images": [],
            "user": {
                "id": 2,
                "nickname": "tom3",
                "image": "b80f9ccf334cda880cde0a25c6b68ead.png"
            }
        }
    ]
}
```

**请求HomeMessage**

请求链接：`/api/homeMessage`

请求方式：`POST `

|参数名称|参数描述|
|:---|:---|
|access_token|通信令牌|
|minId|可选参数，如有返回小于此messageId的Message|
|count|可选参数，默认返回20条|

```
{
    "success": true,
    "data": [
        {
            "id": 14,
            "content": "ceshi666",
            "createdAt": "2017-11-23T01:46:29.000Z",
            "updatedAt": "2017-11-23T01:46:29.000Z",
            "userId": 3,
            "images": [],
            "user": {
                "id": 3,
                "nickname": "tom2",
                "image": "55aff85258799e536c099689b69d81e9.png"
            }
        },
        {
            "id": 13,
            "content": "ceshi555",
            "createdAt": "2017-11-23T01:46:13.000Z",
            "updatedAt": "2017-11-23T01:46:13.000Z",
            "userId": 3,
            "images": [],
            "user": {
                "id": 3,
                "nickname": "tom2",
                "image": "55aff85258799e536c099689b69d81e9.png"
            }
        },
        {
            "id": 11,
            "content": "ceshi444",
            "createdAt": "2017-11-23T00:21:36.000Z",
            "updatedAt": "2017-11-23T00:21:36.000Z",
            "userId": 2,
            "images": [
                {
                    "id": 3,
                    "url": "67097108fb46fead358e188b61410f24.png"
                }
            ],
            "user": {
                "id": 2,
                "nickname": "tom3",
                "image": "b80f9ccf334cda880cde0a25c6b68ead.png"
            }
        },
        {
            "id": 10,
            "content": "ceshi333",
            "createdAt": "2017-11-23T00:19:26.000Z",
            "updatedAt": "2017-11-23T00:19:26.000Z",
            "userId": 2,
            "images": [
                {
                    "id": 2,
                    "url": "c2436882682da92fbcdbf56025cb4db8.png"
                }
            ],
            "user": {
                "id": 2,
                "nickname": "tom3",
                "image": "b80f9ccf334cda880cde0a25c6b68ead.png"
            }
        },
        {
            "id": 9,
            "content": "ceshi222",
            "createdAt": "2017-11-23T00:16:56.000Z",
            "updatedAt": "2017-11-23T00:16:56.000Z",
            "userId": 2,
            "images": [
                {
                    "id": 1,
                    "url": "b09175e064d2d0f8244787b8d10bdccd.png"
                }
            ],
            "user": {
                "id": 2,
                "nickname": "tom3",
                "image": "b80f9ccf334cda880cde0a25c6b68ead.png"
            }
        },
        {
            "id": 8,
            "content": "ceshi222",
            "createdAt": "2017-11-23T00:07:10.000Z",
            "updatedAt": "2017-11-23T00:07:10.000Z",
            "userId": 2,
            "images": [],
            "user": {
                "id": 2,
                "nickname": "tom3",
                "image": "b80f9ccf334cda880cde0a25c6b68ead.png"
            }
        },
        {
            "id": 7,
            "content": "ceshi222",
            "createdAt": "2017-11-23T00:06:00.000Z",
            "updatedAt": "2017-11-23T00:06:00.000Z",
            "userId": 2,
            "images": [],
            "user": {
                "id": 2,
                "nickname": "tom3",
                "image": "b80f9ccf334cda880cde0a25c6b68ead.png"
            }
        },
        {
            "id": 6,
            "content": "ceshi222",
            "createdAt": "2017-11-22T23:58:07.000Z",
            "updatedAt": "2017-11-22T23:58:07.000Z",
            "userId": 2,
            "images": [],
            "user": {
                "id": 2,
                "nickname": "tom3",
                "image": "b80f9ccf334cda880cde0a25c6b68ead.png"
            }
        },
        {
            "id": 5,
            "content": "ceshi222",
            "createdAt": "2017-11-22T23:57:25.000Z",
            "updatedAt": "2017-11-22T23:57:25.000Z",
            "userId": 2,
            "images": [],
            "user": {
                "id": 2,
                "nickname": "tom3",
                "image": "b80f9ccf334cda880cde0a25c6b68ead.png"
            }
        }
    ]
}
```


### Sequelize配置

1，定义数据模型

Account模型

```
export default (sequelize, DataTypes)=>{
    return sequelize.define('account',{
        email:DataTypes.STRING,
        password:DataTypes.STRING,
        access_token:DataTypes.STRING,
    });
}
```

User模型

```
export default (sequelize, DataTypes)=>{
    return sequelize.define('user',{
        nickname:DataTypes.STRING,
        image:DataTypes.STRING,
        sign:DataTypes.STRING,
    });
}
```

Message模型

```
export default (sequelize, DataTypes)=>{
    return sequelize.define('message',{
        content:DataTypes.STRING,
    });
}
```

Image模型

```
export default (sequelize, DataTypes)=>{
    return sequelize.define('image',{
        url:DataTypes.STRING,
    });
}
```

FriendShip模型

```
export default (sequelize, DataTypes)=>{
    return sequelize.define('friendship',{
        info:DataTypes.STRING,
    });
}
```

2，配置数据模型关系

```
//导入数据模型

const Message = sequelize.import('./Message.js');
const Account = sequelize.import('./Account.js');
const Image = sequelize.import('./Image.js');
const User = sequelize.import('./User.js');
const FriendShip = sequelize.import('./FriendShip.js');



//配置数据关系
Account.belongsTo(User);

Message.hasMany(Image);
Message.belongsTo(User);

User.belongsToMany(User,{
  as:'followed',
  through:FriendShip,
});
```

### DataServer实现

1，注册接口，实现文件`register.js`

```
import sequelize from '../data-model/data-base';
import createToken from '../utils/create-token';

const Account = sequelize.model('account');

export default async (req,res)=>{
    
    const {email,password} = req.body;
    const temp = await Account.findOne({where:{email}});
    if(temp){
        return res.json({
            success:false,
            errorMessage:'用户名已存在',
            errorCode:10001,
        })
    }

    const access_token = createToken(email,password);

    const account = await Account.create({email,password,access_token});

    return res.json({
        success:true,
        data:{
            access_token,
        }
    });
}
```


2，登录接口，实现文件`login.js`

```
import sequelize from '../data-model/data-base';
import createToken from '../utils/create-token';

const Account = sequelize.model('account');
const User = sequelize.model('user');

export default async (req,res)=>{

    const {email,password} = req.body;

    const account = await Account.findOne({
        where:{email,password},
    });

    if(!account){
        return res.json({
            success:false,
            errorMessage:'用户名或密码错误',
            errorCode:10002
        });
    }

    const access_token = createToken(email,password);

    account.access_token = access_token;

    await account.save();

    return res.json({
        success:true,
        data:{
            access_token,
            userId:account.userId
        }
    });
}
```


3，初始化用户信息接口，实现文件`createUser.js`

```
import sequelize from '../data-model/data-base';
import saveImage from '../utils/save-image';

const Account = sequelize.model('account');
const User = sequelize.model('user');

export default async (req,res)=>{

    const {access_token,nickname,sign} = req.body;
    const account = await Account.findOne({where:{access_token}});
    
    if(!account){
        return res.json({
            success:false,
            errorMessage:'access_token无效',
            errorCode:10003,
        })
    }

    const image = await saveImage(req.files[0].buffer);

    const user  = await User.create({nickname,sign,image});

    await account.setUser(user);

    res.json({
        success:true,
        data:user,
    });
}
```


4，修改密码接口，实现文件`changePassword.js`

```
import sequelize from '../data-model/data-base';

const Account = sequelize.model('account');

export default async (req,res)=>{
    
    const {access_token,old_password,new_password} = req.body;

    const account = await Account.findOne({
        where:{access_token},
    });

    if(!account){
        return res.json({
            success:false,
            errorMessage:'access_token无效',
            errorCode:10003
        });
    }

    if(account.password != old_password){
        return res.json({
            success:false,
            errorMessage:'旧密码错误',
            errorCode:10006
        });
    }

    account.password = new_password;
    await account.save();

    return res.json({
        success:true,
    });
    
}
```


5，发Message接口，实现文件`postAppMessage.js`

```
import saveImage from '../utils/save-image';
import sequelize from '../data-model/data-base';

const Message = sequelize.model('message');
const Account = sequelize.model('account');
const User = sequelize.model('user');
const Image = sequelize.model('image');

export default async (req,res)=>{

    const {access_token,content} = req.body;

    console.log(req.body);

    const account = await Account.findOne({where:{access_token}});
    if(!account){
        return res.json({
            success:false,
            errorMessage:'access_token无效',
            errorCode:10003,
        })
    }

    const user = await account.getUser();

    if(!user){
        return res.json({
            success:false,
            errorMessage:'未初始化用户信息',
            errorCode:10004,
        })
    }   

    const message =  await Message.create({content});


    await message.setUser(user);
    
    if(req.files){

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

    const m = await Message.findOne({
        where:{id:message.id},
        include:[
            {
                model:Image,
                attributes:['id','url']
            },
            {
                model:User,
            }
        ],
    });


    return res.json({
        success:true,
        data:m
    });

    
}
```


6，删除Message接口，实现文件`deleteMessage.js`

```
import sequelize from '../data-model/data-base';

const Message = sequelize.model('message');
const Account = sequelize.model('account');

export default async (req,res)=>{
    
        const {access_token,messageId} = req.body;

        const account = await Account.findOne({where:{access_token}});

        if(!account){
            return res.json({
                success:false,
                errorMessage:'access_token无效',
                errorCode:10003,
            })
        }

        await Message.destroy({where:{id:messageId}});

        return res.json({
            success:true,
        })
}
```


7，修改用户信息接口，实现文件`updateUser.js`

```
import sequelize from '../data-model/data-base';
import saveImage from '../utils/save-image';

const Account = sequelize.model('account');
const User = sequelize.model('user');

export default async (req,res)=>{

    const {access_token,nickname,sign} = req.body;
    const account = await Account.findOne({where:{access_token}});
    if(!account){
        return res.json({
            success:false,
            errorMessage:'access_token无效',
            errorCode:10003,
        })
    }

    const user  = await account.getUser();

    if(req.files && req.files.length){
        const image = await saveImage(req.files[0].buffer);
        user.image = image;
    }
    if(nickname){
        user.nickname = nickname;
    }
    if(sign){
        user.sign = sign;
    }

    await user.save();

    res.json({
        success:true,
        data:user,
    });
}
```


8，获取指定用户Message接口，实现文件`getMessage.js`

```
import Sequelize  from 'sequelize';
import sequelize from '../data-model/data-base';

const Message = sequelize.model('message');
const Account = sequelize.model('account');
const User = sequelize.model('user');
const Image = sequelize.model('image');

const Op = Sequelize.Op;

export default async (req,res)=>{
    
    const {access_token,minId,userId,count=20} = req.body;
    const account = await Account.findOne({where:{access_token}});
    if(!account){
        return res.json({
            success:false,
            errorMessage:'access_token无效',
            errorCode:10003,
        })
    }



    const options = {
        order:[
            ['id','DESC']
        ],
        where:{
            userId:userId?userId:account.userId,
        },
        include:[
            {
                model:Image,
                attributes:['id','url']
            },
            {
                model:User,
                attributes:['id','nickname','image']
            }
        ],
        limit:parseInt(count),

    };

    if(minId){
        const opMinId = {
            [Op.lt]: minId,
        }
        options.where.id = opMinId;
    }

    const messages = await Message.findAll(options);

    return res.json({
        success:true,
        data:messages
    })


}
```


9，获取Home接口，实现文件`homeMessage.js`

```
import Sequelize  from 'sequelize';
import sequelize from '../data-model/data-base';

const Message = sequelize.model('message');
const Account = sequelize.model('account');
const User = sequelize.model('user');
const Image = sequelize.model('image');

const Op = Sequelize.Op;

export default async (req,res)=>{
    
    const {access_token,minId,count=20} = req.body;
    const account = await Account.findOne({where:{access_token}});
    if(!account){
        return res.json({
            success:false,
            errorMessage:'access_token无效',
            errorCode:10003,
        })
    }

    const user = await account.getUser();

    const follows = await user.getFollowed();

    const followIds = follows.map((follow)=>{
        return follow.id;
    });

    followIds.push(user.id);


    const options = {
        order:[
            ['id','DESC']
        ],
        where:{
            userId:{
                [Op.in]:followIds
            }
        },
        include:[
            {
                model:Image,
                attributes:['id','url']
            },
            {
                model:User,
                attributes:['id','nickname','image']
            }
        ],
        limit:parseInt(count),
    };

    if(minId){
        const opMinId = {
            [Op.lt]: minId,
        }
        options.where.id = opMinId;
    }

    const messages = await Message.findAll(options);

    console.log(messages);

    return res.json({
        success:true,
        data:messages
    });


}



```


10，通过昵称查找用户接口，实现文件`findUser.js`

```
import sequelize from '../data-model/data-base';

const Account = sequelize.model('account');
const User = sequelize.model('user');

export default async (req,res)=>{
    
    const {access_token,userId} = req.body;
    const account = await Account.findOne({where:{access_token}});
    if(!account){
        return res.json({
            success:false,
            errorMessage:'access_token无效',
            errorCode:10003,
        })
    }

    if(userId && userId!=0){
        const user = await User.findOne({where:{id:userId}});
        return res.json({
            success:true,
            data:user
        });
    } else {
        const user = await account.getUser();
        return res.json({
            success:true,
            data:user
        });
    }

    
}
```


11，关注用户接口，实现文件`follow.js`

```
import sequelize from '../data-model/data-base';

const Account = sequelize.model('account');

const User = sequelize.model('user');

export default async (req,res)=>{
    
    const {access_token,userId} = req.body;
    const account = await Account.findOne({where:{access_token}});
    if(!account){
        return res.json({
            success:false,
            errorMessage:'access_token无效',
            errorCode:10003,
        })
    }

    const myself = await account.getUser();

    const friend = await User.findOne({where:{id:userId}});

    if(!friend){
        return res.json({
            success:false,
            errorMessage:'UserId无效',
            errorCode:10005,
        })
    }

    await myself.addFollowed(friend);

    return res.json({
        success:true
    });
}
```


12，取消关注接口，实现文件`unFollow.js`

```
import sequelize from '../data-model/data-base';

const Account = sequelize.model('account');
const User = sequelize.model('user');
const FriendShip = sequelize.model('friendship');

export default async (req,res)=>{
    
    const {access_token,userId} = req.body;
    const account = await Account.findOne({where:{access_token}});
    if(!account){
        return res.json({
            success:false,
            errorMessage:'access_token无效',
            errorCode:10003,
        })
    }


    await FriendShip.destroy({
        where:{
            userId:account.userId,
            followedId:userId
        }
    })

    return res.json({
        success:true
    });



}
```


13，获取所关注用户接口，实现文件`getFollow.js`

```
import sequelize from '../data-model/data-base';

const Account = sequelize.model('account');
const User = sequelize.model('user');

export default async (req,res)=>{
    
    const {access_token} = req.body;
    const account = await Account.findOne({where:{access_token}});
    if(!account){
        return res.json({
            success:false,
            errorMessage:'access_token无效',
            errorCode:10003,
        })
    }

    const user = await account.getUser();

    const follows = await user.getFollowed();

    return res.json({
        success:true,
        data:follows,
    })
}
```


### index.js

引入数据服务模块

```
import register from './data_servers/register';
import login from './data_servers/login';
import createUser from './data_servers/createUser';
import postMessage from './data_servers/postAppMessage';
import deleteMessage from './data_servers/deleteMessage';
import follow from './data_servers/follow';
import unFollow from './data_servers/unFollow';
import getFollow from './data_servers/getFollow';
import getUser from './data_servers/getUser';
import updateUser from './data_servers/updateUser';
import findUser from './data_servers/findUser';
import getMessage from './data_servers/getMessage';
import homeMessage from './data_servers/homeMessage';
import changePassword from './data_servers/changePassword';

```

配置请求路由

```
app.post('/api/register',register);
app.post('/api/login',login);
app.post('/api/createUser',createUser);
app.post('/api/postMessage',postMessage);
app.post('/api/deleteMessage',deleteMessage);
app.post('/api/follow',follow);
app.post('/api/unFollow',unFollow);
app.post('/api/getFollow',getFollow);
app.post('/api/updateUser',updateUser);
app.post('/api/getUser',getUser);
app.post('/api/getMessage',getMessage);
app.post('/api/homeMessage',homeMessage);
app.post('/api/findUser',findUser);
app.post('/api/changePassword',changePassword);
```


