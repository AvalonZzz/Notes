## 

## 开始



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

## mysql



```sql
use 库名;  // 选中某个数据库
-- sql语句 // 注释
// 增
insert into users(username,`password`,realname) values('zhangsan','123','张三'); // 插入语句

// 查
select * from users;
select id,username from users; // 只会查询id和username两列
select * from users where username='zhangsan' and `password`='123' // 只会查询username为zhangsan并且password为123的那一条数据
select * from users where username='zhangsan' or `password`='123' // 只会查询username为zhangsan或者password为123的那一条数据
select * from users where username like '%zhang%' // 只会查询username中有zhang的那些数据
select * from users where password like '%1%' order by id desc; // 查出password中有1的结果并且以id倒序排列，不加desc则正序排列

// 改
SET SQL_SAFE_UPDATES=0; // 开启sql安全更新
update users set realname='张三2' where username='zhangsan'

// 删
delete from users where username='zhangsan'
```

