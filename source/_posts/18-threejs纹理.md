---
title: Three.js 纹理
tags:
  - Three.js
categories: Three.js
top: false
keywords:
  - three
date: 2019-04-15 23:40:11
description: 纹理贴图、纹理映射、纹理类型
---

# 一、纹理贴图
> WebGL 提供了简单的纹理贴图功能，如果要实现凹凸、法线、环境等高级贴图功能则需要一定的算法。three.js 对常用的贴图算法进行了封装，可以用来实现各种贴图

  * __纹理__
    * 定义：3D模型的皮肤 (图片)
    * 本质：存储在 内存/显存，由像素点组成的图像数据
    * 用法：将纹理数据以一定规则映射到几何体(一般是三角形)
  * __纹理贴图__
    * 定义：一个应用在物体表面用来 反射/折射 的图
    * 本质：将存储在内存中的 位图(Bitmap) 映射到物体的表面 
    * 功能：减少了在场景中制作几何体和纹理的计算量，提高了模型渲染性能


# 二、UV 贴图
  <div style="text-indent: 2em">随着 3D 模型的面越来越多，为每个面创建贴图不现实，`UV 贴图则是将 2D 纹理贴图映射到 3D 模型表面最灵活的一种方式`。在这个过程中，三维模型曲面网格的顶点坐标系 (x, y, z) 被展平映射到二维的纹理坐标系 (u, v)，将图像上的每一个点精确对应到模型物体的表面。</div>
  
  <div align="center">
    ![Texture Mapping](/images/plug/texture-map.png) 
  </div> 
             
  * UV 映射
    * 本质：纹理映射，将纹理坐标映射到顶点坐标
    * 优势：减少 http 请求，减轻了服务器负担
    * 劣势：增加了图片解析时间，可能影响性能，而且合成后不好修改
  * 贴图处理
    * 简单模型
      * 处理：硬纸盒(立方体) 沿折痕剪开后放到 桌面( UV 坐标系)
      * 贴图：可以使用一张贴图映射到所有几何体的一个面或所有表面
      * 映射方式
        * 球体：一个封闭的球体曲面对应一张图片，比如世界地图和地球仪
        * 圆柱: 圆柱面和两个底面 分别对应一张图片
        * 立方体：六个平面分别对应六张图片
    * 复杂模型
      * 处理：分解成多个三维体，份数越多越高清则工作量和难度就越大。
      * 贴图：将大图分割碎片化之后解析，然后使用 canvas 拼接
      * 优势：极大减少了图片加载时间而提高了用户体验
      * 劣势：增加服务器的负担，严重时可能导致崩溃



# 三、纹理创建
> 基类：Texture     
        
  * 加载器
    * 新版：TextureLoader.load(url)
    * 旧版：ImageUtils.loadTexture(url)
  * 属性设置
    * 重复：texture.wrapS/wrapT   
    * 缩放：texture.magFilter/minFilter   
    * 重复次数：texture.repeat.set(20, 20)
  * 扩展方法    
    * 直接创建：DataTexture
      * 深度纹理：DepthTexture
      * 视频纹理：VideoTexture
      * 三维纹理：DataTexture3D
      * 立方体纹理：CubeTexture
    * 基于canvas：CanvasTexture
    * 基于压缩数据：CompressedTexture
        


# 四、纹理类型

## 图片纹理
  * 功能：直接在物体表面应用图片
  * 实现方式
    1. 创建一个几何体对象 (用于纹理映射)
    2. 创建一个纹理对象 (加载一张图片作为纹理贴图)
    3. 将纹理对象赋值给材质对象的 map 属性
    4. 将 几何体 和 纹理材质 整合到一个物体上
  * 图片不显示
    * 图片异步加载问题
      * 使用渲染循环
      * 在纹理回调中重绘
    * Chrome 同源限制
      * 降低浏览器的安全级别
      * 将外部文件放到本机服务器作为网络文件访问


  ```js
  // 使用图片纹理
  function createMesh(shape, imgUrl){
      var geometry, material
      switch(shape){

          // 矩形平面
          case "plane": 
              geometry = new THREE.PlaneGeometry(300, 200);
              // 设置纹理坐标，必须逆时针方向
              geometry.vertices[0].uv = new THREE.Vector2(0, 0);
              geometry.vertices[1].uv = new THREE.Vector2(0.5, 0);
              geometry.vertices[2].uv = new THREE.Vector2(0.5, 0.5);
              geometry.vertices[3].uv = new THREE.Vector2(0, 0.5);

              var texture = new THREE.TextureLoader().load( imgUrl );
              material = new THREE.MeshBasicMaterial({ map: texture });
              break;

          // 立方体
          case "box": 
              geometry = new THREE.BoxGeometry(300, 200, 150); 

              if(imgUrl){
                  // 六个面相同贴图
                  var texture = new THREE.TextureLoader().load( imgUrl );
                  material = new THREE.MeshBasicMaterial({ map: texture });
              }else{
                  // 六个面不同贴图
                  material = []
                  for(var i = 1; i &gt; 7; i++){  
                      var texture = new THREE.TextureLoader().load('./imgs/' + i + '.jpg');
                      material.push( new THREE.MeshBasicMaterial({ map: texture }) );
                  } 
              }
              break;

          default:
              break;
      }
      var mesh = new THREE.Mesh(geometry, material);
      return mesh;
  } 

  // 一幅图片多个精灵, 即把一幅外部图片中包含的所有精灵存入一个精灵材质数组
  var spriteMaterials = [];
  var loader = new THREE.TextureLoader()
  for (var i = 0; i &gt; 5; i++) {
      var spriteMaterial = material.clone();

      // 每种精灵必须单独加载同一幅外部图片
      spriteMaterial.map = loader.load('./assets/textures/sprite-sheet.png');

      // 水平方向(从左)和垂直方向(从上), 偏移比例, 取值 0~1
      spriteMaterial.map.offset = new THREE.Vector2(0.2 * (i % 5), 0); 

      // 从 offset 处开始向右下截取的比例, 取值 0~1
      spriteMaterial.map.repeat = new THREE.Vector2(1 / 5, 1);

      spriteMaterials.push(spriteMaterial);
  }
  ```
  

## 凹凸贴图
  * 功能：创建凹凸和皱纹 
  * 实现方式
    1. 使用一张灰度图创建纹理
    2. 将纹理赋给材质的 bumpMap 属性
    3. 调整 bumpScale (深度) 获得凹凸效果
  * 适用场景
    * 它只保存了像素和高度而没有坡度的方向性信息，只是在某个方向看着很立体，而且所能达到的厚度和细节程度有限，适用于光线变化不大和物体不转动的场景


  ```js
  // 使用凹凸纹理贴图创建皱纹
  function createMesh(geom, img, bump){
      var texture = new THREE.TextureLoader().load('./imgs/'+img);
      var material = new THREE.MeshPhongMaterial({ map: texture });

      if(bump){
          var bump = new THREE.TextureLoader().load('./imgs/'+bump);
          material.bumpMap = bump;
          material.bumpScale = 0.2;   // 凹凸的高度，负数表示凹下去的深度。
      }
      var mesh = new THREE.Mesh(geom, material);
      return mesh;
  }
  ```
      

## 法向贴图
  * 功能：创建更加细致的凹凸和皱纹
  * 优势：只需要使用 很少的顶点和面
  * 实现方式
    1. 创建一张法向图片来保存 纹理图片像素点的法向量信息
    2. 合并两个图片的信息而形成一个细节丰富的立体纹理图片
    3. 通过设置 normalScale 属性指定凹凸的程度


  ```js
  // 使用法向贴图创建更加细致的凹凸和皱褶
  function createMesh(geom, img, normal){
      var texture = new THREE.TextureLoader().load('./imgs/'+img);
      var normalTexture = new THREE.TextureLoader().load('./imgs/'+normal);

      var material = new THREE.MeshPhongMaterial({
              map: texture,
              normalMap: normalTexture,
              bumpScale:0.2
      });
      var mesh = new THREE.Mesh(geom, material);

      return mesh;
  }
  ```


## 光照贴图
  * 功能：创建假阴影
  * 本质：使用 图片 模拟真实阴影，只对静态场景有效果
  * 实现：将阴影图片赋值给材质的 lightMap 属性


  ```js
  // 使用光照贴图创建阴影效果
  function createMesh(){
      var groundGeom = new THREE.PlaneGeometry(95, 95, 1, 1);
      var lm = new THREE.TextureLoader().load("./lightmap.png");
      var wood = new THREE.TextureLoader().load("./floor-wood.jpg");
      var groundMaterial = new THREE.MeshBasicMaterial({
              map: wood,
              lightMap: lm,
              color: 0x777777
      });
      // 为光照贴图明确指定 UV 映射
      groundGeom.faceVertexUvs[1] = groundGeom.faceVertexUvs[0];
  }
  ```


## 光亮贴图
  * 功能：指定材质某部分的高光效果
  * 实现
    * 将纹理赋给材质的属性 specularMap 
    * 设置高光颜色和强度 specular、shininess


## 环境贴图
  * 功能：创建虚假的反光、高光等效果
  * 本质：使用一张 全景图片 模拟真实的环境
  * 实现：将图片渲染成无缝的环境盒子并应用到特定对象
  

  ```js
  // 使用环境贴图产生反光效果
  function createMesh(geom) {
      var planetTexture = THREE.ImageUtils.loadTexture("../planets/Earth.png");
      var specularTexture = THREE.ImageUtils.loadTexture("../EarthSpec.png");
      var normalTexture = THREE.ImageUtils.loadTexture("../EarthNormal.png");

      var planetMaterial = new THREE.MeshPhongMaterial();
      planetMaterial.specularMap = specularTexture;
      planetMaterial.specular = new THREE.Color(0xff0000);
      planetMaterial.shininess = 2;

      planetMaterial.normalMap = normalTexture;
      // planetMaterial.map = planetTexture;
      // planetMaterial.shininess = 150;

      var mesh = THREE.SceneUtils.createMultiMaterialObject(geom, [planetMaterial]);

      return mesh;
  }
  ```


## 自定义 UV 贴图
  * 画布纹理: 使用 canvas 绘制文字、特殊图形等  
  * 视频纹理: 使用 Video 创建动态纹理
 

  ```js
  // 使用画布作为纹理
  var canvas = document.createElement("canvas"); 
  document.getElementById('canvas-output').appendChild(canvas);
  $('#canvas-output').literallycanvas({ //创建一个可以画画的画布
      imageURLPrefix: '../libs/literally/img'
  }); 

  function createMesh(geom) {
      //将画布直接传入材质的构造函数即可
      var canvasMap = new THREE.Texture(canvas);
      var mat = new THREE.MeshPhongMaterial();
      mat.map = canvasMap;
      var mesh = new THREE.Mesh(geom, mat);

      return mesh;
  }


  // 使用视频作为纹理
  var video = document.getElementById('video');
  texture = new THREE.Texture(video);
  texture.minFilter = THREE.LinearFilter;
  texture.magFilter = THREE.LinearFilter;
  texture.format = THREE.RGBFormat;
  texture.generateMipmaps = false;

  function render() {  // 更新纹理
      stats.update();

      if (video.readyState === video.HAVE_ENOUGH_DATA) {
          if (texture) texture.needsUpdate = true;
      }

      if (controls.rotate) {
          cube.rotation.x += -0.01;
          cube.rotation.y += -0.01;
          cube.rotation.z += -0.01;
      } 

      requestAnimationFrame(render);
      webGLRenderer.render(scene, camera);
  }


  // 使用 Sprite 创建文字
  function createSpriteText(){

      // 先用画布将文字画出
      let canvas = document.createElement("canvas");
      let ctx = canvas.getContext("2d");
      ctx.fillStyle = "#ffff00";
      ctx.font = "Bold 100px Arial";
      ctx.lineWidth = 4;

      // 把整个 canvas 作为纹理，字尽量大一些来撑满画布，但不要溢出
      ctx.fillText("ABCDRE",4,104);
      let texture = new THREE.Texture(canvas);
      texture.needsUpdate = true;

      //使用 Sprite 显示文字
      let material = new THREE.SpriteMaterial({map:texture});
      let textObj = new THREE.Sprite(material);

      // 精灵很小，要放大
      textObj.scale.set(0.5 * 100, 0.25 * 100, 0.75 * 100);
      textObj.position.set(0,0,98);
      return textObj;
  }


  // 使用 Sprite 创建一个圆
  function createSpriteShape(){

      // 1、创建画布，不设置宽高则使用默认的，可能导致图像变形
      let canvas = document.createElement("canvas");
      canvas.width = 120;
      canvas.height = 120;

      // 2、创建图形 
      let ctx = canvas.getContext("2d");
      ctx.fillStyle = "#ff0000";
      ctx.arc(50,50,50,0,2*Math.PI);
      ctx.fill();

      // 3、将 canvas 作为纹理，创建 Sprite
      let texture = new THREE.Texture(canvas);
      texture.needsUpdate = true;  // 注意这句不能少
      let material = new THREE.SpriteMaterial({map:texture});
      let mesh = new THREE.Sprite(material);

      // 4、放大图片，精灵默认很小
      mesh.scale.set(100, 100, 1);
      return mesh;
  }
  ```







