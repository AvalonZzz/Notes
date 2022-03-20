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

## Http模块

1. 引入http模块
2. 调用`http.createServer()`创建服务器实例
3. 为服务器实例绑定request事件（只要有客户端请求服务器就会触发request事件）
4. 在服务器实例上调用`listen(端口号)`方法来启动服务器

```js
const http = require('http')
const server = http.createServer()
server.on('request', function(req, res) {
  console.log('client requested')
})
server.listen(80, () => {
    console.log('server running')
})
```

req请求对象包含了与客户端相关的数据和属性

```js
server.on('request', function(req){
	console.log(req.url, req.method)
})
```

res响应对象包含了与服务器相关的数据和属性

```js
server.on('request', function(req, res) {
  const {url, method} = req
  res.setHeader('Content-Type', 'text/html;charset=utf-8') // 防止发送给客户端的内容乱码
  res.end(`request end, request url is ${url}, and request method is ${method}`)
})
```

## Node模块化

模块:

- 内置模块（只需要写名字）
- 自定义模块（需要写路径）
- 第三方模块（只需要写名字）

模块具有模块作用域，内部变量在没有导出的情况下只能在模块作用域中使用。

`require（）`方法加载其他模块的一瞬间，会执行模块中的代码。require引入的是`module.exports`导出的对象

`module.exports`可以向外共享成员，默认情况下exports指向`module.exports`。如果给`module.exports`或exports赋值一个新对象，那么exports和`module.exports`就不是指向同一个对象了

![](F:\Notes\node\imgs\exports and module.exports.png)

**CommonJS规范**

1. 每个模块内部，module对象代表当前模块
2. module变量是一个对象，它的exports属性是对外的接口
3. 加载某个模块就是加载该模块的`module.exports`属性

**模块加载机制**

1. 模块在第一次加载后被缓存，后面多次require不会重复执行代码
2. *内置模块*加载优先级最高（require fs模块，优先加载内置fs模块，而不是第三方fs模块）
3. 加载*自定义同名模块*不写扩展名，优先从js->json->node扩展名加载，都没有则加载失败
4. 加载*第三方模块*会从`/node_modules`下加载第三方模块，没有则往上层查找，直到文件系统根目录
5. *目录作为模块*
   1. 在被加载的目录下查找`package.json`文件并寻找main属性作为`require（）`的入口
   2. 在目录中没有`package.json`或main入口不存在，则node试图加载目录下的`index.js`
   3. 以上两步都失败了则报`Error:cannot find module xxx`

## 包与npm

包的分类：

- 项目包（安装到node_modules目录中）
  - 开发依赖包（记录到devDependencies节点中的包）
  - 核心依赖包（记录到dependencies节点中的包）
- 全局包

包的结构规范：

- 包必须以单独目录存在
- 包的顶级目录下必须包含`package.json`文件，且必须要有name、main、version这三个属性

包版本`大版本.功能版本.bug修复版本`。

`npm i 包全名@指定版本 -S/-D`下载第三方包。

`npm uninstall 包全名`卸载第三方包

`npm init -y`快速初始化项目并创建`pageage.json`文件

`npm config get registry`获取下包的服务器地址

`npm config set registry=https://registry.npm.taobao.org/` 切换淘宝镜像源

**nrm镜像源管理工具**

```bash
npm i nrm -g // 全局下载nrm，存放在C:\Users\用户名\AppData\Roaming\npm\node_modules
nrm ls // 查看nrm所有可用的镜像源
nrm use taobao // 选择使用淘宝镜像
```

```json
{
  "name": "utils_zzz", // 包的名称，不可重复
  "version": "1.0.0", // 包的版本号
  "description": "提供常用工具类", // 包的描述信息
  "main": "index.js", // 包的入口文件
  "scripts": { 
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": [], // 搜索关键字
  "author": "", // 作者
  "license": "ISC" // 开源许可协议
}
```

**发布npm包**

1. `npm login`登录npm
2. `npm publish`发布npm包
3. `npm unpublish 包名 --force`删除已发布的包（只能删除72小时内的包，删除过的包24小时内不能重复发布）
