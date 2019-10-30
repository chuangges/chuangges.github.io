---
title: TypeScript 入门
tags:
  - TypeScript
categories: TypeScript
top: false
keywords:
  - typescript
date: 2019-04-10 14:04:28
description: 简单介绍
---

# 一、简单介绍
<div style="text-indent: 2em">TypeScript 是微软推出的一种静态强类型语言，设计初衷就是为了`帮助 JavaScript 的开发人员能够像 c#、Java 等高级语言那样编写代码`，比如使用高级语言的强类型、面向对象、语法检查、代码编译等特性。它是 JavaScript 的超集(基于 JS 并拓展了其语法)，填充了 JS 作为动态弱类型语言的缺点，能够对代码中的错误及时反馈，而且包含一个编译器可以将 TS 代码转换为原生 JS，非常适用于需要团队合作的大型项目和封装库文件 (比较严谨)。</div>


## 语言类型

  * __动态类型语言__：在运行期间检查数据类型的语言。永远不需要指定变量的数据类型，该语言会在变量被首次赋值时在内部记录数据类型。
  * __静态类型语言__：在编译期间检查数据类型的语言。变量在使用前要声明数据类型，好处是把类型检查放在编译期，可以提前检查可能出现的类型错误。
  * __强类型语言__：强制数据类型定义的语言。变量只要不经过强制转换就不会改变数据类型，不允许隐式的类型转换。
  * __弱类型语言__：数据类型可以被忽略的语言。变量声明后可以改变数据类型，而且允许编译器进行隐式的类型转换。


## 主要优势

  * __代码质量__：编译期的静态类型检查加上 IDE 的智能提示，可以尽可能的将 BUG 消灭在编译器，线上运行时质量更稳定可控。
  * __开发效率__：代码重构时更改某个全局变量名，JS 不能实现而 TS 可以轻松做到。
  * __代码可读性__：类型声明本身是最好查阅的文档，适合团队协作。
  * __代码可维护性__：TS 对 ECMAScript 新特性支持的更全面，不需要安装插件即可直接使用。


## 出现原因

  1. __JavaScript 发展迅速__
  2. __我们需要强类型的 JavaScript__
    * 强类型特性可以防止编写 JS 代码时因为数据类型的转换造成的意外错误。
    * 比如函数的参数必须是数字类型，使用原生 JS 时会正常执行而并不会提示错误，这样会增加项目开发的潜在风险。
  3. __按需输出 JavaScript 版本__
    * JS 版本现在几乎每年都有更新，兼容性的脚本对于开发者是一个比较大的挑战。
    * TypeScript 解决了版本问题，你可以按需输出 JS 脚本，比如 ES3、ES5 、ES6 。
  4. __代码标准化利于团队开发__
    * 由于 JS 本身语言的特点和其版本迭代太快的原因，团队成员使用 JS 时很容易随意发挥而不受规范约束。
    * 团队当然可以针对不同版本的 JS 做出使用规范并引入 eslint 等质量检测插件，整理这些内容并推广使用比较浪费时间，还不如基于 TypeScript 并整理一套标准来应对 JS 版本不断的更新迭代。
  5. __主流框架及最新特性的支持__
    * Angular 等主流框架都已经集成 TypeScript 来解决版本兼容性和弱语言的特点。
    * TypeScript 紧跟 JS 的发展，比如 ES7、ES8、ES9 的新特性都支持，比浏览器支持的速度更快。这就意味着你能用最新的语言特性，编写质量更高的 JS。


## 使用方式
> 通过编辑器可以配置自动编译，通过 vue 等支持 ts 的框架可以通过 loader 配置让 webpack 打包时编译。

  * __手动编译__
    * 安装工具：`npm install -g typescript`
    * 本地编译：`tsc test.ts`
  * __使用 IDE__
    * 工具：vscode (已全局安装 typescript)
    * 构建任务：`ctrl + shift + b`
    * 运行任务：编译 (编译一次)、监控 (变动就编译)。
  * __项目集成__
    1. 项目根目录新建 tsconfig.json：`tsc --init`
    2. 配置编译选项：注意必须配置 outDir，常用项如下：
      * target：编译后生成的 JS 标准（es3、es5、es2015、ES2016)。
      * module：代码模块加载器 (commonjs、AMD、es2015)。
      * outDir：编译后生成的 JS 文件的存放目录。
      * include、exclude：编译时需要包含/剔除的文件夹。
    3. webpack 配置打包时编译：需要先安装 ts-loader。
      ```js
      // webpack.config.js：
      module.exports = {
          entry: "./src/index.ts",
          output: {
            filename: "./dist/bundle.js",
          },
          resolve: {
            extensions: ['.ts', '.js', '.json']
          },
          devtool: "source-map",
          module: {
            rules: [{
              test: /\.tsx?$/,
              loader: 'ts-loader'
            }]
          }
      }
      ```


# 二、数据类型
> 类型只是一组值。JS 的所有类型都是动态的，你可以在运行时使用它们。TS 为 JS 额外带来了静态类型，可以在编译期间对值所具有的结构进行类型检查而无需运行代码。

## JS 类型
  * Undefined：唯一元素 undefined(未定义) 的集合。
  * Null：唯一元素 null(空) 的集合。
  * Boolean：两个元素 false、true 的集合。
  * Number：所有数字的集合。
  * String：所有字符串的集合。
  * Symbol：所有符号的集合。
  * Object：所有非原始类型的集合(包括函数和数组)。


## TS 类型
  * JS 类型：Boolean、Number、String、Array、Null、Undefined、Object。
  * 新增类型：Tuple(元组)、Enum(枚举值)、Any(任意值)、Void(空值)、Never(永远不会存在的值)。

  ```ts
    let num: number;
    let str: string;
    let isDone: boolean = false;

    num = '123';     // 错误
    str = 123;       // 错误

    // Array：各元素的类型必须相同
    var arr:boolean[] = [true, false];  // 通过元素类型后面添加 []
    var arr:Array<number> = [6, 2, 3];  // 使用数组泛型：Array<类型>

    // Tuple：一个有不同数据类型的数组，参数和类型一一对应 (不能缺少)
    let x:[string, number];
    x = ['hello', 10];  // OK
    x = [10, 'hello'];  // Error
    x[3] = 'world';     // OK，访问越界元素时使用联合类型(str、num)

    // Enum：通过语义化的单词来代表某一状态，相比数值更加直观和易读
    // 枚举值为 数字或字符串，默认从 0 开始递增，赋值后后面的值递增
    enum Status {success = 1, error = 2, cancel, wait};
    let flag:Status = Status.success;  // 枚举值 1
    console.log(Status[2]);            // 根据枚举值获取变量名

    // Any：相当于关闭类型检查，尽量不用
    let notSure:any = 4;
    notSure = false;
    let list:any[] = [1, true, "free"];
    list[1] = 100;

    // Void：只能用于函数的返回值，表示该函数没有返回值
    function warnUser():void {
      console.log("This is my warning message");
    }

    // Null、Undefined：默认是所有类型的子类型，可以赋值给任意类型
    let u: number = undefined
    let n: null = null

    // Never：抛出异常或没有返回值的函数返回值类型，是所有类型的子类型
    function error(message: string): never {
      throw new Error(message);
    }
    function move(direction: "up" | "down") {
      return direction === "up" ? 1 :
          direction === "down" ? -1 :
          error("永远不会执行到");
    }

    // Object：表示非原始类型
    let obj:object = { name: 'Wang', age: 25 };
    let obj:{ name: string, age: number } = { 
      name: "Wang", 
      age: 25
    }
    ```


## 类型方法

* __类型断言__
  * 用来指定一个值的类型，注意指定的必须是其子类型，比如不能指定数字为 string。
  * 类似类型转换但并不进行数据检查和解构，它只在编译阶段起作用而运行时没影响。
  ```ts
  // 两种实现方式如下，注意使用 JSX 时只允许 as 语法
  let val: any = "hello";
  let len_1: number = (<string>val).length;
  let len_2: number = (val as string).length;

  // 实例：将一个联合类型的变量指定为一个更加具体的类型
  function getLength(something: string | number): number {
    // 类型断言不是类型转换，不能断言成一个联合类型中不存在的类型
    if ((<string>something).length) {
        return (<string>something).length;
    } else {
        return something.toString().length;
    }
  }
  ```
* 类型判断
  * typeof：判断变量类型。
  * instanceof：判断方法或接口类型。
  ```ts
  var s: string = 'great';
  console.log(typeof s === 'string');
  var a: A = new A();
  console.log(a instanceof A);
  ```



# 三、接口
> 接口是一个对外的规范和约定，作用是为属性、函数等类型命名和(第三方)代码定义契约。

## 属性类型
> 对对象属性的约束。

  ```ts
  // 确定属性：变量的个数和类型必须和接口的完全匹配
  interface Person {
    name: string;
    age: number;
  }
  let tom: Person = {
    name: 'Tom',
    age: 25
  }

  // 可选属性：？表示可选
  interface Person {
    name: string;
    age?: number;
  }
  let tom: Person = {
    name: 'Tom'
  }

  // 任意属性：确定属性和可选属性都必须是它的子属性
  interface Person {
    age: number;
    // 报错，可将 string 改为 any
    [propName: string]: string;
  }
  let tom: Person = {
    age: 20,
    gender: 'male'
  }

  // 只读属性
  interface Point {
    readonly x: number;
    readonly y: number;
  }
  let p1: Point = { x: 10, y: 20 };
  p1.x = 5;     // 错误
  ```


## 函数类型
> 对方法的传入参数和返回值进行约束，注意函数的参数名称不影响函数的匹配。

  ```ts
  // 加密：参数和返回值都为字符串
  interface encrypt{
    (key:string, value:string):string;
  }

  var md5:encrypt = function(key:string, value:string):string{
    return key + value;
  }
  var sha1:encrypt = function(key:string, value:string):string{
    return key + '---' + value;
  }

  console.log(md5('name', 'lisi'));
  console.log(sha1('name','wanwu'));
  ```


## 可索引类型
> 对数组和对象的约束。

  ```ts
  // 约束数组
  interface UserArr{
    [index:number]:string
  }
  var arr:UserArr = ['1', '4'];   // 正确
  var arr:UserArr = [2, 'bb'];    // 错误

  // 只读数组
  interface ReadonlyArr {
    readonly [index: number]: string;
  }
  let arr: ReadonlyArr = ["Alice", "Bob"];
  arr[2] = "Mallory";     // 错误

  // 约束对象
  interface UserObj{
    [index:string]:string;
  }
  var arr:UserObj = {name: 'li'};  // 正确
  var arr:UserObj = {age: 20};     // 错误
  ```


## 类类型
> 一个类只能继承自另一个类，有时不同类之间存在一些共有的特性，这时就可以把特性提取成接口用来约束类必须存在的属性和方法。接口可以声明一个类的结构(包含属性和方法)，但它只是一个规范和约定，没有访问修饰符和具体方法，也不能被实例化。类是也是声明一个类的结构，但它包含属性和方法，有访问修饰符和具体方法，可以实例化。

### 实现接口
> 接口约束类的属性或方法，类具体实现接口规范。

  ```ts
  // TS 只会检查类的公共属性和方法，不会检查私有部分
  interface Animall {
    curTime: Date;
  }
  class Clock implements ClockInterface {
    currentTime: Date     // 注释后会报错
    constructor(h: number, n: number) {
    }
    setTime(d: Date) {
      this.curTime = d;
    }
  }

  // TS 只会检查类的实例部分，而不会检查静态部分(构造器)
  interface ClockConstructor {
    // 接口匹配不到任何类型，会报错
    new (hour: number, minute: number);
  }
  class Clock implements ClockConstructor {
    currentTime: Date;
    constructor(h: number, m: number) { }
  }
  ```

### 继承接口
> 实现接口的分割和重用。

  ```ts
  // 动物接口
  interface Animal {
    name: string;
    eat: () => void;
  }

  // 猫科接口
  interface Felidae {
    claw: number;
    run: () => void;
  }

  // 让猫类实现 Animal 和 Felidae 两个接口
  class Cat implements Animal, Felidae {
    name: string;
    claw: number;

    eat() {
      console.log('tag', 'I love eat Food!');
    }

    run: () {
      console.log('tag', 'My speed is very fast!')
    }
  }

  const dog: Dog = new Dog();
  dog.eat();
  ```

### 混合类型
> 一个对象可以为函数和变量使用。

  ```ts
  // Counter 接口既服务于 getCounter 方法，也服务于 counter 对象
  interface Counter {
    (start: number): string;
    interval: number;
    reset(): void;
  }
  function getCounter(): Counter {
    let counter = <Counter>function (start: number) { };
    counter.interval = 123;
    counter.reset = function () { };
    return counter;
  }

  let c = getCounter();
  c(10);
  c.reset();
  c.interval = 5.0;
  ```

### 接口继承类
> 接口可以继承类的成员但是不会去实现。

  ```ts
  // 只有子类才能去实现私有属性 state 
  class Control {
    private state: any;
  }
  // 其他类如果要实现 SelectableControl 接口，必须是 Control 的子类
  interface SelectableControl extends Control {
    select(): void;
  }

  class Button extends Control implements SelectableControl {
    select() { }
  }

  class TextBox extends Control {
    select() { }
  }

  // 错误：Image 类型缺少 state 属性
  class Image implements SelectableControl {
    select() { }
  }

  class Location { }
  ```


# 四、类
> 类是抽象的，它描述了一些属性和方法，比如猫类。类通过实例化之后生成具体的对象，比如加菲猫

  * __类 Class__：定义了一件事物的抽象特点，包含它的属性和方法。
  * __对象 Object__：类的实例，类通过 new 实例化之后生成的具体对象。
  * __封装 Encapsulation__：将对数据的操作细节隐藏起来而只暴露对外的接口，外部只通过对外接口就能访问该对象，这样保证了对象内部的数据不会被外部任意更改。
  * __继承 Inheritance__：子类(派生类) 通过 extends 继承 父类(基类)，子类可以拥有除了父类的私有成员和构造函数之外的所有特性，而且还有自身特性。注意 TS 不支持一次继承多个类，但支持多重继承（A 继承 B，B 继承 C）
  * __多态 Polymorphism__：由继承而产生的不同子类，它们对同一个方法可以有不同的响应。
  * __修饰符 Modifiers__：一些用于限定成员性质的关键字。
  * __存取器 getter、setter__：用于改变属性的读取和赋值行为。
  * __抽象类 Abstract Class__：供其他派生类继承的基类，它不允许被实例化，它的抽象方法不包含具体实现并且必须在派生类中实现。
  * __接口 Interfaces__：不同类之间公有的属性或方法可以抽象成一个接口。接口可以被类实现（implements）。一个类只能继承自另一个类，但是可以实现多个接口。


## 定义
  ```ts
  class Person{ 
    // 属性
    name:string;
    // 构造函数
    constructor(val:string){ 
      this.name = val;
    }
    // 类的方法
    getInfo(age:number=20):string{ 
      return this.name + ": " + age;
    }
  }
  // 实例化创建一个对象
  var p = new Person("龙梅子");
  console.log(p.getInfo());
  ```


## 继承

  ```ts
  // 不使用 constructor
  class Person{
    name:string;
  }
  class Student extends Person{
    getInfo(age:number=20){
      return this.name + ": " + age;
    }
  }
  var s = new Student();
  s.name = '李四';
  console.log(s.getInfo());

  // 使用 constructor + super (父类有，则子类必须有)
  class Person{
    name:string;
    constructor(name:string){
      this.name = name;
    }
    getInfo(){
      return this.name;
    }
  }
  class Student extends Person{
    age:number;
    constructor(name:string, age:number){
      super(name);
      this.age = age;
    }
    getInfo(){
        let superMsg = super.getInfo();
        return superMsg + ":" + this.age;
    }
  }
  var s = new Student("李四", 20);
  console.log(s.getInfo());
  ```


## 修饰符

  1. 访问
    * __private__：私有，只能在当前类的内部访问。
    * __protected__：受保护，只能在当前类内部及其子类可以访问。
    * __public__：公有(默认)，在当前类内部、子类、类外部都可以访问。
    ```ts
    class Person{
      private name:string;
      protected sex:string;
      age:number;
      constructor(name:string, age:number, sex:string){
        this.name=name;
        this.age=age;
        this.sex=sex;
      }
      run(){
        return `我是${this.name}我${this.age}岁`
      }
    }
    class Student extends Person{
      constructor(name:string, age:number, sex:string){
        super(name, age, sex)
      }
      run1(){
        console.log(this.name);   // 报错
        console.log(this.sex);    // 正确
        console.log(this.age);    // 正确
      }
    }
    var p = new Person("张三", 23, "男");
    console.log(p.name);     // 报错
    console.log(p.sex);      // 报错
    console.log(p.age);      // 正确
    ```
  2. 只读 readonly
    ```ts
    // 设置的只读属性必须在声明时或构造函数里被初始化，并且值不能被修改。
    class Person { 
      readonly name: string;
      readonly age: number=23;
      constructor(name:string) {
        this.name = name;
      }
    }
    let p = new Person("yzq");
    p.name = "Lisa";   // 错误
    p.age = 24;        // 错误
    ```
  3. 静态 static
    ```ts
    // 静态属性和方法存在于类本身而非实例上，所以访问时需要直接通过类名。
    class Person {
      static name: String; 
      static age: number;
      constructor(name: String, age: number){ 
        this.name = name; 
        this.age = age; 
      }
      // 静态方法不能访问类里面的非静态属性
      static printInfo() {
        console.log(Person.name + ": " + Person.age)
      }
    }
    Person.printInfo()
    ```


## 存取器
  ```ts
  // 自定义存取数据之前的业务逻辑
  class Person {
    private age: number;
    get age() {
      return this._age;
    }
    set age(inputAge: number=20) {
      if (inputAge > 0 && inputAge <> 100) {
          this._age = inputAge;
      }
    }
  }
  let p = new Person()
  p.age = 200
  console.log(p.age)  // 20
  ```


## 抽象类
    ```ts
    // abstract 用于定义抽象类和其中的抽象方法

    ```





  




