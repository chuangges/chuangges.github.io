---
title: Node.js 主要内容
tags:
  - NodeJS
categories: Node.js
top: false
keywords:
  - node
date: 2019-04-16 00:49:49
description: 主要特点、运行机制、安装配置、核心模块
---

# 一、NodeJS
> 用于创建高性能的 Web 服务器

  <div style="text-indent: 2em">脚本语言都需要一个解析器才能运行，浏览器是html中Js代码的解析器。NodeJS 则是 一个独立运行的 js 代码解析器，它封装了 V8 引擎并引入了 commonjs 规范，分别解决了js比较慢和乱的问题，填补了 js 在服务器端的空白。</div>
  <div style="text-indent: 2em">每一种解析器都是一个运行环境，不但允许 JS 定义各种数据结构并进行各种计算，还允许JS使用运行环境提供的内置对象和方法做一些事情。例如运行在浏览器中的 JS 用于操作 DOM，浏览器就提供了 document 等内置对象。而运行在 NodeJS 中的 JS 用于操作磁盘文件或搭建 HTTP 服务器，NodeJS 就相应提供了 fs、http 等内置对象。</div>


## 特点
  * 轻量高效 
  * 事件驱动模型
  * 异步非阻塞 I/O 模型
  * 单进程单线程应用程序


## 不足
  * 不适合处理复杂逻辑
  * 不适合计算密集型应用
  * 不适合单用户多任务的程序
  * 代码某个环节报错则整个系统崩溃
  * 只支持单核 CPU 而不能充分利用多核 CPU 服务器


## 运行机制

### 重点注意
  * 同步异步：操作任务是否按顺序依次执行
  * 阻塞非阻塞：IO 操作是否影响主进城执行其它代码
  * NodeJs 内部通过线程池来完成异步操作，libuv 针对不同平台的差异性实现了统一调用，所以 NodeJS 程序的单线程仅仅指 Js 运行在单线程，程序本身并非单线程
  * libuv 默认创建一个由四个线程组成的线程池来加载异步操作，但如今的操作系统已经对很多 I/O 任务提供了异步接口，所以只有不存在其他方式时，异步 I/O 才会使用线程池


### 异步非阻塞
> IO 操作的核心是通过事件机制实现异步执行

  <div style="text-indent: 2em;">采用了单线程模型，它使用一个主线程处理所有的请求，当接收到操作请求后就作为一个事件放入任务队列中，然后继续接收其他请求，如果有 I/O 操作（比较耗时）就交给 libuv 的工作线程去执行，所以主线程速度快而且不会阻塞，这就是 `单线程、异步 I/O`。将所有操作都注册为一个事件并等待触发的方式就是 `事件驱动`。</div>

  <div style="text-indent: 2em; margin-top: 15px;">如果 I/O 操作由于复杂的逻辑处理而发生了阻塞，但返回的结果是暂时在事件队列中等待，由事件循环自动依次取回到主程序中恢复执行，而事件队列在主程序之外存储事件，所以不会干扰主程序执行。这就是 `非阻塞 I/O`。</div>

  
### 事件循环
  <div style="text-indent: 2em; margin-top: 15px;">当主线程没有请求接入时，就开始通过事件循环机制查看任务队列来检查队列中是否有要处理的事件，如果是 I/O 任务就交给 libuv 的工作线程去执行并指定回调函数，否则就直接处理并返回结果给上层。当工作线程中的 I/O 任务完成后执行指定的回调函数，把这个完成的事件放到事件队列中等待事件循环时处理。这就是 `事件循环`。</div>


  1. V8 引擎解析 JavaScript 脚本
  2. 解析后的代码，调用 Node API
  3. libuv 库负责 Node API 的执行。它将不同的任务分配给不同的线程，形成一个 Event Loop (事件循环），以异步的方式将任务的执行结果返回给 V8 引擎
  4. V8 引擎再将结果返回给用户
    * 除了 setTimeout、setInterval，NodeJs还提供了：process.nextTick、setImmediate
    * process.nextTick 可以在当前“执行栈”尾部、下一次 EventLoop（主线程读取“任务队列”）之前，触发回调函数。即它指定的任务总是发生在所有异步任务之前
    * setImmediate 则是在当前"任务队列"的尾部添加事件，它指定的任务总是在下一次 Event Loop 时执行
  


## 功能架构    
  <div align="center"> 
    ![Node Framework](/images/nodejs/node-framework.png) 
  </div> 
            
  * __顶部__：Js 编写的应用程序和模块
  * __中间__：通过代码包装和暴露接口而实现 js 可以调用 c++ 代码
    * __Bindings__：绑定核心库的依赖
    * __Addons__：绑定第三方或者自定义 C/C++
  * __底部__：C/C++ 编写的内部组件，提供了系统底层操作方法
    * __V8__：Google 开源 js 引擎，用于将 Js 代码编译为机器码执行以便快速编译
    * __libuv__：跨平台的底层封装，实现事件循环、文件操作等异步功能。
            

## 适用场景
  <div style="text-indent: 2em">NodeJS 处理并发的能力较强，但处理计算和逻辑的能力很弱，因此适用于 高并发、I/O 密集、少量业务逻辑 的场景，比如 RESTFUL API、实时聊天、客户端逻辑强大的单页 APP。一般由前端处理复杂逻辑，NodeJS 只提供异步 I/O，这样可以实现对高并发的高性能处理。</div>

              
# 二、安装配置
> window 系统

## 安装
> 本质是把 NodeJS 执行程序复制到一个目录，然后保证这个目录在系统 PATH 环境变量下，以便终端下可以使用 node 命令

  * 版本：Windows Installer(.msi) 64位
  * 地址：建议不要安装在 C 盘而修改默认地址为 "D:\nodejs\"
  * 命令
    * 查看版本：`node -v`
    * 交互模式：`node`
    * 执行文件：`node hello.js`


## 配置

### 包文件
> 统一管理通过 npm 全局安装的第三方包及其缓存

  1. 新建文件夹：`node_global、node_cache`
  2. 执行命令
    * `npm config set cache "D:\nodejs\node_cache"`
    * `npm config set prefix "D:\nodejs\node_global"`
  3. 修改文件：node_modules\npm\npmrc
    * `prefix = D:\nodejs\node_global`
    * `cache = D:\nodejs\node_global`


### 环境变量
> 全局使用 npm、cnpm 等命令

  <div style="text-indent: 2em;">path 指系统默认指定到某一路径，运行命令时会先从这些路径中开始找。另一种方法是在系统变量中设置 NODE_PATH = 安装的根目录，NodeJS 允许通过变量 NODE_PATH 指定额外的模块搜索路径。所以配置方法如下：</div>

  * 用户变量 PATH 末尾添加 `";D:\nodejs\node_global"`
  * 系统变量新增 `NODE_PATH: "D:\nodejs\node_global\node_modules"`


## 安装 cnpm
> npm 服务器在国外而安装速度慢，所以大多都使用淘宝对 npm 的镜像服务器 cnpm，它和 npm 命令相同

  * 安装：`npm install cnpm -g --registry=https://registry.npm.taobao.org`  
  * 查看：`cnpm -v`
  * 安装依赖：`cnpm install`
  * 安装失败的解决方案：`npm config set registry https://registry.npm.taobao.org`

                  
## 安装 nodemon         
> 编写调试 Node 项目时，每次修改代码后都需要重新启动，比较麻烦。nodemon 工具可以监听代码文件的变化，并自动重启 Node 服务器和数据库服务器

  ```js
  // 安装
  cnpm install -g  nodemon

  // app.js
  var http = require('http')
  http.createServer((req, res) => {
      res.end('hello node');
  }).listen(3000);

  // 启动
  nodemon app
  ```


# 三、模块管理

## 模块机制
  * 根据模块(文件) 划分功能、组织和复用代码
  * 通过 npm 工具和网站进行开源社区代码共享
  * 通过包规范管理应用和可复用组件


## 模块模型
  * __核心模块__：NodeJS 提供，会被编译到二进制执行文件（加载速度快）
    * __C/C++ 内建模块__：存放src目录下的最底层模块，会提供js调用的API
    * __Js 核心模块__：存放在lib目录下，为C/C++内建模块提供封装和桥接
  * __文件模块__：用户编写，动态加载（加载速度较慢）
    * __C++ 扩展模块__：用于提高运行效率等自定义编写的底层模块
    * __Js 文件模块__：由第三方或用户自行编写的模块


## 模块缓存
  * 模块会在第一次 require 后被缓存，`多次 require 不会导致模块的代码被执行多次`
  * Node 模块的缓存不同于浏览器的 js 缓存，浏览器只缓存文件，`Node 模块缓存的是编译和执行之后的对象`
  * require.cache 对象代表 Node 模块缓存区，缓存的模块以属性的方式加入该对象，可以通过 `require.cache['模块标识符'] 来访问具体的缓存模块`，也可以 delete 该缓存


## 模块变量

### 全局对象
  * __global__：表示Node所在的全局环境，类似于浏览器中的window对象
  * __process__：指向Node内置的process模块，允许开发者与当前进程互动
  * __console__：指向Node内置的console模块，提供命令行环境中的标准输入、标准输出功能。


### 全局函数  
  * __模块加载函数__：require
  * __定时器函数__：setTimeout、setInterval、clearTimeout、clearInterval


### 全局变量
  * ___filename__：指向当前运行脚本的文件名
  * ___dirname__：指向当前运行脚本所在的目录


### 模块变量
  * __module__：表示当前模块的对象
  * __module.exports__：模块的导出对象
  * __module.filename__：模块解析后的文件名
  * __exports__：指向 module.exports 对象的变量


## 模块包规范
> NodeJS 采用包来对一组具有相互依赖关系的模块进行统一管理，封装为独立的复用组件或单个应用。Node的包通常为一个目录，主要包括如下内容：

  * __package.json__：包的描述文件
  * __bin__：可执行文件、二进制文件
  * __lib__：待加载的 js 文件
  * __doc__：使用包的说明文档
  * __test__：单元测试用例代码文件
  * __node_modules__：本地安装包


# 四、核心模块

## HTTP
> 处理客户端的网络请求

### 作为客户端
  * __client__：连接服务器
    * 发起请求：`http.request(options, fn)`
    * 简化版，不用 end：`http.get(options, fn)`
  * __ClientRequest__：处理要发送的请求信息
    * 收到请求：`req.on("response", fn)`
    * 收到错误：`req.on("error", fn)`
    * 写入数据：`req.write`
    * 完成发送：`req.end`
  * __ClientResponse__：获取服务端返回信息
    * 传输数据：`res.on("data", fn)`
    * 传输结束：`res.on("end", fn)`
    * 请求关闭：`res.on("close", fn)`
    * 设置存储：`res.setEncoding`
    * 暂停请求：`res.pause`
    * 恢复请求：`res.resume`
    * 状态码：`res.statusCode`
    * 请求头：`res.headers`


### 作为服务端
  * __Server__：创建服务器
    * 生成实例：`Server()`
      * 接收请求：`on("request", fn)`
      * 关闭请求：`on("close", fn)`
      * 监听服务：`listen(port, fn)`
      * 新建 TCP 流：`on("connection", fn)`
    * 直接创建：`createServer(fn).listen(port)`
  * __IncomingMessage__：获取客户端请求信息
    * 传输数据：`on("data", fn)`
    * 传输结束：`on("end", fn)`
    * 请求关闭：`on("close", fn)`
    * 请求方法：`method`
    * 请求地址：`url`
  * __ServerResponse__：处理返回给客户端的信息res
    * 设置头信息：`setHead`
    * 合并头信息：`writeHead`
    * 写入数据：`write`
    * 结束传输：`end`

```js
var http = require('http')
var url = require('url')

http.createServer((req,res)=>{
    // 将传过来的URL转变为对象
    var params = url.parse(req.url,true)
    res.write(params.query.name)
    res.end()
    // res.end(params.query.name);
}).listen(3000);

// 客户端请求
var request = http.get({
    host:'localhost',
    path:'/user?name=tom&age=22',
    port:3000},function(res){
    res.setEncoding('utf-8')
    res.on('data',function(data){
        console.log('res_date：'+data)
    })
})
```
        

## URL
> 处理客户端请求过来的URL

  * __parse__：将 url 字符串地址转为一个对象
  * __format__：将 url 对象转为一个 url 字符串
  * __resolve__：对传入的两个参数用 `/` 拼接


  ```js
  const url = require("url")

  url.parse("http://user:pass@host.com:8080/p/a/t/h?query=string#hash")
  url.parse("http://user:pass@host.com:8080/p/a/t/h?query=string#hash", true)

  url.format({
      protocol: "http:",
      host: "182.163.0:60",
      port: "60"
  })
  url.resolve("http://whitemu.com", "gulu")
  ```


## Querystring
> 处理客户端 get/post 请求传递的参数

  * __parse__：将一个字符串反序列化为一个对象
  * __stringify__：将一个对象序列化成一个字符串
  * __escape__：使传入的字符串进行编码
  * __unescape__：对使用了 escape 编码的字符进行解码


  ```js
  var querystring = require('querystring')
  
  querystring.parse("name=tom&sex=man&sex=women")
  querystring.stringify({ name: 'tom', sex: ['man', 'women']})

  querystring.escape("name=慕白")
  querystring.unescape('name%3D%E6%85%95%E7%99%BD')
  ```


## Path
> 操作文件路径

  * 获取路径信息
    * __dirname__：路径
    * __extname__：扩展名
    * __basename__：文件名
  * 路径拼接
    * __join__：使用当前系统的路径分隔符拼接路径
    * __resolve__：将相对路径拼接出绝对路径
    * __relative__：解析出两个路径之间的相对路径
  * 路径处理
    * __parse__：路径分解而返回对象
    * __format__：路径组合而返回字符串
    * __normalize__：路径标准化
    * __isAbsolute__：判断路径是否为绝对路径


  ```js
  var path = require('path')
  path.dirname('/foo/bar/baz/asdf/a.txt')

  path.join('/foo', 'bar', 'baz/asdf', '..')
  path.resolve('www', 'js/upload', '../mod.js')
  path.relative('C:/test/aaa', 'C:/bbb')

  path.parse('/home/user/dir/file.txt')
  path.format({
      dir: '/tmp', 
      name: 'hello',
      ext: '.js'
  })
  path.normalize('a/../user/bin')
  path.isAbsolute('/foo/bar')
  ```


## Events
> 事件处理模块

  <div style="text-indent: 2em;">回调函数模式让 NodeJS 可以处理异步操作，但对于无法处理多状态的异步操作则需要进行拆分而分阶段执行。为了解决这个问题，NodeJS 提供了 Event Emitter 接口，通过事件解决多状态异步操作的响应问题。events 模块对外暴露一个 EventEmitter 类，可以用来生成事件发生器的实例 emitter，然后通过实例方法处理事件，比如 on/once 监听事件、emit 触发事件、removeListener 删除事件等。</div>
        
  ```js
  // 模块调用
  var events = require("events")
  var emitter = new events.EventEmitter()

  // 事件监听
  emitter.on("connection", function () {
      console.log("linked Success");
  })

  // 事件触发
  emitter.emit("connection")
  ```



## FS
> 在服务端来操作文件。默认异步操作，同步 API 则只需添加 Sync，比如 readFileSync

  * 操作文件
    * __readFile__：文件读取
    * __writeFile__：文件写入
    * __appendFile__：追加写入
    * __rename__：文件重命名
    * __unlink__：文件删除
  * 操作目录
    * __mkdir__：创建
    * __readdir__：读取
    * __rmdir__：删除空目录
    * __chmod__：修改权限
  * 其它
    * __stat__：详细信息
    * __exists__：判断存在

  
  ```js
  var fs = require("fs");
  fs.stat('input.txt', function (err, stats) {

      // 大括号中只有一句语句时可以省略
      if (err) return console.error(err) 

      stats.isFile()        // 是否为文件
      stats.isDirectory()   // 是否为目录
  })

  // 异步写入
  fs.writeFile('input.txt', '写入文件的内容',  function(err) {

      if (err) return console.error(err) 

      // 异步读取
      fs.readFile('input.txt', function (err, data) {

          if (err) return console.error(err) 

          var data_str = data.toString()
      })
  })
  ```



## Stream
> 本质是对 buffer 对象的高级封装，它操作的底层是 buffer 对象

### 流
  <div style="text-indent: 2em;">操作系统采用数据块（chunk）的方式读取数据，每收到一次数据就存入缓存。NodeJS 应用程序有两种缓存的处理方式：第一种是先将数据全部读入内存，然后一次性从缓存中读取，这种方式对于视频等二进制大文件很容易使内存"爆仓"。第二种是采用 数据流（stream）的方式，边读取边存入。</div>

  <div style="text-indent: 2em; margin-top: 15px;">流表示一组有序的、有起止点的字节数据的序列，但是流的数据不能一次性读取或写入。NodeJs 将关于流的操作封装到了 Stream 模块并提供了 Stream API 通过事件来实现基于流的IO操作，比如处理普通文件、网络文件 (http、net)、设备文件 (stdin、stdout)。</div>
            
            
### 流操作
  * __流读取__：NodeJS 不断将文件一小块内容读入缓冲区，再从缓冲区中读取内容
  * __流写入__：NodeJS 不断将流数据写入内在缓冲区，待缓冲区 满后再将缓冲区写入到文件中，重复操作直到完成
  * __直接操作__：readFile/read、writeFile/write 一次性读取内容或写入内存


### 流事件
> 所有 Stream 对象 都是 EventEmitter 类的实例，读写流的不同状态时就会触发相应事件

  * __data__：当有数据可读时触发
  * __end__：数据已经全部读取完毕时触发
  * __error__：读取或写入的过程中发生错误时触发
  * __finish__：所有数据都已经被写入到底层系统时触发


### 流类型
  * __Readable__：从流中读取数据的 API，用于对外提供数据
  * __Writable__：往流中写入数据的 API，用于写入数据
  * __Duplex__：在流中可读写数据的 API，用于读取和写入数据
  * __Transform__：在读写过程中可以修改和变换数据的 Duplex 流


### 流模式
> 根据流中传递数据的类型来区分，一般需要在创建流时指定其模式而且不再修改，但是可以通过拼接转换流来进行模式转换并得到一个新的流

  * __Object Mode__：对象模式，即流中传递的是任意类型的 Js 对象（null 除外）
  * __Buffering Mode__：Buffer 模式，即流中传递的是 Buffer 对象

        
### null 对象
> 在流中的作用如下

  * 向 Writable 流中写入 null，表示数据源的数据已经写入完成，继续写入数据则会报错
  * 从 Readable 流中读取到 null，表示所有数据已经读取完毕，流中不会再有可读取的数据


### 流的拼接
  <div style="text-indent: 2em;">就像可以把两个水管串成一个更长的水管一样，一个 Readable 流和一个 Writable 流串起来后，所有的数据自动从 Readable 流进入 Writable 流。Readable API 提供了 pipe 方法用于流的拼接，但注意位于中间的流必须是 Duplex 流。</div>


```js
// 读取数据流
var fs = require("fs")
var data = ''
// 创建可读流，即将文件内容读取为流数据
var readStream = fs.createReadStream('input.txt') 
readStream.setEncoding('UTF8')  
// 处理流事件 --> data, end, and error
readStream.on('data', function(chunk) {
    data += chunk
})
readStream.on('end',function(){
    console.log(data)
})
readStream.on('error', function(err){
    console.log(err.stack)
})


// 写入流
var fs = require("fs")
var data = '要写入的内容'
// 创建可写流并写入到文件中
var writeStream = fs.createWriteStream('output.txt')
writeStream.write(data, 'UTF8')
writeStream.write(new Buffer('使用Stream写入二进制数据...\n', 'utf-8'));
writeStream.end()   // 标记文件末尾

// 处理流事件 --> data, end, and error
writeStream.on('finish', function() {
    console.log("写入完成。")
})
writeStream.on('error', function(err){
    console.log(err.stack)
})


// 管道流
var fs = require("fs");
// 创建一个可读流
var readStream = fs.createReadStream('input.txt', {
        encoding:'utf8',  // 不传默认buffer，显示为字符串
        start:3,          // 从索引为3的位置开始读
});
// 创建一个可写流
var writeStream = fs.createWriteStream('output.txt');
// 管道读写操作：读取 input.txt 内容并将内容写入到 output.txt
readeStream.pipe(writeStream);


// 链式流：链式是通过连接输出流到另外一个流并创建多个对个流操作链的机制
// 链式流一般用于管道操作。以下是用管道和链式来压缩和解压文件
var fs = require("fs")
var zlib = require('zlib')
var data = 'test dfddgfdgfdgfdgfd jkwu'
// 压缩 input.txt 文件为 input.txt.gz
fs.createReadStream('input.txt')
    .pipe(zlib.createGzip())
    .pipe(fs.createWriteStream('input.txt.gz'))
// 解压 input.txt.gz 文件为 input.txt
fs.createReadStream('input.txt.gz')
    .pipe(zlib.createGunzip())
    .pipe(fs.createWriteStream('input.txt'))
        
       
// HTTP 对象使用 Stream 接口，实现网络数据的读写
var http = require('http')
var server = http.createServer(function (req, res) {
    var body = ''
    req.setEncoding('utf8')
    req.on('data', function (chunk) {
        body += chunk
    });
    req.on('end', function () {
        try {
            var data = JSON.parse(body);
        } catch (er) {
            res.statusCode = 400;
            return res.end('error: ' + er.message);
        }
        res.write(typeof data);
        res.end();
    })
})
server.listen(8080)


// 错误处理
http.createServer(function (req, res) {
    var stream = fs.createReadStream('filename.txt')
    stream
    .on('error', onerror)
    .pipe(zlib.createGzip())
    .on('error', onerror)
    .pipe(res)
  
    onFinished(res, function () {
      stream.destroy()
    })
})
```


## util
> 提供一系列实用方法，注意需要安装 npm install util

  * 格式化字符串：`format`
  * 数据类型验证：`isDate、isArray、isError、isRegExp`
  * 其它：`inherits (继承)、inspect (对象转化为字符串)`
  


# 五、Web 开发

## 搭建 web 服务器
### 通过插件
> http-server、live-server

  ```js
  npm install http-server -g
  cd progect      // 进入项目目录
  http-server     // 开启服务
  ```


### 通过 NodeJs
  ```js
  var http = require('http');
  var url = require('url');
  var path = require('path');
  var fs = require('fs');

  // 命令行第三个参数，用来接收目录，可为空，相对当前server.js的目录名称
  // 比如 node server debug，则 debug 文件夹与 server.js 文件同级并以 debug 启动服务
  var dir, arg = process.argv[2] || ''; 

  http.createServer(function (req, res) {
      var pathname = __dirname + url.parse(req.url).pathname;
      // 保存 dir (目录)
      dir = dir ? dir : pathname; 
      // 替换文件静态路径
      pathname = dir ? pathname.replace(dir, dir + arg + '/') : pathname; 
      if (path.extname(pathname) == "") {
          pathname += "/";
      }
      if (pathname.charAt(pathname.length - 1) == "/") {
          // 入口文件，此处默认index.html
          pathname += "index.html"; 
      }

      fs.exists(pathname, function (exists) {
          if (exists) {
              switch (path.extname(pathname)) {
                case ".html":
                    res.writeHead(200, {"Content-Type": "text/html"});
                    break;
                case ".js":
                    res.writeHead(200, {"Content-Type": "text/javascript"});
                    break;
                case ".css":
                    res.writeHead(200, {"Content-Type": "text/css"});
                    break;
                case ".gif":
                    res.writeHead(200, {"Content-Type": "image/gif"});
                    break;
                case ".jpg":
                    res.writeHead(200, {"Content-Type": "image/jpeg"});
                    break;
                case ".png":
                    res.writeHead(200, {"Content-Type": "image/png"});
                    break;
                default:
                    res.writeHead(200, {"Content-Type": "application/octet-stream"});
              }

              // res 可以自己添加信息来简单交互 比如可以修改 header 信息、修改返回数据
              fs.readFile(pathname, function (err, data) {
                  res.end(data);
              });
          }
          else {
              res.writeHead(404, {"Content-Type": "text/html"});
              res.end("Not Found");
          }
      });
  }).listen(9090, "127.0.0.1"); // 服务器端口

  console.log("server running at http://127.0.0.1:9090/");
  ```



## 连接 mysql
> 安装 mysql 及其驱动：npm install mysql node-mysql -g     

### 直接连接
  ```js
  let http = require('http')
  let mysql = require("mysql")

  const connection = mysql.createConnection({
      host: "127.0.0.1",      // 主机
      user: "root",           // 用户名
      password: "li1993416",  // 用户密码
      port: "3306",           // 端口号
      database: "mysql"       // 数据库名
  })

  // 连接数据库
  connection.connect(function(err){
      if(err){
          console.log(`mysql connect fail: ${err}!`);
      }else{
          console.log("mysql connect success!");
      }
  });

  // 执行查询
  let str = "";
  let sqlQuery = "select * from students";
  connection.query(sqlQuery, function(err, result){
      if(err){
          console.log(`SQL error: ${err}!`);
      }else{
          str = JSON.stringify(result);
      }
  });

  // 返回数据
  http.createServer((req, res)=>{
      res.end(str)
  }).listen(3000);
  console.log('Server running at localhost:3000')

  //关闭connection  
  connection.end(function(err){  
      if(err){ 
          console.log(err.toString());
          return;  
      }  
      console.log('[connection end] succeed!')
  })
  ```


### 通过连接池
> 数据库连接池负责分配、管理和释放数据库连接，它允许应用程序重复使用一个现有的数据库连接而不是再重新建立一个，用于对数据库操作的性能

  ```js
  let mysql = require('mysql');
  const pool = mysql.createPool({
      host: "127.0.0.1",      // 主机
      user: "root",           // 用户名
      password: "li1993416",  // 用户密码
      port: "3306",           // 端口号
      database: "mysql"       // 数据库名
  })
  pool.getConnection(function(err, connection) {
      connection.query('select * from students', [], function(err, result) {
          if (err) {
              console.log("Error: " + err.message);
              return;
          }
          connection.release();
          console.log(result);
      })
  })
  ```


## 上传文件

  ```html
  <!-- index.html -->
  <form action="/file_upload" method="POST" enctype="multipart/form-data">
      <input type="file" name="image" />
      <input type="submit" value="上传文件">
  </form>
  ```

  ```js
  // server.js
  var express = require('express');
  var app = express();
  var fs = require("fs");

  var bodyParser = require('body-parser');
  var multer  = require('multer');

  app.use(express.static('public'));
  app.use(bodyParser.urlencoded({ extended: false }));

  // dest 定义上传目录，array 限制上传类型（即 input name）　
  app.use(multer({ dest: './uploads'}).array('image'));

  app.get('/', function (req, res) {
      res.sendFile( __dirname + "/" + "index.html" );
  })

  app.post('/file_upload', function (req, res) {

      console.log(req.files[0]);  // 上传的文件信息
      var des_file = __dirname + "/" + req.files[0].originalname;

      fs.readFile( req.files[0].path, function (err, data) {
          fs.writeFile(des_file, data, function (err) {
              if(err){
                  console.log(err)
              }else{
                  response = {
                      message: 'File uploaded successfully', 
                      filename: req.files[0].originalname
                  }
              }
              console.log( response );
              res.end( JSON.stringify( response ) );
          })
      })
  })

  var server = app.listen(3000, function () {
        console.log("localhost:3000")
  })


  // 运行命令
  npm install multer body-parser -S
  node server.js
  ```


## WebSocket

### 通过 ws 模块

  ```js
  // 安装
  cnpm i ws -S

  // 客户端js
  if(window.WebSocket != undefined) {  
      //创建一个WebSocket，监听端口6060端口，这里必须是：ws，不能是http
      var connection = new WebSocket('ws://localhost:6060'); 

      // readyState : 0：正在连接、1：连接成功、2：正在关闭、3：连接关闭
      console.log(connection.readyState); 

      // 握手协议成功以后，readyState就从0变为1，并触发open事件  
      connection.onopen = (event) => {   
          console.log(event);
          //连接建立后，客户端通过send方法向服务器端发送数据。   
          connection.send('client message'); 
      };  

      //监听  
      connection.onmessage = (event) => {   
          console.log(event);  
      };  

      //关闭WebSocket连接，会触发close事件。  
      connection.onclose = () => {   
          console.log("Closed");  
      };  

      //出现错误  
      connection.onerror = (event) => {   
          console.log("Error: " + event.data);  
      }
  }

  // 服务器 server.js

  // 启动 WebSocket 服务器
  var server = require('http').createServer() , 
      WebSocketServer = require('ws').Server , 
      wss = new WebSocketServer({ server: server }) , 
      app = require('express')() , 
      port = 6060;

  // 使用 express 中间件
  app.use(function (req, res) { 
      //send用来向客户端发送信息，on用来接收/监听客户端发来的信息
      res.send({ msg: "hello" });
  });
  // 监听事件  %s 字符串、%d 整数、%f 浮点数
  wss.on('connection', function connection(ws) { 
      //监听 
      ws.on('message', function incoming(message) {  
          console.log('received: %s', message);
      });
      //发送 
      ws.send('something')
  })

  server.on('request', app);
  server.listen(port, function () { 
      console.log('http://localhost:' + server.address().port) 
  })
  ```


### 通过 socket.io

  ```js
  // 安装
  cnpm i socket.io -S

  // 客户端 js (需要引入 socket.io.js)
  var socket = io.connect('http://localhost:8080')  
  socket.on('news', function (data) {   
      socket.emit('event', { my: 'data' })
  })


  // 服务器 server.js
  var app = require('http').createServer(handler), 
      io = require('socket.io').listen(app), 
      fs = require('fs')

  app.listen(8080);
  io.set('log level', 1)  // 关闭 debug 信息

  function handler (req, res) {
    fs.readFile(__dirname + '/index.html',function (err, data) {  
        if (err) {
          res.writeHead(500);
          return res.end('Error loading index.html');
        }    
        res.writeHead(200, {'Content-Type': 'text/html'});    
        res.end(data);
    })
  }

  io.sockets.on('connection', function (socket) {
      socket.emit('news', { hello: 'world' });
      socket.on('my other event', function (data) {
        console.log(data);
      })
  })
  ```


## 解决跨域
  ```js
  cnpm install cors --save

  // app.js 加入代码
  var cors = require('cors');
  app.use(cors({
      origin:['http://localhost:8080'],  //指定接收的地址
      methods:['GET','POST'],            // 指定接收的请求类型
      alloweHeaders:['Content-Type', 'Authorization']  // 指定 header
  }))
  ```

