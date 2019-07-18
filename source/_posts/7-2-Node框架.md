---
title: Node.js 常用框架
tags:
  - NodeJS
categories: Node.js
top: false
keywords:
  - node
date: 2019-04-18 00:23:49
description: Express、Koa、Egg 
---

# 一、Express
> 基于 ES5 语法实现而只能通过回调实现异步代码，框架本身缺少约束而写法较多，只适合开发小型项目并作为新手入门级框架

## 核心特性
  * 设置中间件来响应 HTTP 请求
  * 定义路由表用于执行不同的 HTTP 请求
  * 通过向模板传递参数来动态渲染 HTML


## 基础使用
  ```js
  var express = require('express');
  var app = express();

  // 设置 views 的文件夹和模板
  app.set("views", __dirname + "/views");
  app.set("view engine", "jade")

  // 静态资源
  app.use(express.static(__dirname + '/public'))

  // 动态渲染
  app.get('/', function (req, res) {
      res.send('Hello world');
  }).listen(3000)
  ```


## 搭建应用
  ```js
  // 默认 localhost:3000
  npm install express express-generator -g 

  express -e myApp  // 使用 ejs 模板
  cd myApp
  npm install

  npm start 
  ```



## 中间件
> 本质是处理 HTTP 请求的函数。每个中间件都可以接收到请求对象 req、响应对象 res 和回调函数 next 这三个参数，经过处理后可以通过调用 next() 传递给下一个中间件

### 分类
  * __应用级中间件__：使用 app.use/method 绑定到 app 对象
  * __路由级中间件__：使用 router.use/method 绑定到 express.Router() 对象
  * __错误处理中间件__：接收四个参数，用于处理应用中的错误
  * __第三方中间件__：安装后 require 到文件中使用，用来扩展功能
  * __内置中间件__：express.static 托管静态资源、express.json 解析请求


  ```js
  // 应用级
  app.use((req,res,next) => {   
      next()   
  })

  // 路由级
  var router = express.Router()
  router.get('/user/:id', (req, res, next) => {
      if(req.params.id==0) next('route')
      else next()
  },(req, res, next) => {
      res.end("Hello world");
  })

  // 错误处理
  app.use((err, req, res, next) => {
      res.status(500).send('Something broke')
  })

  // 内置中间件 
  app.use(express.static(__dirname + '/public'))
  ```


### 定义方法
  * __app.use__：会对客户端请求路径及其所有扩展结果进行匹配
  * __app.method__：会对客户端请求路径进行精确匹配，但会忽略其中的锚点和 get 提交的参数数据
  
  
  ```js
  // 路径默认 '/'，匹配所有路径(都可以看作是 '/' 的扩展)
  app.use((req, res, next) => { })

  // 路径默认 '/' ，只匹配根路径
  app.get((req, res, next) => { })
  ```


### 响应方法
  * __res.end__：结束响应进程但不返回数据
  * __res.send__：发送各种类型的响应
  * __res.json__：发送 JSON 响应
  * __res.jsonp__：在 JSONP 的支持下发送 JSON 响应
  * __res.render__：呈现视图模板
  * __res.redirect__：重定向请求
  * __res.sendFile__：以八位元流形式发送文件
  * __res.download__：提示将要下载文件
  * __res.sendStatus__：设置响应状态码并发送其字符串表示


## 路由管理

### 路径规则
  * url 字符串不区分字母的大小写
  * url 传递的数据通过冒号语法提取
  * 所有 get 方式提交的 url 参数及锚点均会被忽略
  * 所有路由中间件的书写顺序至关重要，只会执行首次匹配到的中间件


  ```js
  // localhost:3000/tom/24：tom, 24
  app.get('/:name/:age', (req, res)=>{
      console.log(req.params.name, req.params.age)
      res.send()
  })

  // localhost:3000/a#?name=tom 
  app.get('/a', (req, res)=>{
      res.send('success')
  })


  // localhost:3000/admin/login：只执行第一个
  app.get('/:username/:method', (req, res)=>{
      console.log(1)
      res.end()  // 此处如果执行 next() 则会第二个
  })
  app.get('/admin/login', (req, res)=>{
      console.log(2)
      res.end()
  })
  ```


### 路径匹配

#### 字符串
  ```js
  // 匹配根路径的请求
  app.get('/', function (req, res) {
      res.send('root');
  })

  // 匹配 /about 路径的请求
  app.get('/about', function (req, res) {
      res.send('about');
  })

  // 匹配 /random.text 路径的请求
  app.get('/random.text', function (req, res) {
      res.send('random.text');
  })
  ```


#### 字符串模式
  ```js
  // 匹配 /user、/user/5，去掉 ? 则只匹配 /user/5
  app.get('/user/:id?', function(req, res) {
      res.send('/users/:id?')
  })

  // 匹配 acd 和 abcd
  app.get('/ab?cd', function(req, res) {
      res.send('ab?cd')
  })

  // 匹配 /abe 和 /abcde
  app.get('/ab(cd)?e', function(req, res) {
      res.send('ab(cd)?e')
  })

  // 匹配 abcd、abbcd、abbbcd等
  app.get('/ab+cd', function(req, res) {
      res.send('ab+cd')
  })

  // 匹配 abcd、abxcd、abRABDOMcd、ab123cd等
  app.get('/ab*cd', function(req, res) {
      res.send('ab*cd')
  })
  ```


#### 正则表达式
  ```js
  // 匹配任何路径中含有 a 的路径：
  app.get(/a/, function(req, res) {
      res.send('/a/')
  })

  // 匹配 butterfly、dragonfly，不匹配 butterflyman、dragonfly man等
  app.get(/.*fly$/, function(req, res) {
      res.send('/.*fly$/')
  })
  ```

#### 数组
  ```js
  // 匹配 /abcd、/xyza、/lmnand、/pqr
  app.use(['/abcd', '/xyza', /\/lmn|\/pqr/], function (req, res, next) {
      next()
  })
  ```


### 路由句柄
  ```js
  // 一个回调函数
  app.get('/example/a', function (req, res) {
      res.send('Hello from A')
  })
  // 多个回调函数
  app.get('/example/b', function (req, res, next) {
      next()
  }, function (req, res) {
      res.send('Hello')
  });

  // 回调函数数组
  var a = function (req, res, next) { next() }
  var b = function (req, res, next) { next() }
  var c = function (req, res) { res.send('Hello from C') }
  app.get('/example/c', [a, b, c])

  // 混合使用
  app.get('/example/d', [a, b], function (req, res, next) {
      next()
  }, function (req, res) {
      res.send('Hello from D');
  })
  ```


### 参数解析
  * __req.param__：已被弃用
  * __req.query__：解析 GET 请求中的查询字符串
  * __req.body__：解析 POST 请求中的数据（需引用body-parser）
  * __req.params__：解析 GET/POST 请求中的占位符数据（如/:name）
  

  ```js
  // user/10
  app.get("user/:id", function (req, res) {
      res.send(req.params["id"])
  });

  // user/?id=10
  app.get("/user", function (req, res) {
      res.send(req.query["id"])
  })
  
  // 通过 body-parser 模块获取 post 请求数据
  let bodyParser=require('body-parser')
  app.use(bodyParser.urlencoded({ extended: true }))
  app.post("/login", function (req, res) {
      res.send(req.body.name, req.body.password)
  })
  ```


### 特殊路由
  ```js
  // 验证或处理 路径参数
  router.param('id', function (req, res, next, id) {
      req.name = name
      next()
  })
  router.get('/user/:id', function (req, res) {
      res.send('hello ' + req.name)
  })


  // 链式路由：一个路径处理多种请求
  app.route('/book')
    .get(function(req, res) {
        res.send('Get a random book');
    })
    .post(function(req, res) {
        res.send('Add a book');
    })
  ```


### next 方法
  * __next()__：从下一个处理函数开始往下执行
  * __next('router')__：直接执行下一个相同路由


  ```js
  app.get('/', function (req, res) {
      console.log(1)
      next()           
      // 结果：1234，替换为next('route')则结果：1234
      
  }, function (req, res) {
      console.log(2)
      next('route')
  })

  app.get('/', function (req, res) {
      console.log(3)
      next()
  }, function (req, res) {
      console.log(4)
  })
  ```


# 二、Koa
> Express 团队打造的进阶框架，基于 ES7 语法使用 Promise 实现异步而极大简化了异步代码。但是开发项目时缺少约束，多人开发时代码合并困难而不适合团队协作开发项目


## 主要特点
  * __洋葱中间件模式__：next函数决定是否执行下一个中间件
  * __Context 对象__：内部封装了 HTTP 请求对象和响应对象
  * __异步处理__：使用 Promise 并配合 async await 实现异步


## 基础使用
  ```js
  // npm install koa koa-route
  const Koa = require('koa')
  const app = new Koa()
  const path = require('path')
  const route = require('koa-route')

  // 静态资源服务器：npm install koa-static
  const serve = require('koa-static')
  app.use(serve(path.join(__dirname, '/public')))


  // 动态渲染页面：npm install koa-views ejs
  let views = require('koa-views');
  app.use(views(__dirname,{
      extension:'ejs' //指定用ejs模板
  }))
  app.use(async (ctx, next)=>{
      // 渲染 index.ejs
      await ctx.render('index', {name:'cgp', age:9})
  })

  app.listen(3000)
  ```


## 搭建应用
  ```js
  // 默认 localhost:3000
  npm install koa koa-generator -g 

  koa2 project 
  cd project
  npm install

  npm start
  ```


## 路由管理

### 原生路由
  ```js
  // 通过 ctx.request.path 获取用户请求的路径
  app.use(ctx => {
      if (ctx.request.path == '/') {
          // ctx.response.body的简写 
          ctx.body = '<p>Hello World</p>'
      }
  })
  ```


### 路由中间件
> 安装：npm install koa koa-router

  ```js
  var Koa = require('koa');
  var app = new Koa();
  var router = require('koa-router')();

  // 基础用法：常用 get、post、all
  router.get('/', async (ctx, next)=>{
      ctx.body = 'hello people';
      await next()
  });
  router.get('/list', async (ctx, next)=>{
      ctx.body = 'list';
  });


  // 传递参数
  router.get('/home', (ctx,next)=>{ 
      console.log('/home?id=1 传递参数：', ctx.query);
      next();
  });
  router.get('/user/:name', (ctx, next) => {
      console.log('/user/tom 传递参数：', ctx.params.name);
      next();
  });
  router.post('/user/register',async(ctx, next)=>{
      console.log(ctx.request.body)
  })


  // 重定向
  router.get('/login', async (ctx, next) => {
      ctx.redirect('/user')
  })


  // 嵌套路由
  let home = new Router();
  home.get('/list', async(ctx)=>{
      ctx.body = "Home list";
  }).get('/todo', async(ctx)=>{
      ctx.body ='Home ToDo';
  });
  let user = new Router();
  user.get('/list', async(ctx)=>{
      ctx.body = "User list";
  });


  // 装载所有子路由
  let router = new Router();
  router.use('/home', home.routes(), home.allowedMethods());
  router.use('/user', user.routes(), user.allowedMethods());

  // 加载路由：当请求数据的方法与设置的方法不一致时会报错
  app.use(router.routes()).use(router.allowedMethods());

  // 监听服务
  app.listen(3000);
  ```


## 中间件
  <div style="text-indent: 2em">每个中间件默认接受 `Context 对象、next 函数` 两个参数。`Context 对象内部封装了原生 NodeJS 包含的 request、response 对象，分别为 ctx.req、ctx.res。next函数被调用时，会暂停该中间件的运行并将控制传递给下一个中间件。</div>


### 分类
  ```js
  // 第三方中间件：引入koa-router并对其实例化
  var router = require('koa-router')()

  // 错误处理中间件
  app.use(async (ctx, next) => {
      try {
          await next()
      } catch (error) {
          ctx.response.body = {
          code: '404',
          message: '服务器异常',
          desc: error.message
      }
  })

  // 应用级中间件
  app.use(async (ctx, next) => {
      await next();
      if(ctx.status === 404){
          ctx.body = "404页面"
      }
  })

  // 路由级中间件
  router.get('/', async ctx => {
      ctx.body = 'hello koa';
  })

  // 启动路由
  app.use(router.routes()).use(router.allowedMethods())
  ```
        

### 执行顺序
  * 多个中间件会形成堆栈结构，按先进后出顺序执行。
  * 任何路由都会先经过应用级中间件，当执行完成next后再去匹配相应路由
  * 路由在匹配成功并执行完相应操作后还会再次进入应用级中间件执行 next 之后的逻辑


  ```js
  app.use(async (ctx, next) => {
      console.log('1')
      await next();
      console.log('7')
  })

  app.use(async (ctx, next) => {
      console.log('2');
      await next();
      console.log('4')
  })

  router.get('/user', async (ctx, next) => {
      console.log('3')
      ctx.body = 'Hello Koa';
  })
  ```


### 第三方中间件
  * __kao-views__：模板渲染
  * __koa-router__：路由处理
  * __koa-static__：静态文件读取
  * __koa-bodyparse__：处理post请求的数据



## 常用功能

### Cookies
> 框架自带属性，不需要引入中间件

  ```js
  // 设置中文时报错解决
  app.use(async (ctx, next) => {

      // 使用new Buffer().toString('base64')转换
      this.cookies.set('test', new Buffer('你好').toString('base64'))
      const test = new Buffer(ctx.cookies.get('test'), 'base64').toString()

      // 使用encodeURIComponent()转换
      ctx.cookies.set('name', encodeURIComponent('李'))
      const name = decodeURIComponent(ctx.cookies.get('name'))
  })
  ```


### 表单处理
  ```js
  const router = require('koa-router')();
  const bodyParser = require('koa-bodyparse');
  app.use(bodyParser()); 
  
  router.post('/add', async (ctx)=>{
      // 获取表单提交的数据
      const body = ctx.request.body
      ctx.body = { name: body.name }
  })
  ```


### 上传文件

  <div align="center"> 
    ![Node UploadFile](/images/nodejs/node-uploadFile.png)
  </div> 

  ```js
  // 引入：npm install koa-body --save
  const koaBody = require('koa-body');
  app.use(koaBody({
      multipart: true,
      formidable: {
          maxFileSize: 200*1024*1024  // 设置文件最大限制，默认 2M
      }
  }));


  // 上传图片
  router.post('/file_upload', async (ctx) => {
      // 获取上传图片
      const data = ctx.request.body.files.data;
      // 创建可读流
      const savePath = path.join(`./files`, data.name)
      const reader = fs.createReadStream(data.path)
      // 创建可写流
      const writer = fs.createWriteStream(savePath)

      
      const pro = new Promise( (resolve, reject) => {
          // 可读流通过管道写入可写流
          var stream = reader.pipe(writer);

          // 图片上传成功后返回服务器地址，实时显示服务器图片
          stream.on('finish', function () {
              resolve(`http://当前服务器地址${data.name}`);
          });
      })
      ctx.response.body =  await pro
  })


  // 上传单个文件
  router.post('/uploadfile', async (ctx, next) => {
      // 获取上传文件
      const file = ctx.request.files.file; 
      // 创建可读流
      const reader = fs.createReadStream(file.path);
      let filePath = path.join(__dirname, 'public/upload/') + `/${file.name}`;
      // 创建可写流
      const upStream = fs.createWriteStream(filePath);
      // 可读流通过管道写入可写流
      reader.pipe(upStream);
      return ctx.body = "上传成功！";
  })

  
  // 上传多个文件
  router.post('/uploadfiles', async (ctx, next) => {
      // 获取上传文件，旧版本通过 ctx.request.body.files
      const files = ctx.request.files.file; 
      for (let file of files) {
        // 创建可读流
        const reader = fs.createReadStream(file.path);
        // 获取上传文件扩展名
        let filePath = path.join(__dirname, 'public/upload/') + `/${file.name}`;
        // 创建可写流
        const upStream = fs.createWriteStream(filePath);
        // 可读流通过管道写入可写流
        reader.pipe(upStream);
      }
      return ctx.body = "上传成功！";
  })
  ```



### 合并中间件
  ```js
  // koa-compose：将多个中间件合成为一个
  const compose = require('koa-compose')
  const first = asycn (ctx, next) => {
      await next()
  }
  const second = async ctx => {
      ctx.body = 'Hello'
  }
  const middle = compose([first, second])
  app.use(middle)
  ```


# 三、Egg
> 基于 koa2 的企业级框架，是由阿里 NodeJS 团队封装的企业级 Web 应用解决方案

  <div style="text-indent: 2em">官方定位是 为企业级框架和应用而生和孕育出更多上层框架，奉行 `约定优于配置`，帮助开发团队和开发人员降低开发和维护成本。async 的特性避免了回调地狱，洋葱式的中间件架构更容易后置逻辑，内置的多进程管理会更好的利用服务器性能，以及更方便的单元测试和更加约束的目录架构。</div>


## 框架体系
  * 核心体系：egg-core 模块
    * 以 Koa.js 为基类，利用它的 中间件机制 和 HTTP服务机制 作为框架基础
    * 以 Loader 机制作为 Egg.js 各分层机制的约定基础
  * 辅助体系：egg-script、egg-bin 等模块
    * 支持 开发模式、生产模式、多线程模式
  * 生态体系：中间件、插件、框架


## 主要特点
  * 渐进式开发
  * 内置多进程管理
  * 高度可扩展的插件机制
  * 框架稳定，测试覆盖率高
  * 基于 Koa 开发，性能优异
  * 提供基于 Egg 定制上层框架的能力


## 搭建应用
  ```js
  npm i egg-init -g
  egg-init test --type=simple  

  cd test
  npm install

  npm run dev 
  ```


## 项目运行
  * 绿色虚线框中的所有组件组成了一个实际执行代码逻辑的进程
  * request 进来后先穿过中间件，自定义中间件放在 app/middleware 并在 config 中启用
  * 静态资源（project/app/public）经过内置的 egg-static 中间件时会直接响应给客户端，其它资源则会穿越所有中间件并到达路由文件 router.js
  * 路由文件一般没有任何逻辑而只起到目录和索引的作用，它会直接指向一个处理请求的 controller（app/controller）
  * Controller 负责调用并组合 Service（app/service），最后将响应提交给客户端。Service 负责调用 Model 处理具体的业务逻辑
  * 除此之外，Worker 中还有定时任务（app/schedule）

  <div align="center"> 
    ![Egg Run](/images/nodejs/egg-run.png)
  </div> 


## 原理分析
> 要分析框架的实现原理，直接从所有源码开始分析比较难。但是可以先将其精简到最小功能系统，然后针对各个功能进行分析和叠加 

### 模块架构
  <div align="center"> 
    ![Egg Framework](/images/nodejs/egg-framework.png)
  </div> 


### 最简目录
  ```js
  ├── index.js          // 启动文件：初始化 应用实例 和 HTTP 服务器
  ├── app               // 业务模块：路由文件
  │     └── router.js   
  ├── lib               // 应用模块：提供加载器和底层的中间件、插件、路由等机制
  |     ├── egg-core 
  │     └── egg.js                    
  └── package.json
  ```


### 执行流程
  1. 应用初始化
    * 加载所有 loader
    * 加载所有中间件、插件等内容
    * 路由注册
  2. 服务启动
    * 提供 http 服务
    * 执行路由处理


### 代码逻辑
  1. EggApplication 
    * 继承：EggCore
    * 构建：按照顺序加载所有中间件、插件、路由等内容，this.loader.loadAll()

  2. EggCore
    * 继承：Koa
    * 功能
      * 服务器的底层逻辑
      * 工程项目文件的加载器
      * 提供底层的中间件、插件、路由等机制
    * 构建
      * AppWorkerLoader
        * 继承：EggLoader
        * 加载器初始化：this.loader = new EggLoader()
      * Router
        * 继承：Koa-router
        * 路由初始化：this.router = new Router()
        * 路由方法注入：将路由方法注册到对象属性
  3. EggLoader
    * 构建：注入各种加载器方法
              

### 路由优化
  * 路由大小写敏感
  * RESTFul 实现
  * Controller 句柄使用
  * Generator Function 兼容



### RESTFul
> 测试时需要先安装接口测试工具：chrome postman，然后在 chrome 地址栏使用

  * 约定路由路径和对应的控制器
  * 统一处理封装 router 参数
  * 统一封装 controller：根据请求类型和路径注册对应的控制器
    * Async Function
    * Generator Function
    * controller 句柄读取 this.app.controller


  ```js
  // 路由文件添加
  router.resources('users', '/v1/users', 'users');

  // 新建文件 app/controller/users.js
  const { Controller } = require('egg');

  class UsersController extends Controller {

      async create() {
          const { ctx } = this;
          this.ctx.body = '创建';
      }
      async destroy() {
          const { ctx } = this;
          this.ctx.body = '删除';
      }
      async update() {
          const { ctx } = this;
          this.ctx.body = '修改';
      }
      async show() {
          const { ctx } = this;
          this.ctx.body = '查询';
      }
      async index() {
          const { ctx } = this;
          this.ctx.body = '列表';
      }
      async new() {
          const { ctx } = this;
          this.ctx.body = '创建页面';
      }
      async edit() {
          const { ctx } = this;
          this.ctx.body = '修改页面';
      }
  }

  module.exports = UsersController;
  ```


### 加载器
  <div style="text-indent: 2em">实际项目中的业务代码除了路由文件，还有 controller、service 等其他分层的源码文件。Egg.js 是通过加载器 EggLoader 将这些分层的项目业务代码、中间件和插件代码的大部分功能注入到 this、this.app、this.ctx 对象。</div>



 




