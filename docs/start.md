## 知识储备

- HTML
- CSS
- JavaScript
- 掌握基本的命令行操作
- 具有服务端开发经验更佳



## 安装环境

- 下载：<https://nodejs.org/en/download/> 
- 安装：下一步...
- 检验：`node --version `，能够显示版本号证明ok



## REPL环境

> 类似于浏览器中的 Console ，可以做一些代码测试。 

- read
- eval
- print
- loop



## Hello World

- 新建hello.js文件

```javascript
var message = 'Hello Node.js!'
console.log(message)
```

- 切换到hello.js文件所在目录执行

```javascript
node hello.js
```



## 操作文件

读取文件：

```javascript
const fs = require('fs')

fs.readFile('/etc/passwd', (err, data) => {
  if (err) throw err;
  console.log(data);
});
```

写入文件：

```javascript
const fs = require('fs')

fs.writeFile('message.txt', 'Hello Node.js', (err) => {
  if (err) throw err;
  console.log('The file has been saved!');
});
```



## HTTP服务

```javascript
// 使用 Node 非常轻松的构建一个 Web 服务器
// 在 Node 中专门提供了一个核心模块：http
// http 这个模块的职责就是帮你创建编写服务器的

// 1. 加载 http 核心模块
var http = require('http')

// 2. 使用 http.createServer() 方法创建一个 Web 服务器
//    返回一个 Server 实例
var server = http.createServer()

// 3. 服务器要干嘛？
//    提供服务：对 数据的服务
//    发请求
//    接收请求
//    处理请求
//    给个反馈（发送响应）
//    注册 request 请求事件
//    当客户端请求过来，就会自动触发服务器的 request 请求事件，然后执行第二个参数：回调处理函数
server.on('request', function () {
  res.end('Hello Node.js!')
})

// 4. 绑定端口号，启动服务器
server.listen(3000, function () {
  console.log('服务器启动成功，请求访问 http://127.0.0.1:3000/')
})
```

















