## Node.js安装配置

下载稳定版本的包，安装

```bash
path # 通过path命令查看PATH环境变量中是否有node的路径
node -v # 查看node的版本号
```

## NPM

`npm -v`：查看npm版本号

`sudo npm install npm -g`：如果安装了旧版本的npm，可用来更新

`npm install -g cnpm --registry=https://registry.npmmirror.com`：下载淘宝镜像

`npm list -g`：查看所有全局安装的模块

`npm list 模块名`：查看某个模块的版本号

`npm uninstall 模块名`：卸载模块

`npm update 模块名`：更新模块

### 全局安装和本地安装

```bash
npm install express      # 本地安装
npm install express -g   # 全局安装
```

**本地安装**

- 安装包在./node_modules下，如果没有node_modules目录，会在当前执行npm命令的目录下生成node_modules目录
- 可以通过`require()`来引入本地安装的包

**全局安装**

- 将安装包放在/usr/local下或者你node的安装目录
- 可以在命令行里使用

### 版本号

npm语义版本号分为X.Y.Z三位，分别代表主版本号、次版本号和补丁版本号

- 如果只是修复bug，需要更新Z位
- 如果新增了功能，但向下兼容，需要更新Y位
- 如果有大变动，向下不兼容，需要更新X位

### 使用nvm

nvm:nodejs版本管理工具,可切换多个nodejs版本

```bash
nvm list 查看当前所有的node版本
nvm install v版本号 安装指定的版本
nvm use --delete-prefix 版本号 切换到指定版本
```

### http请求

- DNS解析，建立TCP三次握手连接，发送http请求
- server接收到http请求，处理并返回
- 客户端接收到返回数据，处理数据

三次握手：

- 第一次是客户端询问服务器你是否可以用
- 第二次是服务器告诉客户端我可以使用
- 第三次是客户端告诉服务器我接下来要使用了

```js
const http = require('http')
const querystring = require('querystring')
const server = http.createServer((req,res) => {
    // if(req.method === 'POST') {
    //     console.log('content-type', req.headers['content-type'])
    //     let postData = ''
    //     req.on('data', chunk => {
    //         postData += chunk.toString()
    //     })
    //     req.on('end', () => {
    //         console.log(postData)
    //         res.end('hello world')
    //     })
    // }
    const method = req.method
    const url = req.url
    const path = url.split('?')[0]
    const query = querystring.parse(url.split('?')[1])
    // 设置返回格式为JSON
    res.setHeader('Content-type', 'application/json')
    // 定义返回数据
    const resData = {
        method,
        url,
        path,
        query,
    }
    if(method === 'GET') {
        res.end(JSON.stringify(resData))
    }
    if(method === 'POST') {
        let postData = ''
        req.on('data', chunk => {
            postData += chunk.toString()
        })
        req.on('end', () => {
            resData.postData = postData
            res.end(JSON.stringify(resData))
        })
    }
})
server.listen(8000)
```

## Node.js REPL(交互式解释器)

node交互式解释器，可以执行以下任务：

- 读取：读取用户输入，解析输入的javascript数据结构并存在内存中
- 执行：执行输入的数据结构
- 打印：输出结果
- 循环：循环操作以上步骤直到用户两次按下ctrl-c键退出

Node REPL支持多行输入，Node会自动监测是否为连续的表达式

可用下划线_来获取续航一个表达式的运算结果

**REPL命令**

- ctrl-c：退出当前终端
- ctrl-c按下两次：退出Node REPL
- ctrl-d：退出NodeREPL
- 向上/向下键：查看输入的历史命令
- tab键：列出当前命令
- .help：理出使用命令
- .break：退出多行表达式
- .clear：退出多行表达式
- .save filename：保存当前的Node REPL绘画到指定文件
- .load filename：载入当前Node REPL会话的文件内容

## Node.js回调函数

Node.js异步编程依托于回调来实现，但不能说使用了回调程序就异步化了

### 阻塞代码实例

```js
let fs = require('fs')
let data = fs.readFileSync('input.txt')
console.log(data.toString())
console.log('程序执行结束')
// 菜鸟教程官网地址：www.runoob.com
// 程序执行结束!
```

### 非阻塞代码实例

```js
var fs = require("fs");

fs.readFile('input.txt', function (err, data) {
    if (err) return console.error(err);
    console.log(data.toString());
});

console.log("程序执行结束!");

// 程序执行结束!
// 菜鸟教程官网地址：www.runoob.com
```

## Node.js事件循环

Node.js基本上所有的事件机制都是用观察者模式实现的。

Node.js 单线程类似进入一个while(true)的事件循环，直到没有事件观察者退出，每个异步事件都生成一个事件观察者，如果有事件发生就调用该回调函数.

```js
let events = require('events')
// 创建eventEmitter对象
let eventEmitter = new events.EventEmitter()
// 绑定事件及其处理程序
eventEmitter.on('eventName', eventHandler)
// 触发事件
eventEmitter.emit('eventName')
```

## fs文件系统模块

`fs.readFile(path,[options],callback)`

```js
const fs = require('fs')
fs.readFile('./11.txt', 'utf8', function(err,data){
	console.log(err)
	console.log(data)
})
```

`fs.writeFile(path,[options],callback)`

```js
const fs = require('fs')
fs.writeFile('./1.txt', 'hello Node.js', function(err){
    console.log(err)
})
```

## path路径模块

`path.join([...paths])`将多个参数合并为一个路径

```js
const path = require('path')
console.log(path.join('/a', '/b/c', '../', '/d')) // \a\b\d

console.log(path.join(__dirname, '/demo/1.txt')) //F:\node\path\demo\1.txt
```

`path.basename(path[, ext])`获取路径中的最后一部分

```js
const fpath = '/a/b/c/index.html'
let fullName = path.basename(fpath)
console.log(fullName) // index.html
let nameWithoutExt = path.basename(fpath, '.html')
console.log(nameWithoutExt) // index
```

`path.extname(path)`获取文件的扩展名

```js
const filepath = '/a/b/c/index.js'
const fext = path.extname(filepath)
console.log(fext) // .js
```

