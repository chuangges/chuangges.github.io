---
title: TypeScript 基础语法
tags:
  - TypeScript
categories: TypeScript
top: false
keywords:
  - typescript
date: 2019-04-10 14:04:28
description: 入门简介、数据类型、接口、类、函数
---

# 一、入门简介
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


## 开发调试

  1. __安装工具__
    * IDE：推荐使用 `VSCode`
    * TS：`npm install -g typescript`
  2. __编译方式__
    * 手动编译：`tsc test.ts`
    * 自动编译：可以通过 vscode 配置直接编译、webpack/gulp 配置打包时编译。
    ```js
    // 1、vscode
    构建任务：ctrl + shift + b
    运行任务：build (编译一次)、watch (变动就编译)

    // 2、webpack.config.js：需要先安装 ts-loader
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
  3. __配置编译选项__
    ```js
    // tsconfig.json：项目根目录执行 `tsc --init`
    {
      "compilerOptions": {
        // 编译后生成的 JS 标准（es3、es5、es2015、ES2016)
        "target": "es6",
        // 代码模块加载器 (commonjs、AMD、es2015)
        "module": "commonjs",
        // 编译 js 时删除注释
        "removeComments": false,
        // false：编译器无法判断变量使用判断类型时使用 any 类型
        // true：进行强类型检查，编译器无法判断出变量类型时会报错
        "noImplicitAny": false,
        // 生成的 js、js.map 的目录
        "outDir": "./dist",
        // 允许在 ts 文件中打断点
        "sourceMap": true,
        // 允许编译 js 文件
        "allowJs": true
      },
      // 需要排除编译的目录
      "exclude": ["node_modules"],
      // 需要编译的 ts 文件：* 表示文件匹配、** 表示忽略文件的深度问题
      "include": [
        "./src/*.ts",
        "./src/**/*.ts"
      ]
    }
    ```
  4. __配置调试环境__
    * 编辑 `.vscode/launch.json`：点击左侧第三个图标、设置按钮
    * 文件左侧添加断点，选择 launch.json 对应的 name，点击启动按钮
    * 原理分析：启动调试时，VSCode 首先执行 preLaunchTask，将 ts 编译成 js、sourcemap，然后根据 program、outFiles 参数找到编译后的待调试 js 文件执行。至于 ts、js 的断点信息，则是根据 sourcemap 相互定位的。
    ```json
    "configurations": [
      {
        "type": "node",
        "request": "launch",
        "name": "Typescript",
        "program": "${workspaceFolder}/src/index.ts",
        "preLaunchTask": "tsc: build - tsconfig.json",
        "sourceMaps": true,
        "outFiles": [
          "${workspaceFolder}/**/*.js"
        ]
      }
    ]
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
  * 新增类型：Tuple(元组)、Enum(枚举)、Any(任意)、Void(空值)、Never(永不存在的值)。

  ```ts
    let num: number;
    let str: string;
    let isDone: boolean = false;

    num = '123';     // 报错
    str = 123;       // 报错

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



# 三、类型方法

## 类型断言
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


## 类型判断
  * typeof：判断变量类型。
  * instanceof：判断方法或接口类型。

  ```ts
  var s: string = 'great';
  console.log(typeof s === 'string');
  var a: A = new A();
  console.log(a instanceof A);
  ```


## 类型推论
> TS 会在没有明确的指定类型时推测出一个类型。

  ```ts
  // 报错：变量类型推断为 string
  let x = 3;
  x = '3';

  // 正确：变量类型推断为 any
  let y;
  y = 'seven';
  y = 7;

  // 最佳通用类型：根据赋值推论 (number | null)[]
  const x = [0, 1, null];

  // 上下文类型：根据变量所在位置的上下文进行推论
  window.onmousedown = function(mouseEvent: any) {
    // 编译通过，如果不指定 mouseEvent 类型则会报错
    console.log(mouseEvent.button);
  }
  ```


## 类型兼容性
> 基于结构子类型，而结构类型是一种只使用其成员来描述类型的方式，它与名义类型形成对比。在基于名义类型的类型系统中，数据类型的兼容性或等价性是通过明确的声明或类型名称来决定的，而结构性类型系统是基于类型的组成结构，它并不要求明确地声明。


## 基本规则
> 具有相同的属性：如果 x 要兼容 y，那么 y 至少具有与 x 相同的属性。

  ```ts
  // 基于结构类型系统的 TS 会检查成功，Java 则会报错
  interface Named {
    name: string;
  }
  class Person {
    name: string;
    age: number;
  }
  let p: Named;
  p = new Person();  // 正确
  ```



# 四、接口
> 接口是一个对外的规范和约定，作用是为属性、函数等类型命名和(第三方)代码定义契约。

## 属性类型
> 描述实体：对对象属性进行约束。

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
  p1.x = 5;     // 报错
  ```


## 函数类型
> 描述函数类型：对函数的传入参数和返回值进行约束，注意函数的参数名称不影响函数的匹配。

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
> 描述那些能够通过索引得到的类型：对数组和对象的约束。

  ```ts
  // 约束数组
  interface UserArr{
    [index:number]:string
  }
  var arr:UserArr = ['1', '4'];   // 正确
  var arr:UserArr = [2, 'bb'];    // 报错

  // 只读数组
  interface ReadonlyArr {
    readonly [index: number]: string;
  }
  let arr: ReadonlyArr = ["Alice", "Bob"];
  arr[2] = "Mallory";     // 报错

  // 约束对象
  interface UserObj{
    [index:string]:string;
  }
  var arr:UserObj = {name: 'li'};  // 正确
  var arr:UserObj = {age: 20};     // 报错
  ```


## 类类型
> 行为的抽象：一个类只能继承自另一个类，有时不同类之间存在一些共有的特性，这时就可以把特性提取成接口用来约束类必须存在的属性和方法。接口可以声明一个类的结构(包含属性和方法)，但它只是一个规范和约定，没有访问修饰符和具体方法，也不能被实例化。类是也是声明一个类的结构，但它包含属性和方法，有访问修饰符和具体方法，可以实例化。

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

  // 报错：Image 类型缺少 state 属性
  class Image implements SelectableControl {
    select() { }
  }

  class Location { }
  ```


# 五、类

  * __类 Class__：由静态部分和实例部分组成，定义了一件事物的抽象特点，描述了它的属性和方法，比如猫类。
  * __对象 Object__：继承了类所有属性和方法的实例对象，它是类通过 new 实例化之后生成的具体事物，比如加菲猫。
  * __封装 Encapsulation__：将对数据的操作细节隐藏起来而只暴露对外的接口，外部只通过对外接口就能访问该对象，这样保证了对象内部的数据不会被外部任意更改。
  * __继承 Inheritance__：子类(派生类) 通过 extends 继承 父类(基类)，子类可以拥有除了父类的私有成员和构造函数之外的所有特性，而且还有自身特性。注意 TS 不支持一次继承多个类，但支持多重继承（A 继承 B，B 继承 C）
  * __多态 Polymorphism__：由继承而产生的不同子类，它们对同一个方法可以有不同的响应。
  * __修饰符 Modifiers__：一些用于限定成员性质的关键字。
  * __存取器 getter、setter__：用于改变属性的读取和赋值行为。
  * __抽象类 Abstract Class__：提供其他类继承的的基类（父类）。不允许被实例化。抽象方法只能包含在抽象类中并且必须在子类中实现，非抽象方法可以直接使用而无需重复实现。
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

  // 使用 constructor + super
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
    p.name = "Lisa";   // 报错
    p.age = 24;        // 报错
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
> 抽象类更多的是实现业务上的严谨性，而接口更多的是制定各种规范。需要注意的是，它们都无法实例化，但是有很大区别：`抽象类只能作为父类被继承而接口可以当做子类继承其他类、抽象类中非抽象方法可以包含具体实现但接口中不能包含具体实现、抽象类中的抽象方法在子类中必须实现但接口中的非可选项在接口被调用时必须实现`。

  ```ts
  // 抽象类：可以使用修饰符、部分实现方法
  abstract class Animal{
    public name:string;
    constructor(name:string){
      this.name = name;
    }

    // 抽象方法：不包含具体实现，要求子类中必须实现此方法
    abstract eat():any;

    // 非抽象方法：无需要求子类实现或重写
    run(){
      console.log('非抽象方法，不要子类实现、重写');
    }
  }
 
  class Dog extends Animal{
    // 子类中必须实现父类抽象方法，否则 ts 编译报错
    eat(){
      return this.name + "吃肉";
    }
  }

  var dog = new Dog("tom");
  console.log(dog.eat());
  ```


## 高级技巧

### 构造函数
> 通过 typeof 获取 Greeter 类的类型，即构造函数的类型而不是实例的类型，这个类型包含了类的所有静态成员和构造函数。

  ```ts
  class Greeter {
    static greeting = 'Hello, there'
    greet() {
      return Greeter.greeting
    }
  }

  let g: Greeter
  g = new Greeter()
  console.log(g.greet())  // Hello, there

  // 
  let greeterMaker: typeof Greeter = Greeter
  greeterMaker.greeting = 'Hey there'
  let g2 = new greeterMaker()
  console.log(g2.greet())     // Hey there
  ```

### 把类当做接口使用
> 类定义会创建两个东西：类的实例类型和一个构造函数，类可以创建出类型而可以在允许使用接口的地方使用类。

  ```ts
  class Point {
    x: number
    y: number
  }
  // extends 使子类可以共享父类的属性
  interface Point3d extends Point {
    z: number
  }

  let p: Point3d = {x: 1, y: 2, z: 3}
  ```


# 六、函数

## 定义
> 函数的类型由传入参数和返回值组成，它们都需要指定数据类型。参数类型只要匹配即可而不会验证参数名是否正确，没有返回值时必须指定类型为 void 而不能留空。

  ```ts
  // 函数声明
  function sum(x: number, y: number): number{
    return x + y;
  }

  // 函数表达式
  let sum = function(x: number): number {
    return x + 1;
  }

  // 完整函数类型: => 表示函数的定义，左右两侧分别是输入和输出类型
  let sum: (a: number) => number = function(x: number): number {
    return x + 1;
  }

  // 通过接口定义
  interface Search {
    (source: string, name: string): boolean
  }
  let s: Search;
  s = function(source: string, name: string) {
    return source.search(name) !== -1
  }
  ```

## 分类
  ```ts
  // 没有参数没有返回值
  function run1():void{
    console.log(1)
  }

  // 没有参数有返回值
  function run2():string{
    return '2'
  }

  // 有参有返回值
  function run3(name:string, age:number):string{
    return `${name}: ${age}`
  }

  // 有参没有返回值
  function run4(name:string,):void{
    console.log(name)
  }

  // 函数永远不会返回
  function bar(): never {
    throw new Error('never reach');
  }
  ```


## 参数
  ```ts
  // 可选参数：必须放在最后面
  function fn(firstName: string, lastName?: string){
    if(lastName){
      return firstName + lastName;
    }else{
      return firstName;
    }
  }

  // 默认参数：不用放到最后
  function fn(name: string, age:number=20){
    return `${name}: ${age}`
  }

  // 剩余参数：ES6 规定 rest 参数只能是最后一个参数
  function sum2(...list: number[]): number{
    var sum = 0;
    for(var i =0;i<list.length;i++){
      sum += list[i]
    }
    return sum;
  }
  ```

## 重载
> `允许一个函数接受不同数量或类型的参数，并作出不同的处理`。TS 会优先从最前面的函数定义开始匹配，所以多个函数定义如果有包含关系，需要优先把精确的定义写在前面 (输入数字则输出数字)。

  ```ts
  // 反转数字或字符串
  function reverse(x: number): number;
  function reverse(x: string): string;
  function reverse(x: number | string): number | string{
    if(typeof x === 'number'){
      return Number(x.toString().split('').reverse().join(''));
    }else if(typeof x === 'string'){
      return x.split('').reverse().join('');
    }
  }
  ```
泛型就是解决 类 接口 方法的复用性、

# 七、泛型
> 一种约束宽泛的类型，它在定义函数、接口或类时不指定参数的数据类型而在使用时指定，用来创建支持不定数据类型的可复用代码 (泛型函数、泛型接口，泛型类)。

## 泛型变量
> 变量名可以自定义，数量和使用方式对应即可。

  ```ts
  // 定义一个类型变量
  function info<T>(arg: T): T {
    return arg;
  }

  // 定义多个类型变量
  function info<N, A>(name: N, age: A): [N, A] {
    return [name, age];
  }
  ```


## 泛型函数
> 实现返回和参数相同类型的值：

  ```ts
  // 使用 any：返回任意类型，放弃了类型教验
  function getData(value:any):any{
    return value;
  }
  // 使用泛型：返回相同类型，
  function getData<T>(value:T):T{
    return value;
  }
  ```


## 泛型接口

  ```ts
  // 写法一
  interface ConfigFn{
    <T>(value:T):T;
  }
  var getData:ConfigFn = function<T>(value:T):T{
    return value;
  }
  getData<string>('张三');
  getData<string>(1243);    // 报错

  // 写法二
  interface ConfigFn<T>{
    (value:T):T;
  }
  function getData<T>(value:T):T{
    return value;
  }
  var myGetData:ConfigFn<string> = getData;
  myGetData('20');
  myGetData(200);     // 报错
  ```


## 泛型类
> 指类实例部分的类型，所以类的静态属性不能使用类名后的泛型类型。使用泛型实现获取数组最小值时只需要定义一个类：

  ```ts
  class MinClass<T>{
    public list:T[]=[];
    add(num:T):void{
      this.list.push(num);
    }
    min():T{
      var minNum = this.list[0];
      for(var i =0; i<this.list.length; i++){
        if(minNum > this.list[i]){
          minNum = this.list[i]
        }
      }
      return minNum;
    }
  }
  var m = new MinClass<number>();
  m.add(1);
  m.add(6);
  m.add(2);
  console.log(m.min());
  var s = new MinClass<string>();
  s.add('a');
  s.add('f');
  s.add('b');
  console.log(s.min());
  ```


## 泛型约束

  ```ts
  // 1、泛型继承接口

  // 直接实现无法保证所有的参数都有 length 方法
  function getLen<T>(arg:T):T{
    return arg.length
  }
  // 增加接口来约束泛型：没有 length 属性的参数在函数调用前编译无法通过
  interface lengthRequire{
    length:number
  }
  function newGetLen<T extends lengthRequire>(arg:T):T{
    return arg.length
  }

  // 2、使用类型参数：改写 getProperty
  function getProperty<T, K extends keyof T>(obj: T, key: K) {
    return obj[key]
  }
  let x = {a:1, b:2, c:3, d:4};
  getProperty(x, "a");   // 正常
  getProperty(x, "m");   // 异常

  // 3、使用类类型
  function create<T> (c: {new(): T;}): T {
    return new c();
  }
  ```


# 八、枚举
> 可以声明一组带名字的常量，用于取值被限定在一定范围内的场景，比如一周只能有七天、颜色限定为红绿蓝等。

## 根据枚举成员分类

### 数字枚举
> 枚举成员是常量而非变量，所以不能对其进行赋值但可以初始化。它们经过编译后会生成反向映射表，即除了生成键值对的集合，还会生成值键对的集合。

  * __不带初始化器__：枚举成员默认从 0 开始并依次递增
    ```ts
    enum Days { Sun, Mon, Tue, Wed, Thu, Fri, Sat };
    let d: string = Days[0];     // Sun
    let i: number = Days.Sat;    // 6

    // 编译结果
    { 
      0: "Sun", 1: "Mon", 2: "Tue", 3: "Wed", 4: "Thu",
      5: "Fri", 6: "Sat", Fri: 5, Mon: 1, Sat: 6, Sun: 0,
      Thu: 4, Tue: 2, Wed: 3 
    }

    // 报错：不带初始化器的枚举需要被放在第一位置或初始化的枚举后面
    enum E {
      A = getValue(),
      B
    }
    ```
  * __使用初始化器__：后面的枚举成员从初始值依次递增
    ```ts
    enum Days { Sun=7, Mon, Tue, Wed=1, Thu, Fri, Sat };

    // 编译结果
    { 
      1: "Wed", 2: "Thu", 3: "Fri", 4: "Sat", 7: "Sun", 
      8: "Mon", 9: "Tue", Sun: 7, Mon: 8, Tue: 9, Wed: 1, 
      Thu: 2, Fri: 3, Sat: 4, Sun: 7, Mon: 8, Tue: 9 
    }

    // 初始值：常数、常量表达式，不能是变量、NaN、Infinity
    enum Days_1 { one = 2, two }
    enum Days_2 { one = Days_1.Sun, two = 2 * one }
    function getNum (x: number): number {
      return x
    }
    enum Days_3 {
      one = getNum(10) * Days_2.one,
      two = (function () { return 1 })()
    }
    // 报错
    const first = 10
    enum Days_4 { one = first, two, three }
    ```


### 字符串枚举
> 需要使用字符串字面量或者之前定义的字符串枚举成员来初始化，注意它不会生成反向映射表。

  ```ts
  // 全部使用字符串字面量来初始化
  enum StrEnum1 {
    one = 'one',
    two = 'two'
  }

  // 全部使用其他枚举成员的字面量初始化
  enum StrEnum2 {
    one = StrEnum1.one,
    two = StrEnum1.two
  }

  // 报错：不能将这初始化方式混写
  enum StrEnum3 {
    one = 'first',
    two = StrEnum1.two
  }
  ```


### 异构枚举
> 混合字符串和数字成员，一般不推荐使用。

  ```ts
  enum BooleanLike {
    No = 0,
    Yes = "YES"
  }
  ```


## 根据声明方式分类

  * __普通枚举__：可以生成反向映射表。
  * __常量枚举__：不会生成反向映射，节省了代码量但只能通过成员访问。
  * __外部枚举__：不会生成反向映射，主要用于防止枚举的命名冲突和成员冲突。
  * __外部常量枚举__：和常量枚举没有区别，只是会提示是否有枚举命名冲突和成员冲突。

  ```ts
  // 普通枚举：关键词 + 枚举名称
  enum NumEnum { a, b c }

  // 常量枚举：const + 关键词 + 枚举名称 
  const enum Color { Red, Green, Blue }
  Color['0']     // 报错
  Color.Green    // 1

  // 外部枚举：declear + 关键词 + 枚举名称
  declear enum Animal { cat, dog, tiger }
  console.log(Animal)       // 报错：不存在
  console.log(Animal.cat)   // 报错

  // 外部常量枚举：declear + const + 关键词 + 枚举名称
  // 声明语 + 修饰符 + 关键词 + 枚举名称
  declear const enum Animal2 { cat=1, dog, tiger }
  Animal2.tiger      // 3
  ```


















