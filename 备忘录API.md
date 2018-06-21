# 项目实战2-备忘录API

备忘录API提供对备忘录APP的数据服务。依据备忘录产品设计，此API提供如下数据服务：

* 注册用户
* 用户登录
* 发布事项
* 修改事项
* 删除事项
* 标记事项已完成
* 获取当前登录下所有事项

### 元数据

账户对象

```
{
	id:0
	username:'',
	password:'',
	access_token:'',
}
```

Todo条目对象

```
{
	id:0
	title:'',
	content:'',
	isFinish:true OR false,
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

错误返回值：

```
{
    success:false,
    errorCode:10001,
    errorMessage:'系统错误'
}
```

### 请求基础地址

`http://localhost:5000`

### 接口服务

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

提交备忘录条目

请求链接：`/api/postTodoItem`

请求方式：`POST`

|参数名称|参数描述|
|:---|:---|
|access_token|登录令牌|
|title|标题|
|content|内容|

```
{
    "success": true,
    "data": 
}

```

修改备忘录条目：

请求链接：`/api/updateTodoItem`

请求方式：`POST`

|参数名称|参数描述|
|:---|:---|
|access_token|登录令牌|
|id|条目ID|
|title|标题（可选）|
|content|内容（可选）|

```
{
    "success": true,
    "data": 
}

```

切换标记备忘录条目完成状态

请求链接：`/api/finishTodoItem`

请求方式：`POST`

|参数名称|参数描述|
|:---|:---|
|access_token|登录令牌|
|id|条目ID|

```
{
    "success": true,
    "data": 
}

```

删除备忘录条目

请求链接：`/api/deleteTodoItems`

请求方式：`POST`

|参数名称|参数描述|
|:---|:---|
|access_token|登录令牌|
|id|条目ID|

```
{
    "success": true,
}
```

### Sequelize配置

1，定义数据模型

用户数据模型：

```
export default (sequelize, DataTypes)=>{
    return sequelize.define('user',{
        username:DataTypes.STRING,
        password:DataTypes.STRING,
        'access_token':DataTypes.STRING,
    });
}
```

备忘录数据模型：

```
export default (sequelize, DataTypes)=>{
    return sequelize.define('todo',{
        title:DataTypes.STRING,
        content:DataTypes.STRING,
        isFinish:DataTypes.BOOLEAN,
    });
}
```

2，配置数据模型关系：

```
//导入数据模型
const Todo = sequelize.import('./Todo.js');
const User = sequelize.import('./User.js');

//配置数据关系
User.hasMany(Todo);
```

### DataServer实现

1，注册接口，实现文件`register.js`

```
import sequelize from '../data-model/data-base';
import getToken from '../utils/getToken';

const User = sequelize.model('todo_user');

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

const User = sequelize.model('todo_user');

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

3，发布备忘信息接口，实现文件`postTodoItem.js`

```
import sequelize from '../data-model/data-base';

const Todo = sequelize.model('todo');
const User = sequelize.model('todo_user');

export default async (req,res)=>{

    try {
        const {
            access_token,
            title,
            content,
        } = req.body;

        if (!access_token || !title || !content) {
            res.json({
                success:false,
                errorCode:10006,
                errorMessage:'参数无效'
            })

            return;
        }

        const users = await User.findAll({where:{access_token}});

        if(users.length == 0){
            res.json({
                success:false,
                errorCode:10004,
                errorMessage:'access_token无效'
            })

            return
        }

        const user = users[0];

        const todo = await Todo.create({
            title,
            content,
            isFinish:false
        });

        await user.addTodo(todo);

        res.json({
            success:true,
            data:todo
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

4，删除备忘信息，实现文件`deleteTodoItem.js`

```
import sequelize from '../data-model/data-base';

const Todo = sequelize.model('todo');
const User = sequelize.model('todo_user');

export default async (req,res)=>{

    try {
        const {
            access_token,
            id,
        } = req.body;

        if (!access_token || !id) {
            res.json({
                success:false,
                errorCode:10006,
                errorMessage:'参数无效'
            })

            return;
        }

        const users = await User.findAll({where:{access_token}});

        if(users.length == 0){
            res.json({
                success:false,
                errorCode:10004,
                errorMessage:'access_token无效'
            })

            return
        }


        const todos = await Todo.findAll({where:{id}});
        
        if(todos.length == 0){
            res.json({
                success:false,
                errorCode:10005,
                errorMessage:'TodoID无效'
            })
            return
        }

        const todo = todos[0];

        await todo.destroy();

        res.json({
            success:true,
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

5，修改备忘信息，实现文件`updateTodoItem.js`

```
import sequelize from '../data-model/data-base';

const Todo = sequelize.model('todo');
const User = sequelize.model('todo_user');

export default async (req,res)=>{

    try {
        const {
            access_token,
            id,
            title,
            content,
        } = req.body;

        if (!access_token || !id) {
            res.json({
                success:false,
                errorCode:10006,
                errorMessage:'参数无效'
            })

            return;
        }

        const users = await User.findAll({where:{access_token}});

        if(users.length == 0){
            res.json({
                success:false,
                errorCode:10004,
                errorMessage:'access_token无效'
            })

            return
        }

        const todos = await Todo.findAll({where:{id}});

        if(todos.length == 0){
            res.json({
                success:false,
                errorCode:10005,
                errorMessage:'TodoID无效'
            })
            return
        }

        const todo = todos[0];

        if(title){
            todo.title = title;
        }
        if(content){
            todo.content = content;
        }

        await todo.save();

        res.json({
            success:true,
            data:todo
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

6，修改备忘完成状态，实现文件`finishTodoItem.js`

```
import sequelize from '../data-model/data-base';

const Todo = sequelize.model('todo');
const User = sequelize.model('todo_user');

export default async (req,res)=>{

    try {
        const {
            access_token,
            id,
        } = req.body;

        if (!access_token || !id) {
            res.json({
                success:false,
                errorCode:10006,
                errorMessage:'参数无效'
            })

            return;
        }

        const users = await User.findAll({where:{access_token}});

        if(users.length == 0){
            res.json({
                success:false,
                errorCode:10004,
                errorMessage:'access_token无效'
            })

            return
        }

        const todos = await Todo.findAll({where:{id}});

        if(todos.length == 0){
            res.json({
                success:false,
                errorCode:10005,
                errorMessage:'TodoID无效'
            })
            return
        }

        const todo = todos[0];

        todo.isFinish = !todo.isFinish;

        await todo.save();

        res.json({
            success:true,
            data:todo
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

7，获取当前登录用户的备忘信息，实现文件为`getTodoItems.js`

```
import sequelize from '../data-model/data-base';

const Todo = sequelize.model('todo');
const User = sequelize.model('todo_user');

export default async (req,res)=>{

    try {
        const {
            access_token,
        } = req.body;

        if (!access_token) {
            res.json({
                success:false,
                errorCode:10006,
                errorMessage:'参数无效'
            })

            return;
        }

        const users = await User.findAll({where:{access_token}});

        if(users.length == 0){
            res.json({
                success:false,
                errorCode:10004,
                errorMessage:'access_token无效'
            })

            return
        }

        const user = users[0];

        const todos = await user.getTodos();


        res.json({
            success:true,
            data:todos
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

### index.js

引入数据服务模块

```
import register from './data_servers/register';
import login from './data_servers/login';
import postTodoItem from './data_servers/postTodoItem';
import updateTodoItem from './data_servers/updateTodoItem';
import deleteTodoItem from './data_servers/deleteTodoItem';
import getTodoItems from './data_servers/getTodoItems';
import finishTodoItem from './data_servers/finishTodoItem';
```
配置请求路由

```
app.post('/api/register',register);
app.post('/api/login',login);
app.post('/api/postTodoItem',postTodoItem);
app.post('/api/updateTodoItem',updateTodoItem);
app.post('/api/deleteTodoItem',deleteTodoItem);
app.post('/api/getTodoItems',getTodoItems);
app.post('/api/finishTodoItem',finishTodoItem);
```