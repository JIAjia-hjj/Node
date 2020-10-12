### 终端基本使用

##### 打开应用

- notepad 打开记事本
- mspaint 打开画图
- calc 打开计算机
- write 写字板
- sysdm.cpl 打开环境变量设置窗口
##### 常用命令

- md 创建目录
- rmdir(rd) 删除目录，目录内没有文档。
- echo on a.txt 创建空文件
- del 删除文件
- rm 文件名 删除文件
- cat 文件名 查看文件内容
- cat > 文件名 向文件中写上内容。

### 服务器端模块化

- 服务器端模块化规范CommonJS与实现Node.js
- 模块导出与引入
- 模块导出机制分析
- 模块加载规则
    + 模块查找 不加扩展名的时候会按照如下后缀顺序进行查找 .js .json .node
- 模块分类
    + 自定义模块
    + 系统核心模块
        * fs 文件操作
        * http 网络操作
        * path 路径操作
        * querystring 查询参数解析
        * url url解析
        * ......

### 一.认识Node

+ Node.js平台使用的开发语言是JavaScript，平台提供了操作系统低层的API，方便做服务器端编程，具体包括文件操作、进程操作、通信操作等系统模块

##### 1.1.开发环境准备

+ 普通安装方式[官方网站](https://nodejs.org/zh-cn/)

+ 多版本安装方式
  + 卸载已有的Node.js
  + 下载[nvm](https://github.com/coreybutler/nvm-windows)（ nodejs的版本管理工具 ）
  + 在C盘创建目录dev
  + 在dev目中中创建两个子目录nvm和nodejs
  + 并且把nvm包解压进去nvm目录中
  + 在install.cmd文件上面右键选择【以管理员身份运行】
  + 打开的cmd窗口直接回车会生成一个settings.txt文件，修改文件中配置信息
  + 配置nvm和Node.js环境变量
    + NVM_HOME:C:\dev\nvm
    + NVM_SYMLINK:C:\dev\nodejs
    + 把配置好的两个环境变量加到Path中

##### 1.2.Node.js 是什么

+ JavaScript 运行时
+ 既不是语言，也不是框架，它是一个平台

##### 1.3.Node.js 中的 JavaScript

+ 没有 BOM、DOM
+ EcmaScript 基本的 JavaScript 语言部分
+ 在 Node 中为 JavaScript 提供了一些服务器级别的 API
  * 文件操作的能力，浏览器中的 JavaScript 是没有文件操作的能力的
  * http 服务的能力

##### 1.4.Node.js可以用来做什么

- 具有复杂逻辑的动态网站 
- WebSocket服务器 
- 命令行工具 
- 带有图形界面的本地应用程序 
- ......

##### 1.5.nvm常用的命令

- nvm list 查看当前安装的Node.js所有版本
- nvm install 版本号 安装指定版本的Node.js
- nvm uninstall 版本号 卸载指定版本的Node.js
- nvm use 版本号 选择指定版本的Node.js

### 二.创建第一个项目

+ node 可以运行js文件

```js
var foo = 'hello nodejs'
console.log(foo)

//运行该文件 终端会打印出：hello nodejs
```

+ 简单的一个项目

```js
//serve.js
//引入require
var http=require("http");
http.createServer(function (request, response) {
  // 发送 HTTP 头部
  // HTTP 状态值: 200 : OK
  // 内容类型: text/plain
  response.writeHead(200, {'Content-Type': 'text/plain'});
  // 发送响应数据 "Hello World"
  response.end('Hello World\n');
}).listen(8888);
// 终端打印如下信息
console.log('Server running at http://127.0.0.1:8888/');
```

```运行
运行： node serve.js
```

### 三.NPM

##### 3.1.常用的命令

+ npm -v  查看npm的当前版本
+ npm init  初始化项目 ，生成一个pakeage.json文件 
+ npm init -y 跳过向导，快速生成

- npm install   根据package.json安装
- npm install <包名>       只下载
- npm install  --save <包名>      下载并保存依赖项（package.json 文件中的 dependecncies）npm i -S
- npm uninstall  <包名>       只 删除包  如果有依赖项会依然保存
- npm uninstall  --save <包名>       删除同时也会把依赖信息删除   npm un -S
- npm --help         查看使用帮助
- npm 命令 --help          查看具体命令
- npm config list  查看npm 配置信息

##### 3.2.使用淘宝 NPM 镜像

```cmd
npm install -g cnpm --registry=https://registry.npm.taobao.org
```

```cmd
cnpm install [name]
```

##### package.json 包描述文件

+ 就是产品的说明书
+ `dependencies` 属性，用来保存项目的第三方包依赖项信息
+ 所以建议每个项目都要有且只有一个 package.json (存放在项目的根目录)
+ 我们可以通过 `npm init [--yes]` 来生成 package.json 文件
+ 同样的，为了保存依赖项信息，我们每次安装第三方包的时候都要加上：`--save` 选项。

##### 模块查找机制

+ 优先从缓存加载
+ 核心模块
+ 路径形式的文件模块
+ 第三方模块 
  +  node_modules/art-template/  
  + node_modules/art-template/package.json  
  + node_modules/art-template/package.json main  index.js 备选项  
  + 进入上一级目录找 node_modules  按照这个规则依次往上找，直到磁盘根目录还找不到，最后报错：Can not find moudle xxx一个项目有且仅有一个 node_modules 而且是存放到项目的根目录

### 四.模块系统

##### 4.1.模块系统

* 在 Node 中没有全局作用域的概念
* 在 Node 中，只能通过 require 方法来加载执行多个 JavaScript 脚本文件
* require 加载只能是执行其中的代码，文件与文件之间联系是模块作用域，所以不会有污染的问题
  - 模块完全是封闭的
  - 外部无法访问内部
  - 内部也无法访问外部
* 模块作用域固然带来了一些好处，可以加载执行多个文件，可以完全避免变量命名冲突污染的问题
* 但是某些情况下，模块与模块是需要进行通信的
* 在 Node 中，模块有三种
  + 具名的核心模块，例如 fs、http
  + 用户自己编写的文件模块，
    + 相对路径必须加 ./。相对路径中的 ./ 不能省略，否则报错
    + 可以省略后缀名
  + 第三方模块,凡是第三方模块都必须通过 npm 来下载

#####  4.2.核心模块

* 核心模块是由 Node 提供的一个个的具名的模块，它们都有自己特殊的名称标识，例如
  - fs 文件操作模块
  - http 网络服务构建模块
  - os 操作系统信息模块
  - path 路径处理模块
  - 。。。。
* 所有核心模块在使用的时候都必须手动的先使用 `require` 方法来加载，然后才可以使用，例如：
  - `var fs = require('fs')`



##### 4.3.exports和module.exports 

- exports 和 module.exports 的区别

  + 每个模块中都有一个 module 对象
  + module 对象中有一个 exports 对象
  + 我们可以把需要导出的成员都挂载到 module.exports 接口对象中
  + 也就是：`moudle.exports.xxx = xxx` 的方式
  + 但是每次都 `moudle.exports.xxx = xxx` 很麻烦，点儿的太多了
  + 所以 Node 为了你方便，同时在每一个模块中都提供了一个成员叫：`exports`
  + `exports === module.exports` 结果为  `true`s
  + 所以对于：`moudle.exports.xxx = xxx` 的方式 完全可以：`expots.xxx = xxx`
  + 当一个模块需要导出单个成员的时候，这个时候必须使用：`module.exports = xxx` 的方式
  + 不要使用 `exports = xxx` 不管用
  + 因为每个模块最终向外 `return` 的是 `module.exports`
  + 而 `exports` 只是 `module.exports` 的一个引用
  + 所以即便你为 `exports = xx` 重新赋值，也不会影响 `module.exports`
  + 但是有一种赋值方式比较特殊：`exports = module.exports` 这个用来重新建立引用关系的
  + 之所以让大家明白这个道理，是希望可以更灵活的去用它

  



+ require 方法有两个作用：

  +  加载文件模块并执行里面的代码

  + 拿到被加载文件模块导出的接口对象

     

+ exports
  + 在每个模块中，都提供了一个对象：`exports`
  + 该对象默认是一个空对象
  + 你要做的就是把需要被外部访问使用的成员手动的挂载到 `exports` 接口对象中
  + 然后谁来 `require` 这个模块，谁就可以得到模块内部的 `exports` 接口对象

```js
 //main.js
var fooExports = require('./hello');
console.log(fooExports.str);
fooExports.add();
//foo.js
exports.str='hello'
exports.add=function () {
    return x + y
}
```

+ module.exports  
  + 如果一个模块需要直接导出某个成员，而非挂载的方式
  +  那这个时候必须使用下面这种方式

```js
//main.js 
var fooExports = require('./foo')
console.log(fooExports)//[Function]
//foo.js
module.exports = 'hello'
module.exports = function (x, y) {
  return x + y
}


//main.js 
var fooExports = require('./foo')
console.log(fooExports)//{ add: [Function: add], str: 'hello' }
//foo.js
module.exports = {
  add: function () {
    return x + y
  },
  str: 'hello'
}
```

+ 原理
  + 我们发现，每次导出接口成员的时候都通过 module.exports.xxx = xxx 的方式很麻烦，点儿的太多了
  + 所以，Node 为了简化你的操作，专门提供了一个变量：exports 等于 module.exports

```js
 var module = {
   exports: {
     foo: 'bar',
     add: function
  }
 }
```



##### 4.4.fs 文件操作模块

+ 读文件 fs.readFile ('文件路径'，['编码']，'回调函数') 

```js
// 1. 使用 require 方法加载 fs 核心模块
var fs = require('fs')
// 2. 读取文件 第一个参数就是要读取的文件路径，第二个参数是一个回调函数
//        成功
//          data 数据
//          error null
//        失败
//          data undefined没有数据
//          error 错误对象
fs.readFile('./data/a.txt', function (error, data) {
  // <Buffer 68 65 6c 6c 6f 20 6e 6f 64 65 6a 73 0d 0a>
  // 文件中存储的其实都是二进制数据 0 1
  // 这里为什么看到的不是 0 和 1 呢？原因是二进制转为 16 进制了
  // 但是无论是二进制01还是16进制，人类都不认识
  // 所以我们可以通过 toString 方法把其转为我们能认识的字符
  // console.log(data)
  // console.log(error)

  // 在这里就可以通过判断 error 来确认是否有错误发生
  if (error) {
    console.log('读取文件失败了')
  } else {
    console.log(data.toString())
  }
})

```

+ 写文件  fs.writeFile('文件路径'，'要写入的内容'，['编码']，'回调函数');

```js
var fs = require('fs')
// 第一个参数：文件路径， 第二个参数：文件内容， 第三个参数：回调函数
//    成功：
//      文件写入成功
//      error 是 null
//    失败：
//      文件写入失败
//      error 就是错误对象
fs.writeFile('./data/你好.md', '大家好，给大家介绍一下，我是Node.js', function (error) {
  // console.log('文件写入成功')
  // console.log(error)
  if (error) {
    console.log('写入失败')
  } else {
    console.log('写入成功了')
  }
})

```

+ 1.如何得到 wwwDir 目录列表中的文件名和目录名
  + fs.readdir  
+  2.如何将得到的文件名和目录名替换到 template.html 中
  + 2.1.在 template.html 中需要替换的位置预留一个特殊的标记（就像以前使用模板引擎的标记一样）
  +  2.2.根据 files 生成需要的 HTML 内容

```js
var wwwDir = 'D:/Movie/www'
fs.readdir(wwwDir, function (err, files) {
      if (err) {
        return res.end('Can not find www dir.')
      }
      // 2.1 生成需要替换的内容
      var content = ''
      files.forEach(function (item) {
        // 在 EcmaScript 6 的 ` 字符串中，可以使用 ${} 来引用变量
        content += `
          <tr>
            <td data-value="apple/"><a class="icon dir" href="/D:/Movie/www/apple/">${item}/</a></td>
            <td class="detailsColumn" data-value="0"></td>
            <td class="detailsColumn" data-value="1509589967">2017/11/2 上午10:32:47</td>
          </tr>
        `
      })

      // 2.3 替换
      data = data.toString()
      data = data.replace('^_^', content)

      // 3. 发送解析替换过后的响应数据
      res.end(data)
    })
```



##### 4.5.http 网络服务构建模块

+ Node 非常轻松的构建一个 Web 服务器

```js
// 1. 加载 http 核心模块
var http=require('http'))
// 2. 使用 http.createServer() 方法创建一个 Web 服务器，返回一个 Server 实例
var server = http.createServer()
// 3. 服务器要干嘛？当客户端请求过来，就会自动触发服务器的 request 请求事件，然后执行第二个参数：回调处理函数
server.on('request', function (req, res) {
   console.log('收到客户端的请求了，请求路径是：' + req.url);
   console.log('请求我的客户端的地址是：', req.socket.remoteAddress, req.socket.remotePort)
    
  //response 对象有一个方法：write 可以用来给客户端发送响应数据
  //write 可以使用多次，但是最后一定要使用 end 来结束响应，否则客户端会一直等待
  //res.write('hello')
  //res.write(' world')
  // 告诉客户端，我的话说完了，你可以呈递给用户了
  //response.end()  
   //=================================== 等价
  //上面的方式比较麻烦，推荐使用更简单的方式，直接 end 的同时发送响应数据  
  // res.end('hello nodejs')
    
  /**
    需求一：根据不同的请求路径发送不同的响应结果，例如：/ index，/login 登陆，/register 注册
    1）. 获取请求路径：req.url 获取到的是端口号之后的那一部分路径，也就是说所有的 url 都是以 / 开头的
    2）. 判断路径处理响应
  **/
  var url = req.url  
  
  if (url === '/') {
    res.end('index page')
  } else if (url === '/login') {
    res.end('login page')
  } else if (url === '/products') {
    var products = [{
        name: '苹果 X',
        price: 8888
      },
      {
        name: '菠萝 X',
        price: 5000
      },
      {
        name: '小辣椒 X',
        price: 1999
      }
    ]

    // 响应内容只能是二进制数据或者字符串
    //  数字 对象 数组  布尔值
    //JSON.stringify：json对象转json字符串
    res.end(JSON.stringify(products))
  } else {
    res.end('404 Not Found.')
  }
})
// 4. 绑定端口号，启动服务器
server.listen(3000, function () {
  console.log('服务器启动成功了，可以通过 http://127.0.0.1:3000/ 来进行访问')
})
```

+ 端口号（见上）
  + ip 地址定位计算机  requset.socket.remoteAddress
  + 端口号定位具体的应用程序   requset.socket.remotePort

+ Content-Type
  * 服务器最好把每次响应的数据是什么内容类型都告诉客户端，而且要正确的告诉
  * 不同的资源对应的 Content-Type 是不一样，具体参照：http://tool.oschina.net/commons
  * 对于文本类型的数据，最好都加上编码，目的是为了防止中文解析乱码问题
  * text/plain---文本；text/html--html 格式的字符串；image/jpeg--图片

```js
var http = require('http')
var server = http.createServer()
server.on('request', function (req, res) {
  // 在服务端默认发送的数据，其实是 utf8 编码的内容
  // 但是浏览器不知道你是 utf8 编码的内容
  // 浏览器在不知道服务器响应内容的编码的情况下会按照当前操作系统的默认编码去解析
  // 中文操作系统默认是 gbk
  // 解决方法就是正确的告诉浏览器我给你发送的内容是什么编码的
  // 在 http 协议中，Content-Type 就是用来告知对方我给你发送的数据内容是什么类型
  // res.setHeader('Content-Type', 'text/plain; charset=utf-8')
  // res.end('hello 世界')

  var url = req.url
 //需求二：根据请求响应数据的内容类型不同来设置不同的响应头，例如：/plain普通文本类型，/html 发送html类型 
  if (url === '/plain') {
    // text/plain 就是普通文本
    res.setHeader('Content-Type', 'text/plain; charset=utf-8')
    res.end('hello 世界')
  } else if (url === '/html') {
    // 如果你发送的是 html 格式的字符串，则也要告诉浏览器我给你发送是 text/html 格式的内容
    res.setHeader('Content-Type', 'text/html; charset=utf-8')
    res.end('<p>hello html <a href="">点我</a></p>')
  }
})
```

+ 通过网络发送文件
  + 发送的并不是文件，本质上来讲发送是文件的内容
  + 当浏览器收到服务器响应内容之后，就会根据你的 Content-Type 进行对应的解析处理

```js
// 1. 结合 fs 发送文件中的数据
// 2. Content-Type
//    http://tool.oschina.net/commons
//    不同的资源对应的 Content-Type 是不一样的
//    图片不需要指定编码
//    一般只为字符数据才指定编码

var http = require('http')
var fs = require('fs')

var server = http.createServer()

server.on('request', function (req, res) {
  // / index.html
  var url = req.url

  if (url === '/') {
    // 肯定不这么干
    // res.end('<!DOCTYPE html><html lang="en"><head><meta charset="UTF-8"><title>Document</title></head><body><h1>首页</h1></body>/html>')

    // 我们要发送的还是在文件中的内容
    fs.readFile('./resource/index.html', function (err, data) {
      if (err) {
        res.setHeader('Content-Type', 'text/plain; charset=utf-8')
        res.end('文件读取失败，请稍后重试！')
      } else {
        // data 默认是二进制数据，可以通过 .toString 转为咱们能识别的字符串
        // res.end() 支持两种数据类型，一种是二进制，一种是字符串
        res.setHeader('Content-Type', 'text/html; charset=utf-8')
        res.end(data)
      }
    })
  } else if (url === '/xiaoming') {
    // url：统一资源定位符
    // 一个 url 最终其实是要对应到一个资源的
    fs.readFile('./resource/ab2.jpg', function (err, data) {
      if (err) {
        res.setHeader('Content-Type', 'text/plain; charset=utf-8')
        res.end('文件读取失败，请稍后重试！')
      } else {
        // data 默认是二进制数据，可以通过 .toString 转为咱们能识别的字符串
        // res.end() 支持两种数据类型，一种是二进制，一种是字符串
        // 图片就不需要指定编码了，因为我们常说的编码一般指的是：字符编码
        res.setHeader('Content-Type', 'image/jpeg')
        res.end(data)
      }
    })
  }
})

server.listen(3000, function () {
  console.log('Server is running...')
})

```

##### 4.6.url

```js
var url = require('url')

var obj = url.parse('/pinglun?name=的撒的撒&message=的撒的撒的撒', true)

console.log(obj)
console.log(obj.query)
/**Url {
  protocol: null,
  slashes: null,
  auth: null,
  host: null,
  port: null,
  hostname: null,
  hash: null,
  search: '?name=的撒的撒&message=的撒的撒的撒',
  query: [Object: null prototype] { name: '的撒的撒', message: '的撒的撒的撒' },
  pathname: '/pinglun',
  path: '/pinglun?name=的撒的撒&message=的撒的撒的撒',
  href: '/pinglun?name=的撒的撒&message=的撒的撒的撒' 
}**/
```



##### 4.7.其他核心模块应用

```js
// 用来获取机器信息的
var os = require('os')

// 用来操作路径的
var path = require('path')

// 获取当前机器的 CPU 信息
console.log(os.cpus())

// memory 内存
console.log(os.totalmem())

// 获取一个路径中的扩展名部分
// extname extension name
console.log(path.extname('c:/a/b/c/d/hello.txt'))

```

#####  4.8.模块标识

+ 文件操作中的相对路径可以省略 .
  + ./data/a.txt 相对于当前目录
  + data/a.txt   相对于当前目录
  + /data/a.txt  绝对路径，当前文件模块所处磁盘根目录
  + c:/xx/xx...  绝对路径

```js
fs.readFile('data/a.txt', function (err, data) {
  if (err) {
    return console.log('读取失败')
  }
  console.log(data.toString())
})
```

+ 在模块加载中，相对路径中的 ./ 不能省略

```js
// Error: Cannot find module 'data/foo.js'
require('data/foo.js')
require('./data/foo.js')('hello')
```

```js
// 这里如果忽略了 . 则也是磁盘根目录
require('/data/foo.js')
```

+ 

### 五.在node中使用模板引擎

##### 5.1.安装

+ npm install art-template

+ 该命令在哪执行就会把包下载到哪里。默认会下载到 node_modules 目录中
+ 模板引起最早就是诞生于服务器领域，后来才发展到了前端。

##### 5.2.使用

+  在需要使用的文件模块中加载 art-template：require('art-template')
+ 查文档，使用模板引擎的 API

```js
var template = require('art-template')
var fs = require('fs')

fs.readFile('./tpl.html', function (err, data) {
  if (err) {
    return console.log('读取文件失败了')
  }
  // 默认读取到的 data 是二进制数据
  // 而模板引擎的 render 方法需要接收的是字符串
  // 所以我们在这里需要把 data 二进制数据转为 字符串 才可以给模板引擎使用
  var ret = template.render(data.toString(), {
    name: 'Jack',
    age: 18,
    province: '北京市',
    hobbies: [
      '写代码',
      '唱歌',
      '打游戏'
    ],
    title: '个人信息'
  })
  console.log(ret)
})

```

```html
<!--./tpl.html-->
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>{{ title }}</title>
</head>
<body>
  <p>大家好，我叫：{{ name }}</p>
  <p>我今年 {{ age }} 岁了</p>
  <h1>我来自 {{ province }}</h1>
  <p>我喜欢：{{each hobbies}} {{ $value }} {{/each}}</p>
  <script>
    var foo = '{{ title }}'
  </script>
</body>
</html>
```



### require

##### require 方法加载规则

+ 优先从缓存加载
+ 核心模块
+ 路径形式的模块
  * `./xxx`
  * `../xxxx`
  * `/xxxx` / 在这里表示的是磁盘根路径
  * `c:/xxx`
+ 第三方模块
  * 第三方模块的标识就是第三方模块的名称（不可能有第三方模块和核心模块的名字一致）
  * npm
    - 开发人员可以把写好的框架、库发布到 npm 上
    - 使用者在使用的时候就可以很方便的通过 npm 来下载
  * 使用方式：`var 名字 = require('npm install 的那个包名')`
  * node_modules
  * node_modules/express
  * node_modules/express/package.json
  * node_modules/express/package.json main
  * 如果 package.json 或者 package.json main 不成立，则查找备选项：index.js
  * 如果以上条件都不成立，则继续进入上一级目录中的 node_modules 按照上面的规则继续查找
  * 如果直到当前文件模块所属磁盘根目录都找不到，最后报错：`can not find module xxx`

##### require优先从缓存加载

```js
//main.js
require('./a')

// 优先从缓存加载
// 由于 在 a 中已经加载过 b 了
// 所以这里不会重复加载
// 可以拿到其中的接口对象，但是不会重复执行里面的代码
// 这样做的目的是为了避免重复加载，提高模块加载效率
var fn = require('./b')

console.log(fn())

//a.jsm
console.log('a.js 被加载了')
var fn = require('./b')
console.log(fn())

//b.js
console.log('b.js 被加载了')
module.exports = function () {
  console.log('hello bbb')
}


//a.js 被加载了
//b.js 被加载了
//'hello bbb'
//'hello bbb'
```

##### require标识符分析



### express

##### 基本使用

```js
// 0. 安装 npm install express --save
// 1. 引包
var express = require('express')

// 2. 创建你服务器应用程序  也就是原来的 http.createServer
var app = express()

// 在 Express 中开放资源就是一个 API 的事儿
// 公开指定目录
// 只要这样做了，你就可以直接通过 /public/xx 的方式访问 public 目录中的所有资源了
app.use('/public/', express.static('./public/'))
app.use('/static/', express.static('./static/'))
app.use('/node_modules/', express.static('./node_modules/'))

// 模板引擎，在 Express 也是一个 API 的事儿

// 得到路径
// 一个一个的判断
// 以前的代码很丑

app.get('/about', function (req, res) {
  // 在 Express 中可以直接 req.query 来获取查询字符串参数
  console.log(req.query)
  res.send('你好，我是 Express!')
})

app.get('/pinglun', function (req, res) {
  // req.query
  // 在 Express 中使用模板引擎有更好的方式：res.render('文件名， {模板对象})
  // 可以自己尝试去看 art-template 官方文档：如何让 art-template 结合 Express 来使用
})

// 当服务器收到 get 请求 / 的时候，执行回调处理函数
app.get('/', function (req, res) {
  res.send(`
    <!DOCTYPE html>
    <html lang="en">
      <head>
        <meta charset="UTF-8" />
        <title>Document</title>
      </head>
    <body>
      <h1>hello Express！你好</h1>
    </body>
    </html>
`)
})

// 相当于 server.listen
app.listen(3000, function () {
  console.log('app is running at port 3000.')
})
```

+ 详解了 express 静态服务 API

```
app.use('/public/', express.static('./public'))
```

##### art-template 模板引擎的配置

+ app.engine('html', require('express-art-template'))

```js
var express = require('express')
var app = express()
// 配置使用 art-template 模板引擎
// 第一个参数，表示，当渲染以 .art 结尾的文件的时候，使用 art-template 模板引擎
// express-art-template 是专门用来在 Express 中把 art-template 整合到 Express 中
// 虽然外面这里不需要记载 art-template 但是也必须安装
// 原因就在于 express-art-template 依赖了 art-template
app.engine('html', require('express-art-template'))
```

+ res.render('html模板名', {模板数据})
  + Express 为 Response 相应对象提供了一个方法：render
  +  render 方法默认是不可以使用，但是如果配置了模板引擎就可以使用了
  +  第一个参数不能写路径，默认会去项目中的 **views** 目录查找该模板文件
  + 也就是说 Express 有一个约定：开发人员把所有的视图文件都放到 views 目录中
  +  如果想要修改默认的 views 目录，则可以
    + app.set('views', render函数的默认路径)







#####  body-parser 解析表单 POST 请求体

```js
// 配置 body-parser 中间件（插件，专门用来解析表单 POST 请求体）
// parse application/x-www-form-urlencoded
app.use(bodyParser.urlencoded({ extended: false }))
// parse application/json
app.use(bodyParser.json())
```

+ 根据不同请求路径进行处理

```js
var comments = [{
    name: '张三',
    message: '今天天气不错！',
    dateTime: '2015-10-16'
  },
  {
    name: '张三2',
    message: '今天天气不错！',
    dateTime: '2015-10-16'
  },
  {
    name: '张三3',
    message: '今天天气不错！',
    dateTime: '2015-10-16'
  },
  {
    name: '张三4',
    message: '今天天气不错！',
    dateTime: '2015-10-16'
  },
  {
    name: '张三5',
    message: '今天天气不错！',
    dateTime: '2015-10-16'
  }
]
```



+ get

```js
app.get('/', function (req, res) {
  res.render('index.html', {
    comments: comments
  })
})

app.get('/post', function (req, res) {
  res.render('post.html')
})
```

+ post 

```js
// 当以 POST 请求 /post 的时候，执行指定的处理函数
// 这样的话我们就可以利用不同的请求方法让一个请求路径使用多次
app.post('/post', function (req, res) {
  // 1. 获取表单 POST 请求体数据
  // 2. 处理
  // 3. 发送响应

  // req.query 只能拿 get 请求参数
  // console.log(req.query)

  var comment = req.body
  comment.dateTime = '2020-10-07 10:58:51'
  comments.unshift(comment)

  // res.send
  // res.redirect
  // 这些方法 Express 会自动结束响应
  res.redirect('/')
  // res.statusCode = 302
  // res.setHeader('Location', '/') 
})
```

###  

##### 使用路由（crud-express案例）



+ app.js： 是一个入门模块

  + 职责：

    + 创建服务

    + 做一些服务相关配置：

      + 模板引擎

      + body-parser 解析表单 post 请求体
      + 提供静态资源服务

    + 挂载路由

    + 监听端口启动服务

```js
var express = require('express')
var router = require('./router')
var bodyParser = require('body-parser')

var app = express()//创建你服务器应用程序

app.use('/node_modules/', express.static('./node_modules/'))
app.use('/public/', express.static('./public/'))

app.engine('html', require('express-art-template'))//模板引擎

// 配置模板引擎和 body-parser 一定要在 app.use(router) 挂载路由之前
// parse application/x-www-form-urlencoded
app.use(bodyParser.urlencoded({ extended: false }))
// parse application/json
app.use(bodyParser.json())

// 把路由容器挂载到 app 服务中
app.use(router)

app.listen(3000, function () {
  console.log('running 3000...')
})

module.exports = app
```

+ router.js： 路由模块

  + 职责：

    + 处理路由

    + 根据不同的请求方法+请求路径设置具体的请求处理函数

  + 模块职责要单一，不要乱写

  +  我们划分模块的目的就是为了增强项目代码的可维护性

  + 提升开发效率

```js
var fs = require('fs')
var Student = require('./student')

// Express 提供了一种更好的方式
// 专门用来包装路由的
var express = require('express')

// 1. 创建一个路由容器
var router = express.Router()

// 2. 把路由都挂载到 router 路由容器中

// 渲染学生列表页面
router.get('/students', function (req, res) {
  Student.find(function (err, students) {
    if (err) {
      return res.status(500).send('Server error.')
    }
    res.render('index.html', {
      fruits: [
        '苹果',
        '香蕉',
        '橘子'
      ],
      students: students
    })
  })
})
```

+ student.js
  + 数据操作文件模块
  + 职责：操作文件中的数据，只处理数据，不关心业务
  + 这里才是我们学习 Node 的精华部分：奥义之所在
  +  封装异步 API

```js
var fs = require('fs')
var dbPath = './db.json'
/**
 * 获取学生列表
 * @param  {Function} callback 回调函数
 */
exports.find = function (callback) {
  fs.readFile(dbPath, 'utf8', function (err, data) {
    if (err) {
      return callback(err)
    }
    callback(null, JSON.parse(data).students)
  })
}
```











### 其他知识点

#####  如何通过服务器让客户端重定向？

+ 状态码设置为 302 临时重定向    
  + statusCode
+ 在响应头中通过 Location 告诉客户端往哪儿重定向
  + setHeader
+ （如果客户端发现收到服务器的响应的状态码是 302 就会自动去响应头中找 Location ，然后对该地址发起新的请求// 所以你就能看到客户端自动跳转了）

```js
res.statusCode = 302
res.setHeader('Location', '/')
```

##### 301 和 302 状态码区别 

+ 301 永久重定向，浏览器会记住
+ 302 临时重定向
+ express重定向 direct

##### 遇到：Error: Cannot find module './lib/compat'

+ 可以把package-lock.json和node_modules都删掉，重新npm install

### mongoose 连接数据库 