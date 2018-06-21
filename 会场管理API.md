# 项目实战1-会场管理API

## API文档

### 元数据

|字段名|类型|描述|
|:---|:---|:---|
|id|int|唯一且自增，由数据库维护，作为参会登记号|
|name|string|参会者姓名|
|tel|string|参数者电话|
|isCheck|int|是否签到。0-未签到，1-已签到，默认未签到|


### 错误列表

|错误编号|描述|
|:---|:---|
|10001|数据库错误|
|10002|rid无效|

```
{
    success:false,
    errorCode:10001,
    errorMessage:'数据库错误'
}
```

### 请求基础地址

`http://localhost:5000`

### 接口描述

**参会登记**

请求路径： `/api/register`

请求方式：`POST`

请求参数：

|字段名|类型|描述|
|:---|:---|:---|
|name|string|参会者姓名|
|tel|string|参数者电话|


返回值：

```
{
    success:true,
    data:{
        id:1,
        name:'xx',
        tel:'123',
        isCheck:0
    }
}
```

**取消登记**

请求路径： `/api/unRegister`

请求方式：`POST`

请求参数：

|字段名|类型|描述|
|:---|:---|:---|
|rid|string|参会者登记号|



返回值：

```
{
    success:true,
}
```

**签到**

请求路径： `/api/check`

请求方式：`POST`

请求参数：

|字段名|类型|描述|
|:---|:---|:---|
|rid|string|参会者登记号|



返回值：

```
{
    success:true,
}
```

**查询统计信息**

请求路径： `/api/tongji`

请求方式：`GET`

请求参数：无


返回值：

```
{
    success:true,
    data:{
        total:10,
        checked:3,
        unCheck:7,
    }
}
```

**查询已签到名单**

请求路径： `/api/yiqiandao`

请求方式：`GET`

请求参数：无


返回值：

```
{
    success:true,
    data:[
        {
            id:1,
            name:'xx',
            tel:'123',
            isCheck:1
        },
        ...
    ]
}
```


**查询未签到名单**

请求路径： `/api/weiqiandao`

请求方式：`GET`

请求参数：无


返回值：

```
{
    success:true,
    data:[
        {
            id:1,
            name:'xx',
            tel:'123',
            isCheck:0
        },
        ...
    ]
}
```

**查询个人签到状态**

请求路径： `/api/checkPerson`

请求方式：`POST`

请求参数：

|字段名|类型|描述|
|:---|:---|:---|
|rid|string|参会者登记号|



返回值：

```
{
    success:true,
    data:{
        id:1,
        name:'xx',
        tel:'123',
        isCheck:0
    }
}
```

### Sequelize配置

1，定义数据模型

```
export default (sequelize, DataTypes)=>{
    return sequelize.define('person',{
        name:DataTypes.STRING,
        tel:DataTypes.STRING,
        isCheck:DataTypes.INTEGER,
    });
}
```

2，配置Sequelize

```
//引入Sequelize框架
import Sequelize  from 'sequelize';

//通过Sequelize构造方法创建Sequelize实例并链接数据库
const sequelize = new Sequelize(
  'meeting_api',
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



//导入数据模型

const Person = sequelize.import('./Person.js');

//配置数据关系

//同步数据
sequelize.sync({force:false});

export default sequelize;
```

### DataServer实现

1，注册接口，实现文件`register.js`

```
import sequelize from '../data-model/data-base';

//此变量为ORM数据模型，通过此对象既可以操作相应数据
const Person = sequelize.model('person');

export default async (req,res)=>{
    try {
        //打印body内容
        console.log(req.body);

        //从body中获取数据
        const name = req.body.name;
        const tel = req.body.tel;

        //创建一个数据
        const person = await Person.create({
            name,
            tel,
            isCheck:0
        });

        res.json({
            success:true,
            data:person
        })



    } catch (error) {
        res.json({
            success:false,
            errorCode:10001,
            errorMessage:'数据库错误'
        })
    }
}
```

2，取消注册接口，实现文件`unRegister.js`

```
import sequelize from '../data-model/data-base';

//此变量为ORM数据模型，通过此对象既可以操作相应数据

const Person = sequelize.model('person');

export default async (req,res)=>{
    try {
        
        console.log(req.body);

        const rid = req.body.rid;

        const people = await Person.findAll({where:{id:rid}})

        if(people.length == 0){

            res.json({
                success:false,
                errorCode:10002,
                errorMessage:'rid无效'
            })

            return;
        }

        const person = people[0];

        await person.destroy()

        res.json({
            sucess:true,
        })


    } catch (error) {
        res.json({
            success:false,
            errorCode:10001,
            errorMessage:'数据库错误'
        })
    }
}
```

3，签到接口，实现文件`check.js`

```
import sequelize from '../data-model/data-base';

//此变量为ORM数据模型，通过此对象既可以操作相应数据

const Person = sequelize.model('person');

export default async (req,res)=>{
    try {
        
        console.log(req.body);

        const rid = req.body.rid;

        const people = await Person.findAll({where:{id:rid}})

        if(people.length == 0){

            res.json({
                success:false,
                errorCode:10002,
                errorMessage:'rid无效'
            })

            return;
        }

        const person = people[0];

        person.isCheck = 1;
        await person.save();

        res.json({
            sucess:true,
        })


    } catch (error) {
        res.json({
            success:false,
            errorCode:10001,
            errorMessage:'数据库错误'
        })
    }
}
```

4，查询统计信息结构，实现文件`tongji.js`

```
import sequelize from '../data-model/data-base';

//此变量为ORM数据模型，通过此对象既可以操作相应数据

const Person = sequelize.model('person');

export default async (req,res)=>{
    try {
        
       const people = await Person.findAll();
       const total = people.length;
       let checked = 0;
       let unChecked = 0;

       for(let i=0;i<people.length;i++){
           const person = people[i];
           if(person.isCheck == 0){
                unChecked++;
           }else{
                checked++;
           }
       }

       res.json({
           success:true,
           data:{
                total,
                checked,
                unChecked
           }
       })



    } catch (error) {
        res.json({
            success:false,
            errorCode:10001,
            errorMessage:'数据库错误'
        })
    }
}
```

5，查询已签到名单接口，实现文件`yiqiandao.js`

```
import sequelize from '../data-model/data-base';

//此变量为ORM数据模型，通过此对象既可以操作相应数据

const Person = sequelize.model('person');

export default async (req, res) => {
    try {

        const people = await Person.findAll();

        const checkeds = [];

        for (let i = 0; i < people.length; i++) {
            const person = people[i];
            if (person.isCheck == 1) {
                checkeds.push(person)
            }
        }
        res.json({
            success: true,
            data:checkeds
        })



    } catch (error) {
        res.json({
            success: false,
            errorCode: 10001,
            errorMessage: '数据库错误'
        })
    }
}
```

6，未签到名单，实现文件`weiqiandao.js`

```
import sequelize from '../data-model/data-base';

//此变量为ORM数据模型，通过此对象既可以操作相应数据

const Person = sequelize.model('person');

export default async (req, res) => {
    try {

        const people = await Person.findAll();

        const checkeds = [];

        for (let i = 0; i < people.length; i++) {
            const person = people[i];
            if (person.isCheck == 0) {
                checkeds.push(person)
            }
        }
        res.json({
            success: true,
            data:checkeds
        })



    } catch (error) {
        res.json({
            success: false,
            errorCode: 10001,
            errorMessage: '数据库错误'
        })
    }
}
```

7，查询个人信息，实现文件`checkPerson.js`

```
import sequelize from '../data-model/data-base';

//此变量为ORM数据模型，通过此对象既可以操作相应数据

const Person = sequelize.model('person');

export default async (req,res)=>{
    try {
        
        console.log(req.body);

        const rid = req.body.rid;

        const people = await Person.findAll({where:{id:rid}})

        if(people.length == 0){

            res.json({
                success:false,
                errorCode:10002,
                errorMessage:'rid无效'
            })

            return;
        }

        const person = people[0];

        res.json({
            sucess:true,
            data:person
        })


    } catch (error) {
        res.json({
            success:false,
            errorCode:10001,
            errorMessage:'数据库错误'
        })
    }
}
```

### index.js

引入数据服务模块：

```
import register from './data_servers/register';
import unRegister from './data_servers/unRegister';
import check from './data_servers/check';
import tongji from './data_servers/tongji';
import yiqiandao from './data_servers/yiqiandao';
import weiqiandao from './data_servers/weiqiandao';
import checkPerson from './data_servers/checkPerson';
```

配置请求路由：

```
app.get('/api/getMessages',getMessages);
app.post('/api/postMessage',postMessage);
app.post('/api/register',register);
app.post('/api/unRegister',unRegister);
app.post('/api/check',check);
app.get('/api/tongji',tongji);
app.get('/api/yiqiandao',yiqiandao);
app.get('/api/weiqiandao',weiqiandao);
app.post('/api/checkPerson',checkPerson);
```