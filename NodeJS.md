# NodeJS


NodeJS是将谷歌浏览器中的JavaScript V8引擎移植到了服务器运行，这样就可以使用JavaScript编写服务端代码。最大的优势是借助JavaScript天生的事件驱动机制加V8高性能引擎，使编写高性能Web服务轻而易举。


### 1，HTTP

要开发HTTP服务器程序，从头处理TCP连接，解析HTTP是不现实的。这些工作实际上已经由Node.js自带的http模块完成了。应用程序并不直接和HTTP协议打交道，而是操作http模块提供的request和response对象。

request对象封装了HTTP请求，我们调用request对象的属性和方法就可以拿到所有HTTP请求的信息；

response对象封装了HTTP响应，我们操作response对象的方法，就可以把HTTP响应返回给浏览器。

用Node.js实现一个HTTP服务器程序非常简单。我们来实现一个最简单的Web程序，它对于所有请求，都返回Hello world!

安装创建HelloWorld工程的方法创建一个工程。

然后在index.js中开始编码

```
console.log('HelloWorld');

//1，引入http模块
import http from 'http';//因http模块为系统自带模块，不需要制定相对路径

//创建http server，并传入配置事件回调函数
const server = http.createServer((request, response)=>{
    // 回调函数接收request和response对象,
    // 获得HTTP请求的method和url:
    console.log(request.method + ': ' + request.url);
    // 将HTTP响应200写入response, 同时设置Content-Type: text/html:
    response.writeHead(200, {'Content-Type': 'text/html'});
    // 将HTTP响应的HTML内容写入response:
    response.end('<h1>Hello world!</h1>');
});

// 让服务器监听8080端口:
server.listen(8080);

console.log('Server is running at http://127.0.0.1:8080/');
```
在命令行运行

```
babel-node index.js
```
>启动过程中，会弹出防火墙提升，选择允许即可

![图片13](http://7u2rl9.com1.z0.glb.clouddn.com/nodebook13.png)

服务启动成功之后，我们可以通过浏览器访问`http://127.0.0.1:8080/`

![图片14](http://7u2rl9.com1.z0.glb.clouddn.com/nodebook14.png)

这样，一个简单的node服务我们就搭建完成了。

### 2，文件读写

按照JavaScript的标准，异步读取一个文本文件的代码如下：

```
import fs from 'fs';

fs.readFile('sample.txt', 'utf-8', (err, data)=>{
    if (err) {
        console.log(err);
    } else {
        console.log(data);
    }
});
```

请注意，sample.txt文件必须在当前目录下，且文件编码为utf-8。

异步读取时，传入的回调函数接收两个参数，当正常读取时，err参数为null，data参数为读取到的String。当读取发生错误时，err参数代表一个错误对象，data为undefined。这也是Node.js标准的回调函数：第一个参数代表错误信息，第二个参数代表结果。后面我们还会经常编写这种回调函数。


如果我们要读取的文件不是文本文件，而是二进制文件，怎么办？

```
import fs from 'fs';

fs.readFile('sample.png', (err, data)=>{
    if (err) {
        console.log(err);
    } else {
        console.log(data);
        console.log(data.length + ' bytes');
    }
});
```

当读取二进制文件时，不传入文件编码时，回调函数的data参数将返回一个`Buffer`对象。在Node.js中，`Buffer`对象就是一个包含零个或任意个字节的数组（注意和`Array`不同）。

`Buffer`对象可以和`String`作转换，例如，把一个`Buffer`对象转换成`String`：

```
// Buffer -> String
var text = data.toString('utf-8');
console.log(text);
```

或者把一个String转换成Buffer：

```
// String -> Buffer
var buf = Buffer.from(text, 'utf-8');
console.log(buf);
```

将数据写入文件是通过`fs.writeFile()`实现的：

```
import fs from 'fs';

const data = 'Hello, Node.js';

fs.writeFile('output.txt', data, (err)=>{
    if (err) {
        console.log(err);
    } else {
        console.log('ok.');
    }
});
```

`writeFile()`的参数依次为文件名、数据和回调函数。如果传入的数据是String，默认按UTF-8编码写入文本文件，如果传入的参数是Buffer，则写入的是二进制文件。回调函数由于只关心成功与否，因此只需要一个err参数。

### 3，数据处理

crypto模块的目的是为了提供通用的加密和哈希算法。用纯JavaScript代码实现这些功能不是不可能，但速度会非常慢。Nodejs用C/C++实现这些算法后，通过cypto这个模块暴露为JavaScript接口，这样用起来方便，运行速度也快。

#### MD5和SHA1

MD5是一种常用的哈希算法，用于给任意数据一个“签名”。这个签名通常用一个十六进制的字符串表示：

```
import crypto from 'crypto';

const hash = crypto.createHash('md5');

// 可任意多次调用update():
hash.update('Hello, world!');
hash.update('Hello, nodejs!');

const md5String = hash.digest('hex')
console.log(md5String); // 7e1977739c748beac0c0fd14fd26a544
```

`update()`方法默认字符串编码为UTF-8，也可以传入`Buffer`。

如果要计算SHA1，只需要把`md5`改成`sha1`，就可以得到SHA1的结果`1f32b9c9932c02227819a4151feed43e131aca40`。

还可以使用更安全的`sha256`和`sha512`。

#### Hmac

Hmac算法也是一种哈希算法，它可以利用MD5或SHA1等哈希算法。不同的是，Hmac还需要一个密钥：

```
import crypto from 'crypto';

const hmac = crypto.createHmac('sha256', 'secret-key');

hmac.update('Hello, world!');
hmac.update('Hello, nodejs!');

console.log(hmac.digest('hex')); // 80f7e22570...
```

只要密钥发生了变化，那么同样的输入数据也会得到不同的签名，因此，可以把Hmac理解为用随机数“增强”的哈希算法。

#### AES（TODO）

AES是一种常用的对称加密算法，加解密都用同一个密钥。crypto模块提供了AES支持，但是需要自己封装好函数，便于使用：

```
const crypto = require('crypto');

function aesEncrypt(data, key) {
    const cipher = crypto.createCipher('aes192', key);
    var crypted = cipher.update(data, 'utf8', 'hex');
    crypted += cipher.final('hex');
    return crypted;
}

function aesDecrypt(encrypted, key) {
    const decipher = crypto.createDecipher('aes192', key);
    var decrypted = decipher.update(encrypted, 'hex', 'utf8');
    decrypted += decipher.final('utf8');
    return decrypted;
}

var data = 'Hello, this is a secret message!';
var key = 'Password!';
var encrypted = aesEncrypt(data, key);
var decrypted = aesDecrypt(encrypted, key);

console.log('Plain text: ' + data);
console.log('Encrypted text: ' + encrypted);
console.log('Decrypted text: ' + decrypted);
```

运行结果如下

```
Plain text: Hello, this is a secret message!
Encrypted text: 8a944d97bdabc157a5b7a40cb180e7...
Decrypted text: Hello, this is a secret message!
```
可以看出，加密后的字符串通过解密又得到了原始内容。

注意到AES有很多不同的算法，如aes192，aes-128-ecb，aes-256-cbc等，AES除了密钥外还可以指定IV（Initial Vector），不同的系统只要IV不同，用相同的密钥加密相同的数据得到的加密结果也是不同的。加密结果通常有两种表示方法：hex和base64，这些功能Nodejs全部都支持，但是在应用中要注意，如果加解密双方一方用Nodejs，另一方用Java、PHP等其它语言，需要仔细测试。如果无法正确解密，要确认双方是否遵循同样的AES算法，字符串密钥和IV是否相同，加密后的数据是否统一为hex或base64格式。

