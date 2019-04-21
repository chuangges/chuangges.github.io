---
title: Javascript 数据结构
tags:
  - Javascript
  - js 数据
categories: Javascript
top: false
keywords:
  - js
  - 数据结构
date: 2019-03-20 21:40:45
description: 栈、队列、链表、集合、字典、散列表、树、图
---

# 数据结构
> 计算机存储、组织数据的方式

## 逻辑结构
> 数据组织方式

  * __集合结构__
    * 操作：并集、交集、补集
    * 特点
      * 集合由一组无序且唯一的项组成
      * 单个数据元素之间没有任何关系
  * __线性结构__
    * 类型：线性表，包括 堆、栈、队列、一维数组
    * 特点
      * 数据元素是有序的
      * 数据元素之间存在 一对一 的层次关系
  * __树形结构__
    * 类型：树
    * 数据元素之间存在 一对多 的层次关系
  * __图形结构__
    * 类型：图
    * 数据元素之间是 多对多 的层次关系


## 物理结构
> 数据存储方式

  * __顺序存储__
    * 内存：元素连续放置，基于位置关系访问
    * 优点：随机访问
    * 缺点：插入删除效率低，大小固定
  * __链式存储__
    * 内存：元素并不连续放置，基于引用指针关系访问
    * 优点：大小动态扩展，插入删除效率高
    * 缺点：访问链表元素需要循环查找，不能随机访问
  * __索引存储__
    * 内存：整体无序但索引块之间有序
    * 优点：对顺序查找的一种改进，查找效率高
    * 缺点：需要额外空间存储索引表
  * __散列存储__
    * 内存：数据元素根据函数计算存储位置
    * 优点：基于数据本身查找，查找和存取效率高
    * 缺点：存取随机而不利于顺序查找，存储位置可能相同而冲突


# 一、栈
  * 一种遵循先进后出原则的特殊线性表
  * 它基于数组或链表实现，只能在栈顶增删元素
  * 新元素靠近栈顶，旧元素靠近栈底，类似叠起来的书

  ```js
  // 定义栈类
  class Stack {

    // 底层数据结构是数组
    constructor() {
      this.items = []
    }

    // 入栈
    push(element) {
      this.items.push(element)
    }

    // 出栈
    pop() {
      return this.items.pop()
    }

    // 末位
    get peek() {
      return this.items[this.items.length - 1]
    }

    // 是否为空栈
    get isEmpty() {
      return !this.items.length
    }

    // 尺寸
    get size() {
      return this.items.length
    }
    
    // 清空栈
    clear() {
      this.items = []
    }
  }

  // 使用栈类
  const stack = new Stack()
  console.log(stack.isEmpty)  // true

  stack.push(5)
  stack.push(8)
  console.log(stack.peek)     // 8
  console.log(stack.size)     // 2
  ```


# 二、队列
  * 一种遵循先进先出原则的特殊线性表
  * 只能队尾添加、队首删除元素，类似排队人群

  ```js
  class Queue {

    constructor(items) {
      this.items = items || []
    }

    // 循环队列
    enqueue(element){
      this.items.push(element)
    }
    // 优先队列：根据优先级增删
    enqueue(element, priority){
      const queueElement = { element, priority }
      if (this.isEmpty) {
          this.items.push(queueElement)
      } else {
          const preIndex = this.items.findIndex((item) => queueElement.priority &lt; item.priority)
          if (preIndex &gt; -1) {
              this.items.splice(preIndex, 0, queueElement)
          } else {
              this.items.push(queueElement)
          }
      }
    }

    dequeue(){
      return this.items.shift()
    }

    front(){
      return this.items[0]
    }

    clear(){
      this.items = []
    }

    get size(){
      return this.items.length
    }

    get isEmpty(){
      return !this.items.length
    }

    print() {
      console.log(this.items)
    }
  }

  // 实例化
  const queue = new Queue()

  // 普通队列
  queue.enqueue('John')
  queue.enqueue('Jack')
  console.log(queue.size)     // 2
  console.log(queue.isEmpty)  // false

  // 优先队列
  queue.enqueue('Surmon', 3)
  queue.enqueue('John', 2)
  queue.enqueue('Jack', 1)
  queue.print()

  // 循环队列
  class LoopQueue extends Queue {

    constructor(items) {
        super(items)
    }

    getIndex(index) {
        const length = this.items.length
        return index &gt; length ? (index % length) : index
    }

    find(index) {
        return !this.isEmpty ? this.items[this.getIndex(index)] : null
    }
  }
  const loopQueue = new LoopQueue(['Surmon'])
  loopQueue.enqueue('SkyRover')
  loopQueue.enqueue('Even')
  console.log(loopQueue.size)      // 3
  console.log(loopQueue.find(26))  // 'Evan'
  ```


# 三、链表
> 链接方式存储数据的线性表。链表的每个数据单元就是一个节点，包含节点信息、指向后一个节点的引用地址 (称为"链") 

  <div style="text-indent: 2em">数组是存储多个元素时最常用的数据结构，但是在很多编程语言中，数组的长度固定，当数组填满后再添加元素比较困难，删除和添加元素时需要将数组中的所有元素变换位置，操作成本较高。JS 数组并不存在上述问题，主要是因为它们被作为对象实现，不过效率较低。线性表的顺序存储结构由于存储元素不连续都会有以上问题，链表就是针对它的优化结果。</div>
        

## 链表类型
  * 单链表：单一的向下传递，每个节点只记录下一个节点的信息
  * 静态链表：基于数组实现的链表，每个节点包含了数据和指向地址
  * 循环链表：由于单链表到达尾部时回溯到头部很麻烦，所以首尾相连形成循环
  * 双向链表：基于单链表增加前指针，每个节点都保存两个分别指向前后节点的地址 


## 单链表
### 结构  
  <div align="center">
  <!-- link-list.png -->
  ![单链表结构](https://ws3.sinaimg.cn/large/006tNc79gy1g2acrag4juj30i102qjrc.jpg)
  </div>
  
  * data中保存着数据，next保存着下一个链表的引用
  * 我们将链表的尾元素指向了 null 节点，表示链接结束的位置
  * 可以说 data2 跟在 data1 后面，但不能说 data2 是链表中的第二个元素

### 节点操作  
  <div align="center">
  <!-- link-opration.png -->
  ![单链表节点操作](https://ws2.sinaimg.cn/large/006tNc79gy1g29nbya5cyj30rs0ewt91.jpg)
  </div>

  * 插入：修改前驱节点而使其指向新节点，并将新节点指向原前驱节点指向的节点
  * 删除：将待删节点的前驱节点指向待删节点的，同时将待删节点指向 null
    
  ```js
  // 链表节点
  class Node {
    constructor(element) {
      this.element = element  // 数据
      this.next = null        // 指针 
    } 
  }

  // 链表
  class LinkedList {

    constructor() {
      this.head = null
      this.length = 0
    }

    // 追加元素
    append(element) {
      const node = new Node(element)
      let current = null
      if (this.head === null) {
          this.head = node
      } else {
          current = this.head
          while(current.next) {
              current = current.next
          }
          current.next = node
      }
      this.length++
    }

    // 任意位置插入元素
    insert(position, element) {
      if (position &gt;= 0 && position &lt;= this.length) {
        const node = new Node(element)
        let current = this.head
        let previous = null
        let index = 0
        if (position === 0) {
            this.head = node
        } else {
            while (index++ &lt; position) {
              previous = current
              current = current.next
            }
            node.next = current
            previous.next = node
        }
        this.length++
        return true
      }
      return false
    }

    // 移除指定位置元素
    removeAt(position) {

      // 检查越界值
      if (position &gt; -1 && position &lt; length) {
          let current = this.head
          let previous = null
          let index = 0
          if (position === 0) {
              this.head = current.next
          } else {
              while (index++ &lt; position) {
                  previous = current
                  current = current.next
              }
              previous.next = current.next
          }
          this.length--
          return current.element
      }
      return null
    }

    // 寻找元素下标
    findIndex(element) {
      let current = this.head
      let index = -1
      while (current) {
        if (element === current.element) {
          return index + 1
        }
        index++
        current = current.next
      }
      return -1
    }

    // 删除指定文档
    remove(element) {
      const index = this.indexOf(element)
      return this.removeAt(index)
    }

    isEmpty() {
      return !this.length
    }

    size() {
      return this.length
    }

    // 转为字符串
    toString() {
      let current = this.head
      let string = ''
      while (current) {
        string += ` ${current.element}`
        current = current.next
      }
      return string
    }
  }

  const linkedList = new LinkedList()
  console.log(linkedList)

  linkedList.append(2)
  linkedList.append(6)
  linkedList.insert(3, 18)
  console.log(linkedList)
  console.log(linkedList.findIndex(24))
  ```


## 双向链表
  ```js
  // 链表节点
  class Node {
    constructor(element) {
      this.element = element
      this.prev = null
      this.next = null
    }
  }

  // 双向链表
  class DoublyLinkedList {

    constructor() {
      this.head = null
      this.tail = null
      this.length = 0
    }

    // 任意位置插入元素
    insert(position, element) {
      if (position &gt;= 0 && position &lt;= this.length){
          const node = new Node(element)
          let current = this.head
          let previous = null
          let index = 0
          // 首位
          if (position === 0) {
              if (!head){
                  this.head = node
                  this.tail = node
              } else {
                  node.next = current
                  this.head = node
                  current.prev = node
              }
          // 末位
          } else if (position === this.length) {
              current = this.tail
              current.next = node
              node.prev = current
              this.tail = node
          // 中位
          } else {
              while (index++ &lt; position) {
                previous = current
                current = current.next
              }
              node.next = current
              previous.next = node
              current.prev = node
              node.prev = previous
          }
          this.length++
          return true
      }
      return false
    }

    // 移除指定位置元素
    removeAt(position) {
      if (position &gt; -1 && position &lt; this.length) {
          let current = this.head
          let previous = null
          let index = 0

          // 首位
          if (position === 0) {
              this.head = this.head.next
              this.head.prev = null
              if (this.length === 1) {
                  this.tail = null
              }

          // 末位
          } else if (position === this.length - 1) {
              this.tail = this.tail.prev
              this.tail.next = null

          // 中位
          } else {
              while (index++ &lt; position) {
                previous = current
                current = current.next
              }
              previous.next = current.next
              current.next.prev = previous
      }
      this.length--
      return current.element
      } else {
          return null
      }
    }
  }
  ```


# 四、集合
> 由一组无序且唯一的项组成，操作方法可分为 并集、交集、差集。集合、字典、散列表都可以存储不重复的数据

  ```js
  // 定义
  class Set {

    constructor() {
      this.items = {}
    }

    has(value) {
      return this.items.hasOwnProperty(value)
    }

    add(value) {
      if (!this.has(value)) {
          this.items[value] = value
          return true
      }     
      return false
    }

    remove(value) {
      if (this.has(value)) {
          delete this.items[value]
          return true
      }
      return false
    }

    get size() {
      return Object.keys(this.items).length
    }

    get values() {
      return Object.keys(this.items)
    }

    // 并集
    union(otherSet) {
      const unionSet = new Set()
      this.values.forEach((v, i) => unionSet.add(this.values[i]))
      otherSet.values.forEach((v, i) => unionSet.add(otherSet.values[i]))
      return unionSet
    }

    // 交集
    intersection(otherSet) {
      const intersectionSet = new Set()
      this.values.forEach((v, i) => {
          if (otherSet.has(v)) {
              intersectionSet.add(v)
          }
      })
      return intersectionSet
    }
    
    // 差集
    difference(otherSet) {
      const differenceSet = new Set()
      this.values.forEach((v, i) => {
          if (!otherSet.has(v)) {
              differenceSet.add(v)
          }
      })
      return differenceSet
    }

    // 子集
    subset(otherSet) {
      if (this.size &gt; otherSet.size) {
          return false
      } else {
          return !this.values.some(v => !otherSet.has(v))
      } 
    }
  }

  var set1 = new Set();
  set1.add(1);

  var set2 = new Set();
  set2.add(1);
  set2.add(2);

  // 数据
  console.log(set1.values)   
  console.log(set1.has(1))  

  // 并集
  let set11 = set1.union(set2);
  console.log(set11.values);  // [1,2]
  
  // 交集
  let set12  = set1.intersection(set2);
  console.log(set12.values);  // [1]

  // 差集
  let set13 = set2.difference(set1);
  console.log(set13.values); // [2]
  
  // 判断子集
  console.log(set1.subset(set2)); // true
  ```


# 五、字典
> 即映射，以键值对 { key: value } 的形式存储不重复的数据，Object 对象是字典在 Js 中的实现

  ```js
  class Dictionary {

    constructor() {
      this.items = {}
    }

    set(key, value) {
      this.items[key] = value
    }

    get(key) {
      return this.items[key]
    }

    remove(key) {
      delete this.items[key]
    }

    get keys() {
      return Object.keys(this.items)
    }

    get values() {

      // 使用 ES7 values
      // return Object.values(this.items)
      
      // 通过循环生成一个数组并输出
      return Object.keys(this.items).reduce((r, c, i) => {
          r.push(this.items[c])
          return r
      }, [])
    }
  }

  const dictionary = new Dictionary()
  dictionary.set('Gandalf', 'gandalf@email.com')
  dictionary.set('John', 'johnsnow@email.com') 

  console.log(dictionary)
  console.log(dictionary.keys) 
  ```


# 六、散列表
> 又称哈希表，是根据关键字而直接访问内存存储位置的数据结构。

  <div style="text-indent: 2em">通过计算一个数据关键字的函数，将所需查询的数据内容映射到表中的一个存放位置来访问记录，这加快了查找速度。这个映射函数称做散列函数，存放记录的数组称做散列表。简单来说，通过散列函数将要检索的项与索引（散列值）关联起来，生成一种便于搜索的数据结构（散列表）。</div>
        
  * 散列函数
    * 原则：函数本身便于计算、计算出来的地址分布均匀
    * 方法：数字分析法、平方取中法、分段叠加法、除留余数法
  * 处理冲突
    * 原因：键值不同的元素可能会映射到散列表的同一地址，称为 hash 冲突
    * 方法
      * 拉链法/分离链接：将所有散列地址相同的记录都放入一个线性链表
      * 开放定址法：冲突时寻找散列表中的空地址     
        * 线性探测：顺序查看表中下一单元
        * 二次探测：在表的左右进行跳跃式探测
        * 随机探测：建立一个伪随机数发生器
      * 再散列法：冲突时通过其它散列函数计算地址
      * 建立公共溢出区：将所有和散列表冲突的元素都放入溢出表
    
  ```js
  class HashTable {

    constructor() {
      this.table = []
    }

    // 散列函数
    static loseloseHashCode(key) {
      let hash = 0
      for (let codePoint of key) {
        hash += codePoint.charCodeAt()
      }
      return hash % 37
    }

    // 不考虑冲突
    put(key, value) {
      const position = HashTable.loseloseHashCode(key)
      console.log(`${position} - ${key}`)
      this.table[position] = value
    }
    get(key) {
      return this.table[HashTable.loseloseHashCode(key)]
    }
    remove(key) {
      this.table[HashTable.loseloseHashCode(key)] = undefined
    }

    // 分离链接处理冲突：需要一个辅助类 LinkedList 表示要加入的元素
    put(key, value) {
      const position = HashTable.loseloseHashCode(key)
      if (this.table[position] === undefined) {
         this.table[position] = new LinkedList()
      }
      this.table[position].append({ key, value })
    }
    get(key) {
      const position = HashTable.loseloseHashCode(key)
      if (this.table[position] === undefined) return undefined
      const getElementValue = node => {
        if (!node && !node.element) return undefined
        if (Object.is(node.element.key, key)) {
            return node.element.value
        } else {
            return getElementValue(node.next)
        }
      }
      return getElementValue(this.table[position].head)
    }
    remove(key) {
      const position = HashTable.loseloseHashCode(key)
      if (this.table[position] === undefined) return undefined
      const getElementValue = node => {
        if (!node && !node.element) return false
        if (Object.is(node.element.key, key)) {
            this.table[position].remove(node.element)
            if (this.table[position].isEmpty) {
                this.table[position] = undefined
            }
            return true
        } else {
            return getElementValue(node.next)
        }
      }
      return getElementValue(this.table[position].head)
    }

    // 线性探查
    put(key, value) {
      const position = HashTable.loseloseHashCode(key)
      if (this.table[position] === undefined) {
          this.table[position] = { key, value }
      } else {
          let index = ++position
          while (this.table[index] !== undefined) {
            index++
          }
          this.table[index] = { key, value }
      }
      this.table[position].append({ key, value })
    }
    get(key) {
      const position = HashTable.loseloseHashCode(key)
      const getElementValue = index => {
        if (this.table[index] === undefined) return undefined
        if (Object.is(this.table[index].key, key)) {
            return this.table[index].value
        } else {
            return getElementValue(index + 1)
        }
      }
      return getElementValue(position)
    }
    remove(key) {
      const position = HashTable.loseloseHashCode(key)
      const removeElementValue = index => {
        if (this.table[index] === undefined) return false
        if (Object.is(this.table[index].key, key)) {
            this.table[index] = undefined
            return true
        } else {
            return removeElementValue(index + 1)
        }
      }
      return removeElementValue(position)
    }
  }

  const hash = new HashTable()
  hash.put('Surmon', 'surmon.me@email.com')  // 19 - Surmon
  hash.put('John', 'johnsnow@email.com')     // 29 - John

  console.log(hash.get('Surmon'))  // surmon.me@email.com
  console.log(hash.get('Loiane'))  // undefined
  console.log(hash)
  ```


# 七、树
> 一对多的非线性结构、分层数据的抽象模型。树状结构分为树和二叉树，树的操作比较复杂但可以转换为二叉树处理


## 树
  * 定义：n 个相同类型的数据元素的有限集合，树的集合为森林
  * 属性
    * 节点：树中的数据元素
      * 根节点：没有父节点
      * 分支节点：有子节点
      * 叶子节点：没有子节点
    * 子树：节点组成的树
    * 层级：可以按照节点级别分层
    * 深度：节点到根节点的节点数量
    * 高度：所有节点深度中的最大值
    * 权值：
  * 分类
    * 无序树：子节点之间没有顺序关系，又称自由树
    * 有序树：子节点之间有顺序关系
      * 二叉树：每个节点最多含有两个子树
        * 完全二叉树：最后一层右边可能满或不满，左边和其余层都是满的。堆就是一个完全二叉树
        * 满二叉树：所有叶节点都在最底层的完全二叉树
        * 排序二叉树：有序二叉树、二叉查找树、二叉搜索树
        * 平衡二叉树：AVL树，任何节点的左右子树的高度差不超过 1
      * 哈夫曼树：最优二叉树，带权路径最短的二叉树
        * 路径长度：两个节点之间所经过的分支数目
        * 树带权路径长度：所有叶子节点的带权路径长度之和
        * 节点带权路径长度：节点到根节点的分支数目和该节点权值的乘积
      * B 树：一种对读写操作进行优化的自平衡的二叉查找树，能够保持数据有序，拥有多余两个子树。


## 二叉树

### 遍历方式
  * 中序遍历
    * 顺序：左侧子节点、节点本身、右侧子节点
    * 应用：排序操作
  * 前序遍历
    * 顺序：节点本身、左侧子节点、右侧子节点
    * 应用：打印一个结构化的文档
  * 后序遍历
    * 顺序：左侧子节点、节点本身、右侧子节点
    * 应用：计算目录及其子目录的文件空间


### 存储结构

  <div align="center">
  <!-- binary-order.png -->
  ![二叉树顺序存储结构](https://ws3.sinaimg.cn/large/006tNc79gy1g29nbythfsj30l40gm75e.jpg)
  </div>
  <div align="center">
  <!-- binary-link.png -->
  ![二叉树链式存储结构](https://ws4.sinaimg.cn/large/006tNc79gy1g29nbyz7yqj30mg0at74y.jpg)
  </div>

  ```js
  // 顺序存储结构
  var tree = [1, 2, 3, 4, 5, , 6, , , 7];

  // 链式存储结构
  class BinaryNode {
    constructor(key, left = null, right = null) {
      this.key = key
      this.left = left
      this.right = right
    }
  }
  ```


### 基础定义

  ```js
  class Node {
    constructor(key) {
      // 数据元素
      this.key = key 
      // 指向左右节点的指针
      this.left = null    
      this.right = null
    }
  }

  class BinarySearchTree {

    constructor() {
      this.root = null
    }

    insert(key) {
      const newNode = new Node(key)
      const insertNode = (node, newNode) => {
          if (newNode.key &lt; node.key) {
              if (node.left === null) {
                  node.left = newNode
              } else {
                  insertNode(node.left, newNode)
              }
          } else {
              if (node.right === null) {
                  node.right = newNode
              } else {
                  insertNode(node.right, newNode)
              }
          }
      }
      if (!this.root) {
          this.root = newNode
      } else {
          insertNode(this.root, newNode)
      }
    }

    // 中序遍历
    inOrderTraverse(callback) {
      const inOrderTraverseNode = (node, callback) => {
          if (node !== null) {
              inOrderTraverseNode(node.left, callback)
              callback(node.key)
              inOrderTraverseNode(node.right, callback)
          }
      }
      inOrderTraverseNode(this.root, callback)
    }

    // 先序遍历
    preOrderTraverse(callback) {
      const preOrderTraverseNode = (node, callback) => {
        if (node !== null) {
            callback(node.key)
            preOrderTraverseNode(node.left, callback)
            preOrderTraverseNode(node.right, callback)
        }
      }
      preOrderTraverseNode(this.root, callback)
    }

    // 后序遍历
    postOrderTraverse(callback) {
      const postOrderTraverseNode = (node, callback) => {
        if (node !== null) {
            postOrderTraverseNode(node.left, callback)
            postOrderTraverseNode(node.right, callback)
            callback(node.key)
        }
      }
      postOrderTraverseNode(this.root, callback)
    }

    min(node) {
      const minNode = node => {
        return node ? (node.left ? minNode(node.left) : node) : null
      }
      return minNode(node || this.root)
    }

    max(node) {
      const maxNode = node => {
        return node ? (node.right ? maxNode(node.right) : node) : null
      }
      return maxNode(node || this.root)
    }

    search(key) {
      const searchNode = (node, key) => {
        if (node === null) return false
        if (node.key === key) return node
        return searchNode((key &lt; node.key) ? node.left : node.right, key)
      }
      return searchNode(root, key)
    }

    remove(key) {
      return this._removeNode(this.root, key)
    }

    _removeNode(node, key) {
      if (node === null) return false

      if (key &lt; node.key) {
          node.left = this._removeNode(node.left, key)
          return node
      } else if (key &gt; node.key) {
          node.right = this._removeNode(node.right, key)
          return node
      } else {
          if (node.left === null && node.right === null) {
              return null
          }
          if (node.left === null) {
              return node.right
          } else if (node.right === null) {
              return node.left
          } else {
              const minNode = this._findMinNode(node.right)
              node.key = minNode.key
              node.right = this._removeNode(node.right, minNode.key)
              return node
          }
      }
    }
  }

  const tree = new BinarySearchTree()
  tree.insert(11)
  tree.insert(7)
  tree.insert(5)
  console.log(tree)
  tree.inOrderTraverse(value => { console.log(value) })
  ```


# 八、图
> 由一系列顶点和连接顶点的边组成的一种非线性数据结构，可以用来表示任何二元关系

## 图的表示
> 没有绝对正确的表示方式，采用哪种方式取决于图的类型和待解决的问题

### 邻接矩阵    
  * 使用一个二维数组来表示图中顶点之间的连接
  * 如果索引为 i、j 的两个节点连接，则 arr[i][j] === 1，否则为 0
  * 缺点：只有一个相邻顶点的顶点也需要一整行来表示连接，浪费内存

  <div align="center">
  <!-- near-matrix.png -->
  ![邻接矩阵](https://ws2.sinaimg.cn/large/006tNc79gy1g29nby42ahj30fd07tq2y.jpg)
  </div>


### 邻接表  
  * 由图中每个顶点的相邻顶点列表所组成
  * 只要能表示一对多的数据结构都可以用来描述邻接表，比如多维数组、链表、散列表、字典等

  <div align="center">
  <!-- near-table.png -->
  ![邻接表](https://ws1.sinaimg.cn/large/006tNc79gy1g29nbxtgl2j30bx07ft8o.jpg)
  </div>

### 关联矩阵  
  * 矩阵的行列分别表示顶点和边，使用二维数组表示两者之间的连通性
  * 如果顶点A 是边 E 的入射点，则 array[A][E] === 1，否则为 0
  * 通常用于边的数量比顶点少的情况，以节省空间和内存。

  <div align="center">
  <!-- relate-matrix.png -->
  ![关联矩阵](https://ws2.sinaimg.cn/large/006tNc79gy1g29nbxij3aj30jj04wglh.jpg)
  </div>

    
## 图的遍历
> 可以用来寻找特定的顶点或寻找两个顶点之间的路径，检查图是否连通，检查图是否含有环等。思路是追踪每个第一次访问的节点，并且追踪有哪些节点还没有被完全探索。

  * 广度优先搜索：一层一层的遍历顶点
  * 深度优先搜索：选择一个路径一直深入遍历直到尽头，再递归回去选择未访问的顶点
        

## 创建图类

  ```js
  class Graph {

    constructor() {
      this.vertices = []
      this.adjList = new Dictionary()
    }

    // 添加顶点
    addVertex(v) {
      this.vertices.push(v)
      this.adjList.set(v, [])
    }

    // 添加线
    addEdge(v, w) {
      this.adjList.get(v).push(w)
      this.adjList.get(w).push(v)
    }

    toString() {
      return this.vertices.reduce((r, v, i) => {
          return this.adjList.get(v).reduce((r, w, i) => {
              return r + `${w} `
          }, `${r}\n${v} => `)
      }, '')
    }

    // 构建顶点之间的路径
    distance(fromVertex) {
      const vertices = this.vertices
      const { distances, predecessors } = this.bfs(fromVertex)
      vertices.forEach(toVertex => {
          if (!!distances[toVertex]) {
              let preVertex = predecessors[toVertex]
              let slug = ''
              while (fromVertex !== preVertex) {
                  slug = `${preVertex} - ${slug}`
                  preVertex = predecessors[preVertex]
              }
              slug = `${fromVertex} - ${slug}${toVertex}`
              console.log(slug)
          }
      })
    }

    // 广度优先搜索算法
    bfs(v, callback) {
        const read = []
        const distances = []
        const predecessors = []
        const adjList = this.adjList
        const pending = [v || this.vertices[0]]
        const readVertices = vertices => {
          vertices.forEach(key => {
              read.push(key)
              pending.shift()
              distances[key] = distances[key] || 0
              predecessors[key] = predecessors[key] || null
              adjList.get(key).forEach(v => {
                  if (!pending.includes(v) && !read.includes(v)) {
                      pending.push(v)
                      distances[v] = distances[key] + 1
                      predecessors[v] = key
                  }
              })
              if (callback) callback(key)
              if (pending.length) readVertices(pending)
          })
        }
        readVertices(pending)
        return { distances, predecessors }
    }

    // 深度优先搜索算法
    dfs(callback) {
        let readTimer = 0
        const read = []
        const readTimes = []
        const finishedTimes = []
        const predecessors = []
        const adjList = this.adjList
        const readVertices = (vertices, predecessor) => {
          vertices.forEach(key => {
              readTimer++
              if (adjList.get(key).every(v => read.includes(v)) && !finishedTimes[key]) {
                  finishedTimes[key] = readTimer
              }
              if (read.includes(key)) return false
              readTimes[key] = readTimer
              read.push(key)
              if (callback) callback(key)
              predecessors[key] = predecessors[key] || predecessor || null
              if (read.length !== this.vertices.length) {
                  readVertices(adjList.get(key), key)
              }
          })
        }
        readVertices(adjList.keys)
        return { readTimes, finishedTimes, predecessors }
    }
  }


  const graph = new Graph()
  const vertices = ['A','B','C','D'];
  vertices.forEach(v => graph.addVertex(v))

  graph.addEdge('A', 'B')
  graph.addEdge('A', 'C')
  graph.addEdge('A', 'D')
  graph.addEdge('C', 'D')

  console.log(graph.toString())  
  graph.bfs(graph.vertices[0], value => console.log('Visited: ' + value))
  ```



