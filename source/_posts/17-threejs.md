---
title: WebGL 和 Three.js
tags:
  - Three.js
categories: Three.js
top: false
keywords:
  - three
date: 2019-04-14 23:06:46
description: WebGL 概念和绘图流程、Three.js 绘图和构成要素
---

# 一、WebGL 基础

## OpenGL、WebGL
  * __OpenGL__：最常用的跨平台图形库，用于渲染 2D、3D 矢量图形的跨语言、跨平台的应用程序编程接口
  * __OpenGL ES__：OpenGL API 的子集 GPU，是针对手机、PDA 和游戏主机等嵌入式设备而专门设计的接口
  * __GLSL__：OpenGL ES 着色语言，用于OpenGL中着色编程的语言。在图形卡的GPU上执行，代替了固定的渲染管线的一部分，使渲染管线中不同层次具有可编程性，如视图转换、投影转换等。  
  * __WebGL__：基于 OpenGL 设计的面向 web 的图形标准，它提供的一系列 Js API 对显卡的硬件细节进行了封装抽象，可用于控制 GPU渲染管线(显卡硬件) 进行图形渲染，从而获得了较高性能。
  * __WebGL程序__：主要组成是 js控制代码 和 计算机的图形处理单元GPU。而 GPU 核心即着色程序 program 由顶点和片元两个着色器组成，使用 GLSL 编写并通过常以字符串的形式存放在Js中等待调用 (浏览器不识别 GLSL)。


## CPU、GPU
> 图像处理主要偏简单的矩阵运算，逻辑判断等很少。两者的设计架构不同。GPU很适合做简单的并发计算，应用于图像处理、深度学习等领域加快速度，引爆了人工智能

  * __CPU__
    * 可看作"万能教授"，但运算效率较低
    * 中央处理器，计算机的运算和控制核心
    * 需要很强的通用性来处理各种不同的数据类型和逻辑判断
  * __GPU__
    * 可看作"专业学生"，用来实现硬件加速计算，执行重复运算效率高
    * 图像处理器，是显卡的处理器和核心，也是图像运算工作的微处理器
    * 专为执行复杂的数学和几何计算而设计，用于将 CPU 从图形处理的任务中解放出来而去执行其他更多的系统任务，提高计算机的整体性能


## 图形渲染
  <div style="text-indent: 2em">渲染是从使用计算机程序模型生成图像的过程。在图形中一个虚拟场景的呈现需要使用 几何、视点、纹理、光照和阴影等通过一个渲染程序传递信息描述，输出一个数字图像。渲染可以在本地或远程上进行。复杂图像所需的硬件资源可以在专用服务器上完成，即基于服务器的绘制。渲染也可以通过在本地CPU完成, 即基于客户端的渲染。所以渲染类型如下：</div>

  * 软件渲染：所有的渲染是在 CPU 的帮助下计算完成。 
  * 硬件渲染：所有的图形计算都由 GPU (图形处理单元)完成的。

              
## 图形术语
  * __坐标系__：x、y、z 轴
    * 无论画布多大，WebGL 上的坐标被限制为 (1, 1, 1)、(-1, -1, -1)
    * WebGL 在 GPU 上的主要工作就是将顶点数据通过矩阵变换转换到裁剪空间坐标，然后基于空间坐标绘制像素点
  * __缓冲__：即将发送到GPU 的一些二进制数据序列
    * 缓冲区对象用于存储要绘制模型的各种数据，比如顶点、颜色等
    * 缓冲区对象驻留在 GPU 存储器却可以被直接呈现，提高了性能
  * __图元__：几何顶点的组合，比如点、线段和三角面
  * __片元和像素__
    * 在光栅化中纹理映射之后图元信息转化为了像素
    * 片元包括 像素点 和 颜色、深度和纹理数据等信息
  * __网格__：使用顶点所绘的 2D、3D 模型，它的每个面被称为 一个片元 (一般是三角形)
  * __着色器__
    * 现代 GPU 中的顶点处理和片段处理等可编程部分被称为着色器
    * 主要包括顶点着色器和片元着色器，可理解为运行在显卡中的指令和数据
    * 着色器程序使用的是 GLSL ES 语言，所以在 Js 中需要存放在字符串中等待调用编译，通过编译处理后传递给 顶点/片元 着色器执行
  * __纹理图像__
    * 将一张真实图片贴到一个几何图像表面称为 纹理映射，映射的图像为纹理图像
    * 组成纹理图像的像素称为纹素，而纹理坐标 是纹理图像上的坐标，通过纹理坐标可以在纹理图像上获取纹素颜色
  * __绘图__
    * WebGL 中的顶点、颜色、纹理等几何详细信息存储在缓冲区对象，然后被传递到GPU上的着色器程序来创建图形对象
    * WebGL 只能绘制 点、线和三角形，其他都是由它们合成后绘制到三维空间



# 二、WebGL 程序
> 程序代码是 Js、OpenGL 的组合，两者分别负责与 CPU、GPU 通信

## 显卡内部结构 
> 电脑一般都具有独立显卡
       
  * GPU：渲染管线
    * 功能：和 GPU 传递数据
    * 内容：可编程的着色器、不可编程的光栅器等     
  * 显存：GPU 读写数据的存储区  
    * 顶点缓存：存储顶点的位置、颜色等数据  
    * 渲染缓存：接收渲染管线生成数据
    * 纹理缓存：用于实现纹理贴图
    * 帧缓存 
      * 分类: 屏幕缓存、离屏缓存
      * 组成: 颜色缓存、深度缓存、模板缓存


## 图像绘制流程
> 上线网站的 WebGL 相关文件会放在远程的服务器硬盘存储器。网页 http 请求后，CPU 将顶点坐标等数据 和 WebGL 程序代码 传递给 GPU 存储

  1. 浏览器解析硬盘的 WebGL 文件，CPU 分配内存并将数据传递给 GPU 存储数据和程序代码
  2. CPU 执行程序初始化并调用 GPU 存储的程序代码，初始化着色器时把处理好的 着色器程序 和 矩阵等数据 发送给 GPU 处理器
  3. GPU 通过矩阵变换和光栅化等操作将图像信息转化为像素并写入显卡的帧缓存 (包括颜色、透明度等)
  4. 显示器扫描读取显存中的像素数据，然后显示到浏览器 canvas


## GPU 处理流程
> 获取顶点坐标、图元装配(即画出一个个三角形)、光栅化(生成片元, 即一个个像素点)、存储在缓存区

  1. 数据准备
    * uv：贴图坐标
    * 索引：三角形绘制顺序
    * 矩阵数据：uniform 变量传递给顶点着色器
    * 顶点坐标：attribute 变量传递给顶点着色器，一般由三维软件导出几何体的各个顶点
  2. 生成顶点着色器
    * 顶点着色器：Js 定义的程序代码，初始化后会传递给 GPU
  3. 图元装配
    * 实现：顶点坐标 转换为图元 (一般是三角形)
    * 本质：GPU 逐顶点数量执行顶点着色器程序，通过矩阵变换将孤立的顶点坐标处理为 2D 图形并输出
  4. 生成片元着色器    
    * 实现：处理模型的颜色、质地、光照效果、阴影
  5. 光栅化       
    * 实现：将图元转换为像素点
    * 片元：一个像素点及其包含的颜色、深度和纹理数据
    * 本质：GPU 逐片元数量执行片元着色器程序，为每个片元进行着色
  6. 存储到缓存区    
    * 片元只要通过了检测和融合 (融合单元主要实现透明度效果)，就可以存储到颜色缓存区，最终完成整个渲染   



# 三、WebGL 绘图

## 初始化绘图上下文
  ```js
  var canvas = document.getElementById("canvas")
  var gl = canvas.getContext("webgl", {
      //可选选项
      antialias: false, 
      stencil: true
  })
  ```

## 初始化着色器程序 
  * 创建：顶点着色器程序、片段着色器程序
  * 编译：通过 js 将这两个着色器 link 到一个 program (着色程序) 并提交到 GPU


## 建立模型和数据缓存  
  * 创建缓冲区对象并绑定到目标，然后写入数据并连接所分配的变量，运行程序时使用缓冲区对象向顶点传入多个顶点数据
  * 创建纹理并加载纹理图像，配置属性并在 webgl 中使用

 
## 图像绘制和动画

### 简单图形 
  * API：`gl.drawArrays(mode, first, count)`
    * mode：画图函数(点、线、三角形)
    * first：指定顶点开始绘制的位置 
    * count：所绘制图形的顶点个数
  * 实例
    * 三个点：`gl.drawArrays(gl.POINTS, 0, 3)`
    * 三角形：`gl.drawsArrays(gl.TRIANGLES, 0, 3)`
                         
### 复杂的三维图形 
  * API：`gl.drawElements(mode, count, type, offset)`
    * type：指定缓冲区中值的类型，常用 gl.UNSIGNED_BYTE/UNSIGNED_SHORT 表示无符号8/16位整数值
    * offset：指定索引数组中开始绘制的位置，以字节为单位
  * 实例
    * 三角形：`gl.drawElement(gl.TRIANGLES, n, gl.UNSIGNED_SHORT, 0)`


## 方法封装
  ```js
  function webGLStart() {
      // 初始化WEBGL上下文信息
      var canvas = document.getElementById("myCanvas");
      initGL(canvas);

      // 初始化着色器
      initShader();

      // 初始化缓冲区
      initBuffers();

      // 初始化纹理图片
      initTexture();

      // 指定清空画布的颜色，
      gl.clearColor(0, 0, 0, 1);

      // 清除颜色缓冲区 (使用定义的颜色来填充相应区域)
      gl.clear(gl.COLOR_BUFFER_BIT);

      // 开启隐藏面消除的功能  启用深度测试
      gl.enable(gl.DEPTH_TEST);

      // 开始绘制场景
      drawScene();
  }
  ```

 
# 四、Three.js 基础
> 使用 js 代码写 3D 程序

## 对比 WebGL

  * WebGL 
    * 原生 API 是一种非常低级的接口，需要一些数学和图形学的相关技术，学习和开发项目的成本较高
    * 可看作是浏览器提供的接口，js 中可以直接用 WebGL API 绘制3D图形
  * Three.js
    * 通过对 WebGL API 的封装与简化而形成的一个易用的图形库，上手容易
    * 可看作是一款 webGL框架。它将复杂接口简单化，将 webGL 基于光栅化的 2D API 封装成了简单的 3D API，同时基于面向对象思维而将数据结构对象化
    

## 简化工作
  * 辅助导出模型数据
  * 自动生成各种矩阵
  * 生成了顶点着色器
  * 辅助生成材质、配置灯光
  * 根据设置的材质生成片元着色器

      
## 执行流程
  1. 创建场景、灯光、相机等
  2. 创建模型、材质(内置/自定义)
  3. 生成顶点着色器、片元着色器
  4. 渲染页面


# 五、Three.js 绘图

## 模型渲染
  * 原理：将创建好的物体添加到场景中，然后通过相机渲染到渲染器，从而呈现到网页
  * 方式
    * 实时渲染：不停地渲染画面
    * 离线渲染：事先渲染出一帧帧图片，使用时拼接
          
               
## 实现步骤
  1. 构建一个三维空间 (创建场景)
  2. 选择一个观察点，并确定观察方向/角度等 (创建相机)
  3. 在场景中添加供观察的物体 (创建物体并添加至场景中)
  4. 将观察到的场景渲染到屏幕上 (创建渲染器并渲染场景)

  ```js
  // 绘制网格线
  function initGrid() {

      // 场景
      var scene = new THREE.Scene();

      // 相机
      var camera = new THREE.OrthographicCamera(-5, 5, 3.75, -3.75, 0.1, 100);
      // 设定相机位置
      camera.position.set(0, -25, 0);
      // 相机看向
      camera.lookAt(new THREE.Vector3(0, 0, 0));
      scene.add(camera);

      // 渲染器
      var renderer = new THREE.WebGLRenderer({ antialias: true });
      // 设定渲染器尺寸
      renderer.setSize(800, 600);
      // 添加到dom
      renderer.autoClear = false;
      // 重绘时颜色
      renderer.setClearColor(0x000000);
      document.body.appendChild(renderer.domElement);

      // 定义一个几何体
      var geometry = new THREE.Geometry();
      geometry.vertices.push(new THREE.Vector3(-2, 0, 0));
      geometry.vertices.push(new THREE.Vector3(2, 0, 0));
      // for循环出来六条线段
      for (var i = 0; i &lt;= 5; i++) {
          // 定义竖着的线段
          var line = new THREE.Line(geometry, new THREE.LineBasicMaterial({
              color: 0xffffff,
          }));
          // 每条线段之间的间隔为0.8，-2是为了达到田字格的效果
          line.position.z = (i * 0.8) - 2;
          scene.add(line);

          // 定义横着的线段
          var line = new THREE.Line(geometry, new THREE.LineBasicMaterial({
              color: 0xffffff,
              opacity: 0.2
          }));
          line.position.x = (i * 0.8) - 2;
          line.rotation.y = 90 * Math.PI / 180;
          scene.add(line);

          // 渲染
          renderer.render(scene, camera);
      }
  }
  ```


## 场景动画
> 在循环渲染(即重绘，循环绘制新场景) 的过程中不断改变 物体或相机的属性，让场景动起来  的颜色、位置等

  * 属性
    * 物体：`scale、rotation、position、material`
    * 相机：`位置、方向、面向坐标`
  * 实现方式
    * __直接实现__
      * 物体移动而摄像机不动：`mesh.position.x -= 1`
      * 摄像机移动而物体不动：`camera.position.x = camera.position.x + 1`
    * __Tween.js__
      * 引入后可以定义某个属性值的过渡而实现复杂动画
      * 它会自动计算出起止的所有中间值，这个过程叫做 tweening 补间

  ```js
  // 旋转的正方体
  var scene, camera, renderer, mesh, stats;
  initThree();
  animation();

  function initThree() {
      // 创建场景对象 (插入光照/物体)
      scene = new THREE.Scene();  

      // 创建相机对象
      camera = new THREE.PerspectiveCamera( 70, window.innerWidth / window.innerHeight, 1, 1000);

      // 光照设置
      var light = new THREE.DirectionalLight( 0xffffff );
      light.position.set( 0, 1, 1 ).normalize();  // 光源位置
      scene.add(light);    // 将光照插入场景


      // 创建网络模型
      var geometry = new THREE.BoxGeometry( 10, 10, 10);  // 创建一个立方体几何对象
      var material = new THREE.MeshPhongMaterial({        // 材质对象
              color: 0x0033ff, 
              specular: 0x555555, 
              shininess: 30 
      });

      mesh = new THREE.Mesh(geometry, material );   // 网格模型对象
      mesh.position.z = -50;
      scene.add(mesh);

      // 创建渲染器对象
      renderer = new THREE.WebGLRenderer({ antialias: true })   // 参数是可选配置
      renderer.setSize(window.innerWidth, window.innerHeight)   // canvas元素设置宽高 (默认300/150)
      renderer.clear()                                // 清除面板
      renderer.setClearColor(0xb9d3ff, 1)             // 设置画布颜色
      document.body.appendChild(renderer.domElement)  // 将Canvas元素添加到<body>中。

      window.addEventListener( 'resize', onWindowResize, false );
      function onWindowResize() {
          camera.aspect = window.innerWidth / window.innerHeight;
          camera.updateProjectionMatrix();
          renderer.setSize( window.innerWidth, window.innerHeight );
          render();
      }
    
      // 执行渲染
      render();         
  }

  function render() {
      renderer.render(scene, camera)    
  }

  // 简单动画：在循环渲染过程中不断改变 物体/相机的属性，从而让场景动起来
  function animation() {
      mesh.rotation.x += .04;
      mesh.rotation.y += .02;

      render();
      requestAnimationFrame(animation);

      // mesh.material = new THREE.MeshLambertMaterial({
      //     color: 0xff0000
      // });
      // mesh.position.z = 1;
      // mesh.position.set(1.5, -0.5, 0);
      // mesh.position = new THREE.Vector3(1.5, -0.5, 0);

      // 更新性能插件     
      // stats.update();  

      // 让物体运动
      // TWEEN.update();   
  }

  // 复杂动画：Tween 的任何一个函数返回的都是自身，支持链式编程
  function initTween(){
      new TWEEN.Tween(mesh.position)    // 要改变属性的对象
              .to( { x: -400 }, 3000 )  // 目标值和需要时间
              .repeat( Infinity )       // 重复无穷次
              .start();                 // 开始动画
  }
  ```


## 性能检测
> 引用插件 stats.js 之后可以在场景中的左上角看到视图小框，点击切换可看到以下信息

  * __38 MS (19-74)__：MS 表示渲染一帧需要的毫秒数，其值越小越好
  * __34 FPS (30-34)__：FPS 表示每秒渲染的帧数，其值越大越好，括号内表示帧率范围

  ```js
  // 动画性能检测： http://www.wjceo.com/lib/js/libs/stats.min.js
  stats = new Stats();

  // 参数为 0 时显示 FPS 界面，参数为 1 时显示 MS 界面
  stats.setMode(1);  

  // stats 的界面对应左上角
  stats.domElement.style.position = 'absolute';
  stats.domElement.left = '0px';
  stats.domElement.top = '0px';
  document.body.appendChild(stats.dom);
  ```


# 六、Three.js 要素
> 绘图页面中必备且唯一的要素：camera、scene、renderer

  * __Scene__：场景（三维空间），是物体、光源等元素的容器 
  * __Camera__：相机（观察者），控制视角位置、范围和焦点位置 
  * __Object3D__：物体（三维模型），包括二维的点线面、三维的粒子等
  * __Light__：光源，包括全局光、平行光、点光源等
  * __Renderer__：渲染器（画布），用于渲染场景及其物体
  * __Control__：控制器（相机控件），可通过键盘、鼠标控制相机


## 相机
### 分类
  * __正投影相机__
    * 特点：表现物体的实际尺寸而远近相同
    * 参数：视角、窗口的长宽比、近远裁面的距离
    * OrthographicCamera(left, right, top, bottom, near, far)
  * __透视投影相机__
    * 特点：类似人眼而近大远小
    * 参数：各平面距离相机中心的垂直距离
    * PerspectiveCamera(fov, aspect, near, far)
  * __3D 相机__
    * 特点：合成两个相机的拍摄结果，用于 3D 立体影像或视差屏障等效果
    * StereoCamera()
  * __全景相机__
    * 特点：可以 360 度拍摄
    * 参数：最后一个设置立方体边缘的长度
    * CubeCamera(near, far, cubeResolution)

### 属性
  ```js
  // 相机设置
  camera.position.set(0, -1, 5);    // 设定相机位置 
  camera.up.x = 0;                 // 相机以哪个方向为上方
  camera.lookAt(scene.position);   // 相机面向哪个坐标(默认面向z轴负方向)
  camera.lookAt(new THREE.Vector3(10,0,0));  // 相机往x轴移动10单位 
  ```


## 物体
> Object3D 是它们的基类。创建时需要 几何体和材质，几何体定义模型的形状 (骨架)，材质定义几何图形的表面效果 (皮肤，包括颜色、纹理、透明度、质感等)

  * __Line__：线段模型，由线条组成
  * __Mesh__：网格模型，由三角面和四边形面组成
  * __Points__：粒子系统，使用一堆点的集合来描述
  * __Sprite__：精灵图，指包含于场景中的二维图像或动画
    * 特点：一个永远面向相机的平面
    * 功能：加载 纹理但是不接受阴影


### 几何体
  * __构造函数创建__：Geometry 是它们的基类
    * __二维平面__
      * 圆平面：CircleGeometry 
      * 矩形平面：PlaneGeometry
    * __三维几何体__
      * 立方体：BoxGeometry、CubeGeometry(旧版本)
      * 球体：SphereGeometry    
      * 圆柱：CylinderGeometry
      * 圆环：TorusGeometry
      * 环面：TorusKnotGeometry
      * 多面体：PolyhedronGeometry
      * 正四面体：TetrahedronGeometry
      * 正八面体：OctahedronGeometry
      * 正二十面体：IcosahedronGeometry
      * 复杂几何体
        * 凸面体：ConvexGeometry
        * 扫描体：LatheGeometry
        * 管状体：TubeGeometry
        * 拉伸几何体：ExtrudeGeometry
        * 参数几何体：ParameterGeometry
        * 文本几何体：TextGeometry
  * __自定义创建__
    * __三维__
      * 几何体：Geometry
      * 顶点：Vector3
      * 顶面：Face3  
    * __二维__
      * 平面：Shape
      * 线条
        * 直线：moveTo、lineTo 
        * 圆弧：arc、absarc 
        * 椭圆：ellipse、absellipse 
        * 贝塞尔曲线：quadraticCurveTo
      * 几何体          
        * makeGeometry
        * createPointsGeometry
        * createSpacedPointsGeometry   

  ```js
  function initShape(){

      // 模型对象和材质对象 
      var geometry, material;   


      // -------------- 构造函数创建 ---------------

      // 矩形平面：PlanGeometry(长,宽, 长的分割, 宽的分割) 
      let planeGeometry = new THREE.PlaneGeometry(100, 50)
      let planeMaterial = new THREE.MeshLambertMaterial({ color: 0xffffff })
      let plane = new THREE.Mesh(planeGeometry,planeMaterial)
      plane.receiveShadow = true        // 接收阴影
      plane.rotation.x = -0.5*Math.PI   // x 轴旋转 -90 度
      plane.position.set(15, 0, 0)
      scene.add( plane )
      
      
      // 圆平面：CircleGeometry(半径, 切片数, 开始, 跨过角度)  
      // 切片数越大则越接近圆, 参数为3(最小值)是三角形, 参数为4则是矩形
      geometry = new THREE.CircleGeometry(3, 100)
      material = new THREE.MeshLambertMaterial({ color: 0xffffff })
      let circle = new THREE.Mesh(geometry, material)


      // 圆环：TorusGeometry(半径, 管道半径, 纬度分割, 经度分割, 圆环面的弧度)
      // 最后一个参数 控制是否绘制完整一个圆环
      geometry = new THREE.TorusGeometry(5, 1, 50, 50)  
      material = new THREE.MeshLambertMaterial({ color: 0x7777ff })
      let torus = new THREE.Mesh(geometry, material)

      
      // 立方体：BoxGeometry(长, 宽, 高, 长的分割, 宽的分割, 高的分割)
      // 旧版本用CubeGeometry
      geometry = new THREE.BoxGeometry(4, 4, 4)
      material = new THREE.MeshLambertMaterial({ color: 0xff0000 })
      let box = new THREE.Mesh(geometry, material)

      
      // 球体：SphereGeometry(半径, 经度切片, 纬度切片, 经度开始, 经度跨过, 纬度开始, 纬度跨过)
      geometry = new THREE.SphereGeometry(4, 20, 20)
      material = new THREE.MeshLambertMaterial({ color: 0x7777ff } )
      let sphere = new THREE.Mesh(geometry, material)
      sphere.scale.set(1.5,1.5,1.5);  //缩放
      
          
      // 圆柱：CylinderGeometry(顶部面积, 底部面积, 高, 圆分割, 高分割, 是否没有顶面和底面) 
      // 顶部或底部为0，则变成圆锥
      geometry = new THREE.CylinderGeometry(2, 2, 5)
      material = new THREE.MeshLambertMaterial({ color: 0x7777ff })
      let cylinder = new THREE.Mesh(geometry, material)
      

      // 正 20 面体 
      meometry = new THREE.IcosahedronGeometry(4, 0);   
      material = new THREE.MeshLambertMaterial( {color: 0x7777ff} )
      let icosahedron = new THREE.Mesh(geometry, material)


      // -------------- 自定义创建 ---------------

      // 点模型
      geometry = new THREE.Geometry();    // 声明一个空几何体对象
      var p1 = new THREE.Vector3(10, 0, 0);   // 顶点1坐标
      var p2 = new THREE.Vector3(0, 20, 0);   // 顶点2坐标
      var p3 = new THREE.Vector3(15, 15, 0);  // 顶点3坐标
      geometry.vertices.push(p1,p2,p3);       // 顶点坐标添加到geometry对象

      material = new THREE.PointsMaterial({  // 点对象像素的颜色尺寸
          color: 0x0000ff,
          size: 10.0       
      }); 
      var points = new THREE.Points(geometry, material);
      scene.add(points);        // 点模型添加到场景中


      // 线条
      geometry = new THREE.Geometry();
      var p1 = new THREE.Vector3(10, 0, 0);
      var p2 = new THREE.Vector3(0, 20, 0);
      geometry.vertices.push(p1, p2);           // vertices 存放坐标
      
      var color1 = new THREE.Color(0xFF0000);  
      var color2 = new THREE.Color(0x0000FF);   
      geometry.colors.push(color1, color2);      // colors 存放顶点颜色
      
      material = new THREE.LineBasicMaterial({
          // color: 0x0000ff,        // 线条颜色，用16进制表示，默认白色
          vertexColors: THREE.VertexColors,  // 线条材质是否使用顶点颜色
      });
      var line = new THREE.Line(geometry, material);
      scene.add(line);  


      // 三角面和矩形 
      geometry = new THREE.Geometry(); 
      var p1 = new THREE.Vector3(0, 0, 0); 
      var p2 = new THREE.Vector3(80, 0, 0); 
      var p3 = new THREE.Vector3(0, 80, 0); 

      // 创建三角面及其法向量 (平面的法向量垂直于平面本身)
      geometry.vertices.push(p1, p2, p3); 
      var normal = new THREE.Vector3( 0, 0, 1 ); 
      var face = new THREE.Face3( 0, 1, 2, normal); 
      var color1 = new THREE.Color(0xFF0000);  // 红色
      var color2 = new THREE.Color(0x00FF00);  // 绿色
      var color3 = new THREE.Color(0x0000FF);  // 蓝色
      face.vertexColors.push(color1, color2, color3);  // 定义顶点颜色
      geometry.faces.push(face); 

      // 创建矩形平面
      var p4 = new THREE.Vector3(0, 80, 0); 
      geometry.vertices.push(p1, p2, p3, p4); 
      var normal = new THREE.Vector3(0, 0, 1); 
      var face0 = new THREE.Face3(0, 1, 2, normal); 
      var face1 = new THREE.Face3(0, 2, 3, normal); 
      geometry.faces.push(face0, face1); 

      material = new THREE.MeshLambertMaterial({
          color: 0x0000ff,         
          side: THREE.DoubleSide   // 两面可见
      });
      var mesh = new THREE.Mesh(geometry,material);
      scene.add(mesh);


      // 二维图形：ShapeGeometry(shapes, options)  
      // shapes 参数: THREE.Shape对象可单个传入, 多个时传数组
      var x = 0, y = 0
      var heartShape = new THREE.Shape()  // 心形

      heartShape.moveTo( x + 5, y + 5 )
      heartShape.bezierCurveTo( x + 5, y + 5, x + 4, y, x, y )
      heartShape.bezierCurveTo( x - 6, y, x - 6, y + 7,x - 6, y + 7 )
      heartShape.bezierCurveTo( x - 6, y + 11, x - 3, y + 15.4, x + 5, y + 19 )
      heartShape.bezierCurveTo( x + 12, y + 15.4, x + 16, y + 11, x + 16, y + 7 )
      heartShape.bezierCurveTo( x + 16, y + 7, x + 16, y, x + 10, y )
      heartShape.bezierCurveTo( x + 7, y, x + 5, y + 5, x + 5, y + 5 )

      geometry = new THREE.ShapeGeometry( heartShape )
      material = new THREE.MeshBasicMaterial( { color: 0x00ff00 } )
      var mesh = new THREE.Mesh( geometry, material ) 
      scene.add( mesh )

      
      // 几何体 
      // 1.创建顶点
      var vertices = [
          new THREE.Vector3(0,2,0),
          new THREE.Vector3(-1,0,0),
          new THREE.Vector3(0,0,1),
          new THREE.Vector3(1,0,0),
          new THREE.Vector3(0,0,-1),
          new THREE.Vector3(0,-2,0)
      ]

      // 2.创建面
      var faces = [
          new THREE.Face3(0,1,2),
          new THREE.Face3(0,2,3),
          new THREE.Face3(0,3,4),
          new THREE.Face3(0,4,1),
          new THREE.Face3(5,2,1),
          new THREE.Face3(5,3,2),
          new THREE.Face3(5,4,3),
          new THREE.Face3(5,1,4),
      ]

      // 3.创建几何体架构
      var geom = new THREE.Geometry()
      geom.vertices = vertices
      geom.faces = faces
      geom.computeFaceNormals()  //计算法向量, 这决定了对光做出的反应

      // 4.添加材质
      var materials = [
          // 几何体面的材质 (Lambert材质适合光照)
          new THREE.MeshLambertMaterial({ 
              opacity: 0.8, 
              color:0x44e144, 
              transparent: true 
          }),
          // 一个几何体框架的材质
          new THREE.MeshBasicMaterial({
              color:0x008800,
              wireframe:true
          }),
      ]

      // 5.创建几何体(传入结构, 材质)
      mesh = THREE.SceneUtils.createMultiMaterialObject(geom, materials) 
      mesh.name = '源'
      mesh.children.forEach(function (e) {
          e.castShadow = true  //可以生成影子
      })

      // 6.添加进场景中
      scene.add(mesh);
  }
  ```



### 材质
> 三维物体表面各个可视属性（颜色、纹理、光滑度、透明度、反射率、反光度等）的结合，决定了物体的外观效果。THREE.Material 是材质的基类，拥有所有材质的公有属性，它派生出的各种材质如下：

  * __MeshBasicMaterial__
    * 名称：基础材质 
    * 功能
      * 显示几何体的线框
      * 给几何体赋予一种简单的颜色 
    * 特点
      * 材质颜色如果没有指定则随机
      * 物体颜色始终是材质颜色，不会因光照产生明暗、阴影效果
  * __MeshLambertMaterial__
    * 名称：朗伯材质
    * 功能：创建 暗淡的、不光亮的 表面
    * 特点
      * 可以通过 emissive 属性给材质添加亮色
      * 在物体表面均匀地散射灯光，比如纸的表面粗糙但亮度均匀
  * __MeshPhongMaterial__  
    * 名称：Phong 式材质
    * 功能：创建金属类光亮的表面
    * 特点
      * 反光强度更大
      * 会给表面添加金属光泽
      * 可通过 shininess 属性改变反光强度
  * __MeshStandardMaterial__
    * 名称：网格标准材质
    * 功能：创建 暗淡的、有金属性光泽的 表面
    * 特点
      * MeshLambertMaterial、MeshPhoneMaterial 的结合
      * 同时具有 粗糙度和金属性 的材质，并且可以改变这些属性
  * __MeshDepthMaterial__
    * 名称：深度材质
    * 功能：根据内容所在的深度对网格对象的灰度级别从黑到白绘制
  * __MeshNormalMaterial__
    * 名称：法向材质
    * 功能：根据面的 法线或朝向 使用不同的颜色来渲染网格的面
  * __MeshFaceMaterial__
    * 名称：面材质
    * 功能：一个可以为几何体的各个表面指定不同颜色的容器 
  * __ShaderMaterial__
    * 名称：着色器材质
    * 功能：允许使用自定义的着色器程序，比如顶点的放置方式、像素的着色方式
  * __LineBasicMaterial__
    * 名称：直线基础材质
    * 功能：用于在 THREE.Line 中创建着色的直线  
  * __LineDashMaterial__
    * 名称：虚线材质
    * 功能：允许创建出一种虚线的效果
  * __PointsMaterial__
    * 名称：粒子材质
    * 功能：适用于粒子系统、模拟雪花小雨等
  * __SpriteMaterial__
    * 名称：雪碧材质
    * 功能：加载 图像纹理、canvas 创建的纹理等
    * 特点：能够使用纹理贴图，并且应用于雪碧材质上

          

## 光源
> THREE.Light 是所有光源的基类，其构造函数接受 hex(一个16进制的颜色值)作为参数。但要让光源具有颜色之外的特性。我们需要使用由基类派生出来的其他种类光源，注意环境光、方向光的光强不受距离远近影响 

### 类型
  * __AmbientLight__
    * 名称：环境光
    * 功能：弱化阴影、添加颜色
    * 光线：来自各个方向并均匀反射到物体表面
    * 特点
      * 影响整个场景
      * 没有特定来源
      * 不会影响阴影的生成
      * 不能作为场景中唯一的光源
  * __DirectionLight__
    * 名称：平行光、方向光
    * 功能：常用于实现阴影效果
    * 光线：一组没有衰减的平行光线，类似太阳光
    * 特点：产生阴影、光强相同、效果只与方向有关
  * __PointLight__
    * 名称：点光源
    * 光线：单点发光但辐射四面八方
    * 特点：类似蜡烛和灯泡、单点发光、不产生阴影
  * __SpotLight__
    * 名称：聚光灯      
    * 功能：实现阴影效果，类似台灯、手电筒
    * 光线：从一个锥体中射出并在物体上产生聚光的效果
    * 特点
      * 距离越远则光强越弱
      * 光强衰减指数越大则光强衰减越快


### 影响材质
> 只有在正常的光照条件下，我们才能容易分辨物体的材质

  * 没有任何光源时，材质颜色最终是黑色 (离开光时材质无法体现)
  * Lambert 材质会受环境光的影响而呈现环境光的颜色，与材质本身颜色关系不大
  * 只有环境光时，灯光位置不影响材质效果(阴影等)。只有其他光源时，能照射到的地方为 光源颜色，不能照射的地方则呈现暗色(黑色)
  * 当混合光源存在时，单一光源能照射的地方呈现为 对应的光源颜色，多种光源都能照射到的地方是 颜色相加结果，都不能照射到的则呈现 暗色/环境光颜色
    * 光源：环境光为 绿色(0x00FF00)，平行光为 红色(0xFF0000)
    * 效果：平行光照射到的地方呈现黄色(颜色值相加：0xFFFF00)，其它呈现绿色


### 阴影效果
  ```js
  function initShadow() { 
      // 告诉render我要渲染阴影
      renderer.shadowMap.enabled = true

      // 让物体(立方体)投射阴影
      var geo = new THREE.BoxGeometry(10,10,10)
      var mat = new THREE.MeshPhoneMaterial({color:0xffffff})
      var box = new THREE.Mesh(geo,mat)
      box.castShadow = true

      // 让其他物体(平面)接受阴影
      var geo_2 = new THREE.PlaneGeometry(100,50,32,32)
      var mat_2 = new THREE.MeshPhoneMaterial({color:0xeeeeee, side:THREE.DoubleSize})
      var plane = new THREE.Mesh(geo_2, mat_2)
      plane.receiveShadow = true

      // 告诉光源需要投射阴影
      var light = new THREE.DirectionalLight(0xffffff)
      light.castShadow = true 
      scene.add(light)    
  }
  ```


## 渲染器
  * __WebGLRender__：使用 WebGL 渲染图形
  * __WebGLRenderTarget__：渲染一个缓冲区
  * __WebGLRenderTargetCube__：用于 CubeCamera
  * __CSS3DRenderer__：基于 CSS3，简化版为 2D
  * __SVGRenderer__：使用 SVG 渲染几何数据


## 控制器
  * __FlyControls__：飞行控制，用键盘和鼠标控制相机的移动和转动
  * __OrbitControls__：轨道控制器，模拟轨道卫星旋转平移并用键盘、鼠标控制相机位置
  * __PointerLockControls__：指针锁定，鼠标离开画布依然能被捕捉到鼠标交互而常用于游戏
  * __TrackballControls__：轨迹球控制器，通过键盘和鼠标控制前后左右平移和缩放场景
  * __TransformControls__：变换物体控制器，可以通过鼠标对物体的进行拖放等操作



