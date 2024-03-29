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

![](D:\Notes\node\imgs\exports and module.exports.png)

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

## Express

### 创建基本服务器

```js
// 创建基本的Express服务器
// 导入express
const express = require('express')
// 创建web服务器
const app = express()
// 调用app.listen(端口号，成功回调)启动服务器
app.listen(80, () => {
	console.log('express server running')
})
```

**express监听get请求**

`app.get('url/:动态参数', function(req, res) {/*处理函数*/})`携带参数在`req.query`中，动态参数在`req.params`中

**express监听post请求**

`app.post('url/:动态参数',function(req, res) {/*处理函数*/})`动态参数在`req.params`中

**express把内容响应给客户端**

`res.send(返回数据)`

```js
app.get('/user',(req, res) => {
	res.send({name: 'zzz', age: 20, gender: '男'})
})
app.post('/user', (req, res) => {
	res.send('请求成功')
})
```

**托管静态资源**

`app.use('路径前缀', express.static('目录名'))`可将指定目录名创建静态资源服务器，存放静态资源的目录名不会出现在url中

多次调用会创建多个静态资源目录，根据调用`express.static()`的顺序查找资源 

```js
// 导入express
const express = require('express')
// 创建web服务器
const app = express()
// 将public文件夹作为静态资源目录
app.use(express.static('/public'))
```

### 路由

路由就是映射关系

在Express中路由就是客户端请求和服务器处理函数之间的映射关系

Express的路由由请求类型、请求URL地址和处理函数组成

```js
app.METHOD(PATH, HANDLER)
```

路由匹配过程：会按照路由定义的顺序去查找，只有在METHOD和PATH都对应上时才会去执行对应的处理函数

**路由模块化**

为了方便管理路由，Express不建议直接将路由挂载到app上，而是将路由抽离为单独的模块

步骤：

1. 创建路由模块对应的js文件
2. 调用`express.Router()`函数创建路由对象
3. 向路由对象是哪个挂在具体的路由
4. 使用`module.exports`向外共性路由对象
5. 使用`app.use('路由前缀', 路由对象)`函数注册路由模块

### 中间件

当一个请求到达Express服务器之后，可以连续调用多个中间件，从而对这次请求进行预处理

![](D:\Notes\node\imgs\middleware.png)*

中间件本质是一个function处理函数，形参列表中必须要有next参数

```js
// 路由处理函数
app.get('/',(req,res) => {})
// 中间件
app.get('/',(req,res,next) => {next()})
```

`app.use(中间件函数)`即可定义一个全局生效的中间件。**中间件注册必须在路由注册之前**

```js
const mw = function (req,res,next){
  console.log(111)
  next()
}
app.use(mw)
```

局部中间件是在路由的第二个参数中定义的

```js
const mw1 = function (req,res,next){
  console.log(111)
  next()
}
const mw2 = function (req,res,next){
  console.log(222)
  next()
}
app.get('/', [wm1, wm2], function(req,res){})
```

**中间件的分类**

应用级别的中间件，即绑定到app实例上的中间件

```js
// 应用级别全局中间件
app.use((req,res,next)=>{next()})
// 应用级别局部中间件
app.get('/', mw, (req,res) => {})
```

路由级别的中间件，即绑定到路由对象上的中间件。用法和应用级别的一样

```js
const router = express.Router()
router.use(function(req,res,next){
	next()
})
```

错误级别的中间件，用来捕获整个项目中发生的错误，中间件函数有4个参数，分别是err、req、res和next

```js
app.get('/', function(req,res){
	throw new Error('服务器发生了错误')
	res.send('Home Page')
})
app.use(function(err, req, res, next) {
	console.log('发生了错误'+err.message)
	res.send('Error'+err.message)
})
```

内置中间件

- `express.static`
- `express.json`解析JSON格式的请求体数据,必须在注册路由中间件之前注册
- `express.unlencoded`解析URL-encoded格式的请求体数据，必须在注册路由中间件之前注册

```js
// 解析配置application/json格式数据的内置中间件
app.use(express.json())
// 配置解析application/x-www-form-urlencoded格式数据的内置中间件
app。use(express.urlencoded({extended: false}))
```

第三方中间件

```js
const bodyParser = require('body-parser')
app.use(bodyParser)
```

**解决跨域问题**

1. CORS
2. JSONP（只支持GET请求）

```js
// CORS
// 1. 运行npm i cors安装中间件
// 2. 使用const cors = require('cors')导入中间件
// 3. 在路由之前调用app.use(cors())配置中间件
const cors = require('cors')
app.use(cors())
```

CORS响应头

Access-Control-Allow-Origin: 具体域名 | *

```js
res.setHeader('Access-Control-Allow-Origin', '*')
```

Access-Control-Allow-Headers: 默认情况下CORS只支持客户端向服务器发送9个请求头条Accept、Accept-Language、Content-Language、DPR、Downlink、Save-Data、Viewport-Width、Width、Content-Type（值仅限于text/plain、multipart/form-data、application/x-www-form-urlencoded三者之一）。如果超过这9个则需要在Access-Control-Allow-Headers中配置额外的请求头

```js
res.setHeader('Access-Control-Allow-Headers', 'Content-Type', 'X-Custom-Type')
```

Access-Control-Allow-Methods: 默认情况下CORS只支持客户端发起GET、POST、HEAD请求，如果客户端希望通过PUT、DELETE等方式发送请求，则需要Access-Control-Allow-Methods配置

```js
res.setHeaders('Access-Control-Allow-Methods', 'POST, GET, PUT, HEAD')
res.setHeaders('Access-Control-Allow-Methods', '*')
```

跨域请求分类：

- 简单请求：没有设置Access-Control-Allow-Methods和Access-Control-Allow-Heads
- 预检请求：设置了Access-Control-Allow-Methods或Access-Control-Allow-Heads，或者向服务器发送了application/json格式的数据

在浏览器和服务器正式通信之前，浏览器会先发送OPTION请求进行预检，以获知服务器是否允许该实际请求，这次OPTION请求成为“预检请求”。服务器响应了预检请求才会发送真正的请求

JSONP

JSONP是通过script的src属性请求服务器上的数据，它不是一个Ajax请求，只支持GET请求。

如果项目已配置CORS，则需要在配置CORS中间件之前声明JSONP接口，否则JSONP接口会被处理成CORS接口

```js
// 实现步骤
// 1. 获取客户端发送过来的回调函数名字
// 2. 得到想通过JSONP形式发送给客户端的数据
// 3. 根据前两步得到的数据，拼接成一个函数调用的字符串
// 4. 把上一步拼接得到的字符串，响应给<script>标签解析执行
app.get('/api/jsonp',(req,res)=>{
    const funcName = req.query.callback
    const data = {name: 'zzz'm age: 22}
    const scriptStr = `${funcName}(${JSON.stringify(data)})`
    res.send(scriptStr)
})
```

## 数据库

传统型数据库（关系型数据库）的组织结构分为数据库（database）、数据表（table）、数据行（row）、字段（field）这4部分组成

**字段特殊标识**

1. PK（Primary Key)主键、唯一标识
2. NN（Not Null）值不允许为空
3. UQ（Unique）值唯一
4. AI（Auto Increment）值自动增长

### SQL语法

注释：`--`用两个短横线定义

#### SELECT语句

```sql
-- 从FROM指定的表中，查询出所有的数据
SELECT * FROM 表名称
-- 从FROM指定的表中，查询出指定列名称的数据
SELECT 列名称[, 列名称] FROM 表名称
```

#### INSERT INTO语句

```sql
-- 向指定的表中，插入如下几列数据，列的值通过values一一指定
INSERT INTO table_name(列1, 列2, ...) VALUES(值1, 值2, ...)
```

#### UPDATE语句

```sql
-- 用UPDATE指定要更新那个表中的数据
-- 用SET指定列对应的新值
-- 用WHERE指定更新的条件
UPDATE 表名称 SET 列名称 = 新值[, 列名称 = 新值] WHERE 列名称 = 某值
```

#### DELETE语句

```sql
-- 从指定的表中，根据WHERE条件，删除对应的数据行
DELETE FROM 表名称 WHERE 列名称 = 值
```

#### WHERE子句

WHERE子句用于限定选择的标准，在SELECT、UPDATE、DELETE语句中都可以用WHERE子句来限定选择标准

```SQL
SELECT 列名称 FROM 表名称 WHERE 列 运算符 值
UPDATE 表名称 SET 列 = 新值 WHERE 列 运算符 值
DELETE FROM 表名称 WHERE 列 运算符 值
```

#### AND和OR运算符

AND和OR可在WHERE子句中把多个条件结合起来

AND表示必须同时满足多个条件，相当于&&运算符

OR表示满足一个条件即可，相当于||运算符

```SQL
SELECT * FROM users WHERE id < 3 AND status = 0
SELECT * FROM users WHERE status = 1 OR username = 'zcw'
```

#### ORDER BY子句

ORDER BY语句用于排序

ORDER BY语句默认按照升序排列，即ASC，如果希望降序可使用DESC关键字

```sql
SELECT * FROM users ORDER BY status
-- 下面这条语句等价于上面这条
SELECT * FROM users ORDER BY status ASC 

SELECT * FROM users ORDER BY status DESC
-- 多重排序，先按照status降序再按照username升序
SELECT * FROM users ORDER BY status DESC, username ASC
```

#### COUNT(*)函数

COUNT(*)函数用于返回查询结果的总数据条数

```sql
SELECT COUNT(*) FROM users WHERE status = 0
```

#### AS关键字

AS关键字用于给查询出来的列名称起别名

```sql
SELECT COUNT(*) AS total FROM users WHERE status = 0
SELECT username AS name,password AS pw FROM users WHERE status = 0
```

## mysql模块

```js
const mysql = require('mysql')
// 建立与MySQL数据库的连接
const db = mysql.createPool({
	host: '127.0.0.1', // 数据库的IP地址
	user: 'root', // 登录数据库的账号
	password: 'q7240insbwgia', // 登录数据库的密码
	database: 'my_db_01' // 指定要操作哪个数据库
})
```

**查询**数据

```js
db.query('sql语句', (err, results) => {
	if (err) return console.log(err.message)
    // 如果执行的sql语句是select则结果是一个Array
	console.log(results)
})
```

**插入数据**

```JS
const user = {username: 'spider-man', password: 'pcc321'}
// 待执行的sql语句，英文的?表示占位符
const sqlStr = 'INSERT INTO users (username, password) VALUES (?, ?)'
// 使用数组的形式，依次为?占位符指定具体的值
db.query(sqlStr, [user.username, user.password], (err, results) => {
    if(err) return console.log(err.message)
    // results 数据结构
    // OkPacket {
    //     fieldCount: 0,
    //     affectedRows: 1,
    //     insertId: 8,
    //     serverStatus: 2,
    //     warningCount: 0,
    //     message: '',
    //     protocol41: true,
    //     changedRows: 0
    // }
    if (result.affectedRows === 1) {
        console.log('数据插入成功')
    }
})
```

如果数据表中要插入的字段很多，像上面那种写法一个个对应插入很麻烦，可以用以下简写

```js
const user = {username: 'spider-man', password: 'pcc321'}
// 待执行的sql语句，英文的?表示占位符
const sqlStr = 'INSERT INTO users SET ?'
db.query(sqlStr, user, (err, results) => {
    if(err) return console.log(err.message)
    if (result.affectedRows === 1) {
        console.log('数据插入成功')
    }
})
```

**更新数据**

```js
const user = {id: 8, username: 'iron-man1', password: 'avalonzzz'}
const sqlStr = 'UPDATE users SET username=?,password=? WHERE id=?'
db.query(sqlStr, [user.username, user.password, user.id], (err, results) => {
    if(err) return console.log(err.message)
    if(results.affectedRows === 1){
        console.log(results, '数据库更新成功')
    }
})
```

简写形式

```js
const user = {id: 8, username: 'iron-man2', password: 'avalonzzzz'}
const sqlStr = 'UPDATE users SET ? WHERE id = ?'
db.query(sqlStr, [user, user.id], (err, results) => {
    if(err) return console.log(err.message)
    if(results.affectedRows === 1){
        console.log(results, '数据库更新成功')
    }
})
```

## Session原理

http请求是无状态的，可用cookie突破这个限制

cookie是存储在浏览器中不超过4kb的字符串，不同域名下的cookie各自独立。客户端发送请求时会把当前域名下的未过期的cookie都发送给服务器

因为cookie不具备安全性，所以需要采用服务端session认证的操作

![](D:\Notes\node\imgs\session.png)

```js
// 使用express-session npm i express-session
const express = require('express')
const session = require('express-session')
const app = express()
app.use(session({
  secret: 'keyboard cat',
  resave: false,
  saveUninitialized: true
}))
```

将express-session配置成功后，可通过req.session访问和使用session

```js
app.post('/api/login', (req, res) => {
	if(req.body.username !== 'admin' || req.body.password !== '000000'){
		return res.send({status: 1, msg: '登录失败'})
	}
	req.session.user = req.body // 将用户的信息存储到session中
    req.session.isLogin = true // 将用户的登录状态存储到session中
    res.send({status: 0, msg: '登录成功'})
})
```

获取session

```js
app.get('/api/username', (req,res) => {
	if(req.session.iislogin) {
		return res.send({status: 1, msg: 'fail'})
	}
    res.send({
        status: 0,
        msg: 'success',
        username: req.session.user.username
    })
})
```

清空session（当用户登出的时候）

```js
app.post('/api/logout', (req, res) => {
	req.session.destroy()
	res.send({
		status: 0,
		msg: '退出登录成功'
	})
})
```

## JWT

session认证机制需要cookie配置，cookie不支持跨域，需要配置很多才能实现，所以麻烦。

JWT（JSON Web Token）是目前最流行的跨域认证解决方案

![](D:\Notes\node\imgs\JWT.png)

JWT有Header（头部）、Payload（有效荷载）、Signature（签名）组成。三者之间用英文的`.`分隔，Payload才是用户的真正信息

JWT使用方式：在服务端返回JWT之后，将其存在localStroage或sessionStorage中，请求的时候将其放在HTTP的Authorization字段中

```http
Authorization: Bearer <token>
```

**安装JWT**

jsonwebtoken：用于生成JWT字符串

express-jwt：用户将JWT字符串解析还原成JSON对象

```bash
npm i jsonwebtoken express-jwt
```

在使用这两个包时，需要顶一个secret秘钥，用于加密和解密JWT字符串

```js
const express = require('express')
const jwt = require('jsonwebtoken')
const expressJWT = require('express-jwt')
const app = express()
const secretKey = 'AvalonZzz'
// 登录接口
app.post('/api/login', (req, res) => {
    // ... 判断用户认证是否正确
    res.send({
        status: 200,
        message: '登录成功',
        // 调用jwt.sign()生成JWT字符串，三个参数分别是用户信息、加密秘钥、配置对象
        token: jwt.sign({username: userinfo.username}, secretKey, {expiresIn: '30s'})
    })
})
// 使用app.user()来注册中间件
// expressJWT({secret: secretKey}) 配置解析Token的中间件
// .unless({path: [/^\/api//]}) 用来指定哪些接口不需要访问权限
app.use(expressJWT({secret: secretKey}).unless({path: [/^\/api//]}))
app.get('/admin/getinfo', (req,res) => {
    console.log(req.user) // 可通过req.user对象来访问JWT解析出来的用户信息了
    res.send({
        status: 200,
        message: '请求成功',
        data: req.user
    })
})
```

捕获解析JWT失败产生的错误

```js
app.use((err, req, res, next) => {
	if(err.name === 'UnauthorizedError') {
		return res.send({status: 401, message: '无效的Token'})
	}
	res.send({status: 500, message: '未知错误'})
})
```

