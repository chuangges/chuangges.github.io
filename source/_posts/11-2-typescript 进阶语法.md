---
title: TypeScript 进阶语法
tags:
  - TypeScript
categories: TypeScript
top: false
keywords:
  - typescript
date: 2019-11-06 16:06:20
description: 类型检查、高级类型、命名空间、声明合并、装饰器、JSX
---


# 一、类型检查
> TS 的类型约束对象为变量、函数的参数和返回值，比如字面量类型约束变量、void 和 never 约束函数返回值等。

## 类型断言
  * 实现：`<类型> 值、值 as 类型`，注意使用 JSX 时只允许 as 语法。
  * 用来指定一个值的类型而使 TS 假设已经类型检查，注意指定的必须是其子类型。
  * 类似类型转换但并不进行数据检查和解构，它只在编译阶段起作用而运行时没影响。

  ```ts
  // 实现
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


## 类型推断
> 没有明确指定变量的类型时，TS 会根据某些规则自动推断出类型，常见场景有：声明变量时没有指定类型、函数默认参数、函数返回值。

  ```ts
  let a;         // 推断为 any
  let b = 1;     // 推断为 number
  let c = [];    // 推断为由 any 类型构成的数组
  let d = (x=1) => x+1;  // 函数默认参数被推断为 number，返回值也会被推断
  let e = [1, null];     // 推断出兼容所有数据的类型：number、null 的联合类型

  a = 'seven';
  a = 7;
  b = '3';  // 报错

  // 上下文类型：根据变量所在位置的上下文进行推论
  window.onmousedown = function(mouseEvent: any) {
    // 编译通过，如果不指定 mouseEvent 类型则会报错
    console.log(mouseEvent.button);
  }

  // TS 推论有时不符合预期，可以通过类型断言进行覆盖。
  interface Foo{
    bar: number
  }
  let foo: Foo = {} as Foo;  // 约定对象按照接口规范
  foo.bar = 1;               // 实现对象的具体定义
  ```


## 类型兼容
* TS 允许类型相互兼容的变量（函数、类等结构）相互赋值

### 兼容规则
> 基于(成员的)结构类型，与通过明确的声明或类型名称决定兼容性的名义类型形成对比。基础规则为：函数之间是参数多的兼容参数少的、结构之间则是成员少的兼容成员多的，两个兼容的类型之间必须具有共同的属性。

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


### 兼容实例
> 如果类型 Y 可以被赋值给类型 X 时，则认为 X 兼容 Y，X 为目标类型，Y 为源类型。

  * 函数
    ```ts
    // 形参需要包含关系，参数名可以不相同
    let x = (a: number) => 0;
    let y = (b: number, s: string) => 0;
    y = x;    // 正确
    x = y;    // 报错

    // 返回类型需要被包含关系
    let x = ()=>({name: 'Alice'});
    let y = ()=>({name: 'Alice', age:20});
    y = x;    // 报错
    x = y     // 正确

    // 可选参数和剩余参数的兼容
    let a1 = (p1: number, p2: number) => {};
    let b1 = (p1?: number, p2?: number) => {};
    let c1 = (...args: number[]) => {};
    a1 = b1;
    a1 = c1;
    b1 = a1;    // 报错：可选参数不能被兼容

    // 对于有重载的函数，源函数的每个重载都要在目标函数上找到对应的函数签名。
    ```
  * 接口
    ```ts
    interface X {
      a: any;
      b: any;
    }
    interface Y {
      a: any;
      b: any;
      c: any;
    }
    let x: X = {a:1,b:2};
    let y: Y = {a:1,b:2,c:3};
    x = y; // x 兼容 y，成员少的会兼容成员多的
    ```
  * 枚举：枚举类型与数字类型兼容，不同枚举类型之间不兼容。
    ```ts
    enum Status { Ready, Waiting };
    enum Color { Red, Blue, Green };

    let status = Status.Ready;   // 0
    status = Color.Green;        // 报错
    ```
  * 类
    * 比较两个类的对象时，只会比较实例的成员而不比较静态成员和构造函数。
    * 类的私有成员和受保护成员会影响兼容性。当检查类实例的兼容时，如果目标类型包含一个私有成员或受保护成员，那么源类型必须包含来自同一个类的这个成员。这允许子类赋值给父类，但是不能赋值给其它有同样类型的类。
    ```ts
    class Animal {
      feet: number;
      constructor(name: string, numFeet: number) { }
    }
    class Size {
      feet: number;
      constructor(numFeet: number) { }
    }
    let a: Animal;
    let s: Size;
    a = s;         // 正确
    s = a;         // 正确
    ```
  * 泛型：类型参数只影响使用其做为类型一部分的结果类型。
    ```ts
    interface NotEmpty<T> { 
      data: T;   // 注释掉则正确
    }
    let x: NotEmpty<number>;
    let y: NotEmpty<string>;
    x = y;       // 报错
    ```


### 子类型与赋值
> TS 有两种类型的兼容性：子类型与赋值。它们的区别在于，赋值扩展了子类型兼容，允许和 any 来回赋值，以及 enum 和对应数字值之间的来回赋值。实际上，类型兼容性是由赋值兼容性来控制的，包括 implements、extends 语句。


## 类型保护
> 通过类型判断等方式在特定语句块中确定变量的类型，从而可以放心地访问它的属性和方法。TS 支持的四种方式如下：

  * in：判断一个属性/方法是否属于某对象，比如 `a in obj`。
  * typeof：判断基本类型，比如 `typeof b === 'number'`。
  * instanceof：判断一个实例是否属于某个类，比如 `x instanceof ClassX`。
  * 类型保护函数：当判断逻辑复杂时，可以自定义返回值是一个 类型谓词的判断函数。

  ```ts
  // 自定义类型保护函数
  let name: string | undefined;
  if(typeof name === "string") {
      console.log(name.length)
  }

  // 类型保护函数：类型谓词为 name is Type
  function isFish(pet: Fish | Bird): pet is Fish {
    return (<Fish>pet).swim !== undefined;
  }
  if (isFish(pet)) {  // pet 是 Fish 类型
      pet.swim();
  }
  else {
      pet.fly();
  }
  ```

## 类型别名
> 对已知的一些类型通过 type 定义新名字以避免重复。它和接口的区别如下：

  * 类型别名不能使用 extends、implement。
  * 接口创建了一个新名字，可以在其他任何地方使用。类型别名并不创建新名字。
  * 如果无法通过接口描述一个类型而需要使用联合/元组类型，则一般可以选择类型别名。

  ```ts
  type Name = string;
  type NameResolver = () => string;
  type NameOrResolver = Name | NameResolver;
  function getName(n: NameOrResolver): Name {
    if (typeof n === 'string') {
        return n;
    }
    else {
        return n();
    }
  }

  // 泛型
  type Container <T> =  {value: T};

  // 属性中引用
  type Tree <T> = {
    value: T;
    left: Tree <T>;   
    right: Tree <T>;
  }

  // 配合交叉类型使用
  type LinkedList <T> = T & { next: LinkedList <T> };
  interface Person {
    name: string
  }
  var people: LinkedList<Person>;
  var s = people.name;
  var s = people.next.name;

  // 报错：类型别名不能出现在声明右侧的任何地方
  type Yikes = Array<Yikes>;
  ```


# 二、高级类型

## 交叉类型
> 通过 `&` 获取多个类型的并集，与继承的区别是继承可以有自己的属性而交叉没有。

  ```ts
  function extend<T, U>(first: T, second: U): T & U {
    const result = <T & U>{};
    for (let id in first) {
        (<T>result)[id] = first[id];
    }
    for (let id in second) {
      if (!result.hasOwnProperty(id)) {
          (<U>result)[id] = second[id];
      }
    }
    return result;
  }

  const x = extend({ a: 'hello' }, { b: 42 });

  // 现在 x 拥有了 a、b 属性
  const a = x.a;
  const b = x.b;
  ```


## 联合类型
> 通过 `|` 获取多个类型中的一个，具体类型需要等到赋值时根据类型推论的规则推断出。

  ```ts
  // 类型限定
  let a: number | string = "a";
  // 取值限定
  let b: "a" | "b" | "c";
  function fn(name: string, age: string | number){ }

  a = 'hello';
  a = 20        // 报错：已经推断出 string
  ```


## 索引类型
> 使用不存在的索引时会返回 undefined 而且没有约束，因此我们需要有对索引的约束。

  ```ts
  let obj = { a: 1, b: 2, c: 3 };

  // 不使用索引类型：
  function getValue(obj: any,keys: string[]){
    return keys.map(key => obj[key]);
  }
  console.log(getValue(obj,["c","f"]))  // 返回 undefined，没有约束

  // 使用索引类型：T[k][] 表示返回值必须是 obj 中的值组成的列表
  function getValue<T,K extends keyof T>(obj: T, keys: K[]): T[K][] { 
    return keys.map(key => obj[key]); // keys 中的元素只能是 obj 中的键
  }
  console.log(getValue(obj,["a","b"]));
  console.log(getValue(obj,["c","f"]));  // 报错
  ```

## 映射类型
> 从旧类型中创建新类型的一种方式，比如将接口中的所有成员变成只读、可选。

  ```ts
  interface Obj{
    a: string;
    b: number;
    c: boolean;
  }

  // 将所有属性变为只读，生成一个新接口
  type ReadonlyObj = Readonly<Obj>;
  type Readonly<T> = {
    readonly [P in keyof T]: T[P];
  }

  // 将所有属性变为可选
  type PartialObj = Partial<Obj>;
  type Partial<T> = {
    [P in keyof T]?: T[P];
  }

  // 获取原类型的子集：两个等同
  type PickObj = Pick<Obj,'a'|'b'>;
  interface PickObj {
    a: string,
    b: number
  }
  ```

## 条件类型
> 指由表达式所决定的类型，它使类型具有了不唯一性并增加了语言的灵活性。

  * 实例
    ```ts
    // 如果类型 T 可以被赋值给类型 U，name 结果就赋予 X 类型，否则赋予 Y 类型
    T extends U ? X : Y;

    type TypeName<T> =
      T extends string ? string :
      T extends number ? number :
      T extends boolean ? boolean :
      T extends undefined ? undefined :
      T extends Function ? Function :
      object;
    type T1 = TypeName<string>;   // 字符串类型
    type T2 = TypeName<string[]>; // object 类型
    type T3 = TypeName<Function>; // function 类型
    type T4 = TypeName<string | string[]>; // string、obj 的联合类型
    ```
  * 应用：类型的过滤
    ```ts
    type Diff<T, U> = T extends U ? never : T;

    // 作用是过滤掉第一个参数中的'a'。T5 为联合类型 'b' | 'c' 
    type T5 = Diff< 'a'|'b'|'c', 'a'|'e' >;

    // 解析过程
    Diff<'a', 'a'|'e'> | Diff<'b', 'a'|'e'> | Diff<'c', 'a'|'e'>
    never | 'b' | 'c'
    'b' | 'c'
    ```
  * TS 内置
    * `Exclude<T, U>`：从 T 中剔除可以赋值给 U 的类型。
    * `Extract<T, U>`：提取 T 中可以赋值给 U 的类型。
    * `NonNullable<T>`：从 T 中剔除 null、undefined。
    * `ReturnType<T>`：获取函数返回值类型。
    * `InstanceType<T>`：获取构造函数类型的实例类型。
    ```ts
    // "b" | "d"
    type T0 = Exclude<"a" | "b" | "c" | "d", "a" | "c" | "f">;

    // "a" | "c"
    type T1 = Extract<"a" | "b" | "c" | "d", "a" | "c" | "f">;

    // string | number
    type T3 = NonNullable<string | number | undefined>;
    ```


## 可以为 null 的类型
> TS 有两种特殊的类型 `null、undefined`，类型检查器默认它们可以赋值给任意类型。`--strictNullChecks` 标记可以解决此错误：当声明一个变量时，它不会自动包含 null、undefined，但是可以使用联合类型明确的包含它们：

  ```ts
  let s = 'foo';
  s = null;        // 报错

  let sn: string | null = 'bar';
  sn = null;       // 正确
  sn = undefined;  // 报错
  ```

### 可选参数/属性
> 使用 strictNullChecks 标记之后默认添加 `| undefined`。

  ```ts
  function f(x:number, y?: number) {
    return x + (y || 0);
  }
  f(1);
  f(1, undefined);
  f(1, null)  // 报错

  class C {
    a: number;
    b?: number;
  }
  let c = new C();
  c.a = undefined;   // 报错
  c.b = undefined;   // 正确
  ```

### 类型保护和断言
> 由于可以为 null 的类型是通过联合类型实现，那么需要使用类型保护来去除 null。如果编译器不能够去除 null/undefined，可以使用类型断言手动去除，语法 `identifier!` 表示从 identifier 的类型里去除了 null、undefined。

  ```ts
  // 使用类型保护来去除 null
  function f(sn: string | null): string {
    if (sn == null) {
        return "default";
    }
    else {
        return sn;
    }
  }
  // 简洁方式
  function f(sn: string | null): string {
    return sn || "default";
  }

  // 使用类型断言手动去除 null/undefined
  function fixed(name: string | null): string {
    function postfix(epithet: string) {
      // 去掉 ! 就会报错
      return name!.charAt(0) + '.  the ' + epithet;
    }
    name = name || "Bob";
    return postfix("great");
  }
  ```


## 字面量类型
> 分为字符串、数字字面量类型，通过指定一个固定值实现类型约束或区分函数重载

  ```ts
  let gender: "男"| "女";   // 只能为是男或女
  let a:"A";               // 只能为 A
  let user: {              // 必须有 name、age
      name: string,
      age: number
  }

  let b:1;
  function getNum(): 1 | 2 | 3 | 4 { }

  // 区分函数重载
  function createElement(tagName: "img"): HTMLImageElement;
  function createElement(tagName: "input"): HTMLInputElement;
  function createElement(tagName: string): Element { }
  ```


## 可辨识联合
> 合并单例类型、联合类型、类型保护和类型别名来创建一个叫做 可辨识联合 的高级模式，它也称为 标签联合、代数数据类型。它在函数式编程很有用处。一些语言会自动地为你辨识联合，TS 则基于已有的 JS 模式。

  * 实现要素
    * 具有普通的单例类型属性：可辨识的特征。
    * 一个类型别名包含了那些类型的联合：联合。
    * 此属性上的类型保护。
    ```ts
    // 将要联合的接口：都有 kind 属性但有不同的字符串字面量类型
    interface Square {
      kind: "square";
      size: number;
    }
    interface Rectangle {
      kind: "rectangle";
      width: number;
      height: number;
    }
    interface Circle {
      kind: "circle";
      radius: number;
    }

    // 使用可辨识联合
    type Shape = Rectangle | Circle | triangle;
    function area(s: Shape) {
      switch (s.kind) {
        case "rectangle": return s.height * s.width;
        case "circle": return Math.PI * s.radius ** 2;
      }
      // 报错：switch 没有包涵所有情况，没有处理 case triangle
    }
    ```
  * 完整性检查：没有涵盖所有可辨识联合的变化时让编译器通知
    * 法一：启用 `--strictNullChecks` 并指定一个返回值类型。
    * 法二：使用 never 类型，用来除去所有可能情况后剩下的类型。
    ```ts
    // 报错：TS 认为函数返回值为 number | undefined
    type Shape = Rectangle | Circle | triangle;
    function area(s: Shape): number {
      switch (s.kind) {
        case "rectangle": return s.height * s.width;
        case "circle": return Math.PI * s.radius ** 2;
      }
    }

    // 报错
    function assertNever(x: never): never {
      throw new Error("Unexpected object: " + x);
    }
    function area(s: Shape) {
      switch (s.kind) {
        case "square": return s.size * s.size;
        case "rectangle": return s.height * s.width;
        case "circle": return Math.PI * s.radius ** 2;
        default: return assertNever(s);
      }
    }
    ```



## 多态的 this 类型
> 表示某个包含类或接口的子类型，称为 `F-bounded 多态性`。它能很容易的表现连贯接口间的继承。

  ```ts
  class BasicCalculator {
    public constructor(protected value: number = 0) { }
    public currentValue(): number {
      return this.value;
    }
    public add(operand: number): this {
      this.value += operand;
      return this;
    }
    public multiply(operand: number): this {
      this.value *= operand;
      return this;
    }
    // other operations go here
  }

  let v = new BasicCalculator(2)
              .multiply(5)
              .add(1)
              .currentValue();
  ```



# 三、命名空间

## 单文件

  ```ts
  namespace Page {
    export interface IPage {
      render(data: object): string;
    }
    export class IndexPage implements IPage {
      render(data: object): string {
        return '<div>Index page content</div>';
      }
    }
  }

  // 使用别名：import a = x.y
  namespace Shapes {
    export namespace Polygons {
      export class Triangle { }
      export class Square { }
    }
  }
  
  import polygons = Shapes.Polygons;
  let sq = new polygons.Square();
  ```


## 文件拆分
> 三斜线指令引用当前目录文件：`/// <reference path="./IPage.ts" />`。

  ```ts
  // IPage.ts
  namespace Page {
    export interface IPage {
      render(data: object): string;
    }
  }

  // IndexPage.ts
  /// <reference path="./IPage.ts" />
  namespace Page {
    export class IndexPage implements IPage {
      render(data: object): string {
        return '<div>Index page content</div>';
      }
    }
  }

  // App.ts
  /// <reference path="./IndexPage.ts" />
  /// <reference path="./DetailPage.ts" />
  let a = new Page.IndexPage().render({value: 'index'})
  ```


## 命名空间和模块
  * __命名空间__
    * 内部模块，通过 namespace 声明的全局对象，主要用于组织代码并避免命名冲突。
    * 命名空间通过三斜线指令引入，相当于源码嵌入，会引入额外变量到当前作用域。
    * 本质是 IIFE，不存在文件即模块的加载机制约束，算是旧时代的产物，不建议使用。
  * __模块__
    * 外部模块的简称，一个文件即一个模块，主要用于代码复用并解决加载依赖关系。
    * 模块通过 import/require 引入，调用时通过变量引用而不会主动影响当前作用域。
    * TS 遵从文件即模块的 ES Module 规范，通过编译可以输出 CommonJS、AMD、UMD 等模块形式，推荐使用。


# 四、声明合并
> 编译器将多个地方具有相同名称的声明合并为一个，合并后的声明同时拥有原先所有声明的特性。TS 声明会创建出声明实体中的一至三类，比如一个 namespace 会创建出 namespace、value，声明实体可以在一定条件下进行合并。


## 声明分类
  * __声明实体__
    * __命名空间 namespace__：指声明的分类而非实体。建立应用时需要通过 `import`，如果使用 `const/let/var` 则会使新的实体不再具有命名空间的属性。
    * __类型 type__：可以描述一个值的形状，它只能存在于命名空间。它可以是写在变量声明时冒号右边的部分，也可以是类型别名的值 (`type Foo = string;`)。
    * __值 value__：编译为 js 后唯一会被保留的一种，命名空间与类型都会被清除。
  * __声明及其实体__
    * __命名空间 (namespace)__：命名空间、值。一个命名空间实体如果不包含值的声明则只属于命名空间一类。
    * __类 (class)__：类型、值
    * __枚举 (enum)__：类型、值
    * __接口 (interface)__：类型
    * __类别名 (type)__：类型
    * __函数 (function)__：值
    * __变量 (let、const、var、parameters)__：值


## 声明合并


### 接口
> 将双方的成员放到一个同名的接口。

  * 对于非函数成员，必须类型一致，否则报错。
  * 对于函数成员，会发生重载，重载的顺序按照以下规则。
    * 接口内部按照先后顺序。接口之间，声明在后的接口函数成员排序更靠前。
    * 如果参数类型为单一的字符串字面量，排名最靠前。后面的接口的排在第一位，前面接口的排在第二位。

  ```ts
  // 接口合并
  interface Box {
    x: number,
    y: string,
  }
  interface Box {
    y: string;
    foo(bar: string[]): string[],
  }
  let box: Box = {
    x: 1,
    y: "15",
    foo(bar: any) { return bar }
  };

  // 注释为合并后的函数排序
  interface StateMerge {
    x: number,
    y: string,
    foo(bar: number): number;   // 4
    foo(bar: string): string;   // 5
    foo(bar: "b"): number;      // 2
  }
  interface StateMerge {
    y: string;
    foo(bar: string[]): string[],  // 3
    foo(bar: "a"): number,         // 1
  }
  ```

### 命名空间
  * 同名的 namespace 也会合并导出的成员。
  * 注意合并后的命名空间成员无法访问非导出成员。

  ```ts
  namespace Box {
    let flag = true;
    export function isFlag() {
      // 合并后会报错：flag 并没有导出
      return flag;
    }
  }
  ```

### 命名空间与类、函数、枚举
  * 可以用来扩展 class、function、Enum。
  * 注意合并时需要将命名空间放在它们的后面，否则会报错。

  ```ts
  // 命名空间和类：相当于为类 C 添加了属性
  class C{
  }
  namespace C{
    export let state = 1
  }
  console.log(C.state);   // 1

  // 命名空间和函数：相当于为函数 Lib 添加了属性
  function Lib(): string {
    return Lib.version;
  }
  namespace Lib {
    export let version = "1.0";
  }
  console.log(Lib());   // 1.0

  // 命名空间和枚举类型
  enum Color {
    red = 1,
    green = 2,
    blue = 4
  }
  namespace Color {
    export function mixColor(colorName: string) {
      if (colorName === 'yellow') {
          return Color.red + Color.green;
      }
      else if (colorName === 'white') {
          return Color.red + Color.green + Color.blue;
      }
      else if (colorName == "magenta") {
          return Color.red + Color.blue;
      }
      else if (colorName == "cyan") {
          return Color.green + Color.blue;
      }
    }
  }
  ```


## 非法合并
> TS 并非允许所有的合并，目前不支持 class 与其它 class、变量合并。模仿类的合并可以参考 TypeScript Mixins (混入)。


# 五、装饰器

  <div style="text-indent: 2em">一种特殊类型的声明，能够被附加到类声明、方法、访问符、属性或参数而用于监视、修改或替换其定义。它可以看作是在原有代码外层包装了一层处理逻辑来增强代码功能，比如对参数的类型判断、对返回值的排序过滤、对函数添加节流、防抖。目前它还属于实验性语法，你必须在命令行或 `tsconfig.json` 启用编译选项：</div>

  ```ts
  // 命令行
  tsc --target ES5 --experimentalDecorators

  // tsconfig.json
  {
    "compilerOptions": {
      "target": "ES5",   // 方法修饰器时需配置为 es6
      "experimentalDecorators": true
    }
  }
  ```


## 定义
> 使用形式为 `@expression`，expression 必须是一个函数。它的本质是代码编译时执行的函数，注意不是 TS 编译而是 JS 执行的编译阶段。

  ```ts
  // hello.ts：执行命令 tsc 编译、node helloword.js
  function helloWord(target: any) {
    console.log('hello Word!');
  }

  @helloWord
  class HelloWordClass { }

  // 多个装饰器可以同时应用到一个声明时，可以在同行或多行
  @f @g x

  @f
  @g
  x
  ```


## 常用类型
> 注意没有函数装饰器，不能使用在声明文件和任何外部上下文。

  ```ts
  // 1、类装饰器
  function addPropsAndMethods<T extends { new(...args: any[]): {} }>(target: T){
    return class extends target {
      word = "Hello World";
      printWord = function () {
        return `${this.word}!`;
      }
    }
  }

  // @addAge(18)
  function addAge(args: number) {
    return function (target: Function) {
      target.prototype.age = args;
    }
  }

  // 2、属性装饰器
  function writableProperty(flag: boolean) {
    // target：对于静态成员是类的构造函数，对于实例成员是类的原型对象。
    return function (target: any, propName: string | symbol) {
      Object.defineProperty(target, propName, {
        writable: flag
      })
    }
  }

  // 3、方法装饰器
  function editableMethod(flag: boolean) {
    return function (target: any, methodName: string | symbol, descriptor: PropertyDescriptor) {
      descriptor.writable = flag;
    }
  }

  // 4、参数装饰器
  function printArgs(target: any, methodName: string | symbol, paramIndex: number): void {
    // paramIndex：参数在函数参数列表中的索引
    console.log(target, methodName, paramIndex);
  }

  // 5、存取器装饰器：TS 不允许为单个成员装饰 get、set 访问器
  function configurable(value: boolean) {
    return function (target: any, propName: string, descriptor: PropertyDescriptor) {
      descriptor.configurable = value;
    }
  }


  @addPropsAndMethods
  class DummyClass{

      // 类的属性默认可以被写入
      @writableProperty(true)
      name:string;
      bornY: number;
      _domain:string;

      constructor(name:string, bornY:number){
        try{
            this.name = name;
        } catch(e) {
            console.log('Error:',e);
        }
        this.bornY = bornY;
      }

      printData(prefix: string, @printArgs printAll:boolean){
        if (printAll){
            console.log(this.name + this.bornY);
        }else{
            console.log(this.name);
        }
      }

      // 不允许修改该方法
      @editableMethod(false)
      dummyMethod(){
        console.log('Hi');
      }

      // 存取器装饰器
      @configurable(false)
      get data() { return this.name+'@'+ this.bornY; }

      @configurable(false)
      set domain(value: string) {
          this._domain = value;
      }
      get domain():string{
          return this._domain;
      }
  }

  let d = new DummyClass("Jack Ma", 1981);

  console.log('类的装饰器：为类添加方法、属性和默认属性值');
  console.log((<any>d).helloWorld());
  console.log('属性的装饰器)');
  console.log('方法的装饰器：让方法不可被修改，下面的修改无效');
  d.dummyMethod();
  d.dummyMethod = function(){
      console.log('Hi there!');
  }

  d.dummyMethod();

  console.log('存取器装饰器');
  console.log(d.data);

  d.domain = "coolwp.com";
  console.log(d.domain);
  ```

## 加载顺序

  * 类装饰器总是最后执行。
  * 方法和方法参数中参数装饰器先执行。
  * 有多个参数装饰器时从最后一个依次向前执行。
  * 方法和属性装饰器，谁在前面谁先执行。因为参数是方法一部分，所以参数会一直紧挨着方法执行。


# 六、JSX
> 全称 `JavaScript XML`，React 发明的一种类似 XML 的 JS 扩展语法，主要用于创建虚拟 DOM 并可以编译为 JS 对象，编译的基本规则为：`< 开头的代码以 HTML 语法解析、{ 开头的代码以 JS 语法解析`。TS 支持嵌入、类型检查和直接编译 JSX 为有效的 JS。

## 基础使用

  * __使用前提__：使用 tsx 文件、启用 jsx 选项。
  * __类型断言__：tsx 文件中的类型断言必须使用 as 操作符，因为使用尖括号表示类型断言时结合 JSX 语法后将带来解析上的困难。
  * __编译模式__：仅影响编译阶段，类型检查不受影响。可以通过在命令行使用 `--jsx` 标记或者在 `tscofnig.json` 文件中指定选项。
    * __preserve__：将保持 JSX 作为部分输出以用于 Babel 等变换时编译。输出 .jsx 文件。
    * __react__：调用 `React.createElement` 而不需要经过 JSX 转换，输出 .js 文件。 
    * __react-native__：相当于保留，因为它保留了所有 JSX。输出 .js 文件。
  * __结果类型__：默认是 any 类型，可以通过指定 `JSX.Element` 接口实现自定义类型，但是不能从接口里检索元素、属性或子元素的类型信息。


## 类型检查
> 假设有一个 JSX 表达式 `<expr />`，expr 可能是固有元素(环境自带的东西，比如 DOM 环境的 div、span)，也可能是基于值的元素(自定义组件)。TS 使用与 React 相同的规范来区别它们：`固有元素总是以一个小写字母开头，基于值的元素总是以一个大写字母开头`。


### 固有元素
> 使用特殊接口 JSX.IntrinsicElements 来查找。如果这个接口没有指定，则不对固有元素进行类型检查而全部通过。如果接口存在，则固有元素的名字需要在接口的属性里查找。

  ```ts
  // 没有 JSX.IntrinsicElements 里指定的就会报错。
  declare namespace JSX　{
    interface IntrinsicElements {
      foo: any
    }
  }
  <foo />    // 正确
  <bar />    // 报错

  // 指定一个用来捕获所有字符串索引
  declate namespace JSX {
    interface IntrinsicElements {
      [elemName: string]: any
    }
  }
  ```


### 基于值的元素
> 在它所在的作用域里按照标识符查找。它有两种定义方式：__无状态函数组件 SFC、类组件__，由于它们在 JSX 表达式里无法区分，TS 解析时会先将表达式作为无状态函数组件，解析成功则完成操作，否则以类组件的形式进行解析，依旧失败则抛出错误。

  ```ts
  import MyCom from './myCom';
  <MyCom />         // 正确
  <OtherCom />      // 报错

  // 无状态函数组件：返回值可以赋值给 JSX.Element
  interface FooProp {
    name: string;
    X: number;
    Y: number;
  }
  declare function AnotherCom(prop: {name: string});  // declare 声明文件或模块
  function ComFoo(prop: FooProp) {
    return <AnotherCom name={prop.name} />;
  }
  const Button = (prop: {value: string}, context: {color: string}) => <button>

  // 类组件：元素的实例类型必须赋值给 JSX.ElementClass，否则抛出错误。
  // 元素类的类型：MyComponent、MyFactoryFn
  // 元素实例的类型： { render: () => void }
  declare namespace JSX {
    interface ElementClass {
      render: any;  // 如果没有 render 方法则会报错。
    }
  }
  class MyComponent {
    render() {}
  }
  function MyFactoryFn() {
    return { render: () => {} }
  }
  <MyComponent />;    // 正确
  <MyFactoryFn />;    // 正确
  ```


### 属性类型检查

  1. 确定元素属性类型
    * 固有元素：JSX.IntrinsicElements 接口中的属性类型。
    * 基于值的元素：元素实例类型上的 JSX.ElementAttributesProperty 指定属性的类型，没有指定则使用类元素构造函数或 SFC 调用的第一个参数的类型。
  2. JSX 里进行属性检查
    * 支持可选属性、必选属性。
    * 如果一个属性名不是个合法的 JS 标识符(比如 data-* 属性)，并且它没有出现在元素属性类型里时不会报错。
    ```ts
    // 固有元素 foo：{bar?: boolean}
    declare namespace JSX {
      interface IntrinsicElements {
        foo: {
          required: string; 
          bar?: boolean 
        }
      }
    }
    <foo required="a" />;
    <foo required="a" bar />;
    // 正确：some-unknown 不是合法的标识符
    <foo required="a" some-unknown />;
    // 报错：缺少必须属性、unknown 不存在
    <foo />;
    <foo required="a" unknown />;

    // 基于值的元素 MyCom：{foo?: string}
    declare namespace JSX {
      interface ElementAttributesProperty {
        props;     // 指定属性名
      }
    }
    class MyCom {
      props: {  // 在元素实例类型上指定属性
        foo?: string;
      }
    }
    <MyCom foo="bar" />
    ```


### 子孙类型检查
  ```ts
  interface PropsType {
    children: JSX.Element
    name: string
  }

  class Component extends React.Component<PropsType, {}> {
    render() {
      return (
        <h2>{this.props.children}</h2>
      )
    }
  }

  // 正确
  <Component>
    <h1>Hello World</h1>
  </Component>

  // 报错：JSX.Element 不是数组类型
  <Component>
    <h1>Hello World</h1>
    <h2>Hello World</h2>
  </Component>
  ```


## React 集成

  ```ts
  // 使用 React 类型定义
  /// <reference path="react.d.ts" />

  interface Props {
    foo: string;
  }
  class MyCom extends React.Component<Props, {}> {
    render() {
      return <span>{this.props.foo}</span>
    }
  }

  // 正确
  <MyCom foo="bar" />; 
  // 报错
  <MyComponent foo={0} />; 
  ```


## 工厂函数
> `jsx: react` 编译选项用的工厂函数是可以配置的。可以使用 `jsxFactory` 命令行选项，或者内联的 `@jsx` 注释指令在每个文件上设置。

  ```ts
  // 设置 jsxFactory：调用 createElement("div") 而非 React.createElement("div")
  import preact = require("preact");
  /* @jsx preact.h */
  const x = <div />;

  // 结果
  const preact = require('preact');
  const x =  preact.h("div", null);
  ```


