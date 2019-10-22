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
    * 运行任务：编译 (编译一次)、监控 (变动就编译)
  * __项目集成__
    1. 项目根目录新建 tsconfig.json：`tsc --init`
    2. 配置编译选项：注意必须配置 outDir，常用项如下
      * target：编译后生成的 JS 标准（es3、es5、es2015、ES2016)。
      * module：代码模块加载器 (commonjs、AMD、es2015)。
      * outDir：编译后生成的 JS 文件的存放目录。
      * include、exclude：编译时需要包含/剔除的文件夹。
    3. webpack 配置打包时编译：需要先安装 ts-loader
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


# 二、基础语法

## 数据类型
> 类型只是一组值。JS 的所有类型都是动态的，你可以在运行时使用它们。TS 为 JS 额外带来了静态类型，可以在编译期间进行静态检查而无需运行代码。

  * JS 类型
    * Undefined：唯一元素 undefined(未定义) 的集合
    * Null：唯一元素 null(空) 的集合
    * Boolean：两个元素 false、true 的集合。
    * Number：所有数字的集合。
    * String：所有字符串的集合。
    * Symbol：所有符号的集合。
    * Object：所有非原始类型的集合(包括函数和数组)。
  * TS 类型
    * JS 类型：`Boolean、Number、String、Array、Null、Undefined、Object`。
    * 新增类型：`Tuple(元组)、Enum(枚举)、Any(任意值)、Void(空值)、Never(永不存在的值)`。
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
  * 类型断言
    * 告诉编译器跳过数据检查。
    * 使用 JSX 时只允许 as 语法断言。
    ```ts
    // 一、“尖括号”语法
    let someValue: any = "this is a string";
    let strLength: number = (<string>someValue).length;

    // 二：as 语法
    let someValue: any = "this is a string";
    let strLength: number = (someValue as string).length;
    ```
  * 变量声明
    * var：可以重复声明和赋值、变量提升、函数作用域。
    * let：可以重复赋值、暂时性死区、块级作用域。
    * const：不能重新赋值、暂时性死区、块级作用域。



## 接口



























