# JS Interview

[TOC]

# JS

## 非递归遍历树

队列

## Js New 操作

新建一个空的简单 JS 对象，将构造函数指向该对象，将调用函数的 this 指向新建的这个对象，调用构造函数，如果返回值为对象，返回该返回值，否则返回 this

## 方法调用 this 指向

词法作用域

对于全局函数，指向 global/window; 对于方法，指向当前调用的对象；对于 lambda，指向创建时捕获的外围调用者；对于使用 bind、call、apply ，指向新绑定的对象（非严格模式下，绑定非对象会被提升为对象）；对于 new 的函数，指向新创建的对象。

## 什么是 JS 闭包

创建函数式，捕获创建的外围词法作用域的对象的值（基本值类型）和引用（对象），从而可以从内部函数访问外围函数作用域。

## setTimeout 在单线程的 JS 里异步执行

任务队列分为同步任务和异步任务队列；异步任务会被交给事件循环实现，有些异步任务，如 node.js 和浏览器中的 IO 会交给线程池，而一些会造成不可知结果的任务，如浏览器中的渲染则是和 JS 线程交替执行

两种异步任务队列：

宏任务：

1. IO
2. setTimeout
3. setInterval
4. setImmediate
5. requestAnimationFrame

微任务：

1. process.nextTick
2. Promise
3. Promise.then
4. MutationObserver

宏任务执行后，首先将微任务队列执行完毕后，再执行下一个宏任务。创建异步任务可能有定时器模块，到了执行时间才开始将异步任务加入异步队列。

## var 和 let、const 的区别

var 是函数作用域, 不允许变量提升，不存在死区；let、const 是词法作用域，不允许变量提升，存在死区，不允许重复声明

## JS 绑定新对象

bind, apply, call

## ES5 和 ES6 中的作用域和闭包

ES5 的 var，全局和函数级
ES6 词法块级作用域
函数化的闭包，实现为对值对象获取其当时值快照、引用对象获取引用指针保存在创建的匿名对象中，通过类似 operator() 的方法予以调用

### This 的指向

谁调用 this，this 就指向谁

- 全局
    - 浏览器 window
    - node {}
- new
    - 返回对象，指向该对象
    - 返回其它，指向新对象
- call，apply，bind
    - 严格模式下，绑定传入的参数
    - 非严格模式下
        - 若为空，指向全局对象
        - 若为值对象，返回对应的应用对象
        - 其它，为传入的参数
- 方法调用，调用位置上下文的对象
- lambda，外层上下文绑定的 this 

## JS 基本对象

- Boolean
- String
- Null
- Undefined
- Symbol
- BigInt
- Object

**typeof null==='object' JS 最初版本在 32 位环境中，性能考虑，低位存储类型，000 开头表示对象，null 全零，错误判断为 object**

## 说一说对 JS 执行上下文栈和作用域链的理解

JS 执行上下文就是 JavaScript 代码被解析和执行时所在环境的抽象概念，执行上下文分为：

- 全局执行上下文
- 函数执行上下文
- eval 函数执行上下文

创建过程：

1. 创建变量对象：初始化 arguments，提升函数声明和变量声明
2. 创建作用域链：在执行上下文的创建阶段，作用域链在变量创建后创建
3. 确定 this 的值

其栈即为函数调用栈。

作用域负责收集和维护所有声明的标识符（变量）组成的一系列查询，并实施一套严格的规则，确定当前执行的代码对这些标识符的访问权限，JS 使用词法作用域。其作用域链为逐层向外。

## setTimeout 倒计时误差

加入宏队列需要时间，计时到达后等待主线程任务完成需要时间
方法：从服务器或者 ntp 获取时间，修正计时器



## 可迭代对象的特点

有 `[Symbol.iterator]` 接口返回一个遍历器对象，可以使用 next() 函数获取 value 和 done，可以使用 for ... of 进行循环

### 自定义迭代器对象

```js
*fn() {
    yield xxx
}
```

使用生成器可以自定义迭代器对象

### 异步迭代器

有 `[Symbol.asyncIterator]` 接口返回一个遍历器对象，可以使用 next() 获取 value 和 done 的 promise，可以使用 for await ... of 循环

异步生成器

```js
async function* fn() {
    yield await xxx;
}
```

## 原型链继承的思路和优缺点

调用方法和属性时，如果没找到会顺着原型链向上查找，实现某种程度的继承

优点：

1. 单继承
2. 可以像超类传递参数
3. 解决引用值共享问题
4. 可以动态修改原型链

缺点：

1. 单继承
2. 查找耗时

## 组合继承的思路和优缺点

将原型链和借用构造函数结合到一起，通过原型链实现对原型属性和方法的继承，通过借用构造函数实现对实例属性的继承

优点：

1. 每个子类可有自己的属性
2. 可向超类传递参数
3. 可函数复用

缺点：

1. 调用两次构造函数

```js
function SuperType(name) {
    this.name = name;
    this.colors = ["red", "blue", "green"];
}

SuperType.prototype.sayName = function() {
    alert(this.name);
}

function SubType(name, age) {
    SuperType.call(this, name); // 第二次调用 SuperType()
    this.age = age;
}

SuperType.prototype = new SuperType(); // 第一次调用 SuperType()
SubType.prototype.sayAge = function() {
    alert(this.age);
}
```

## 原型式继承

通过已有对象创建新对象，不必创建自定义类型，ES6 中可以使用 Object.create(proto) 来创建

优点：

1. 借助已有对象创建新对象，不必创建自定义类型

缺点：

1. 包含引用类型的属性会被所有实例共享

## 寄生式继承

类似工厂模式

优点：

1. 考虑对象而不是自定义类型和构造函数，很有用

缺点：

1. 不能函数复用
2. 包含引用类型只的属性会被所有实例共享

```js
function object(o) {
    function F(){}
    F.prototype = o;
    return new F();
}

function createAnother(original) {
    // 通过调用函数创建一个新对象
    var clone = object(original);  
    // 以某种方式来增强这个对象
    clone.sayHi = function() {
        alert("hi");
    }

    return clone;
}
```

## 寄生组合式继承

原型链继承方法，借用构造函数继承属性

优点：

只调用一次超类构造函数
原型链不变

```js
function inheritPrototype(subType, superType) {
    var prototype = object(superType.prototype); // 创建对象
    prototype.constructor = subType; // 增强对象
    subType.prototype = prototype; // 指定对象
}

function SuperType(name) {
    this.name = name;
    this.colors = ["red", "blue", "green"];
}

SuperType.prototype.sayName = function() {
    alert(this.name);
}

function SubType(name, age) {
    SuperType.call(this, name);

    this.age = age;
}

inheritPrototype(SubType, SuperType);

SubType.prototype.sayAge = function( {
    alert(this.age);
})
```

## ES6 继承

通过 extends 关键字实现：

```js
console.log(typeof SuperType);//function
console.log(SuperType === SuperType.prototype.constructor); //true
```

类内方法默认不可枚举，而 ES5 可枚举；class 不能不加 new 直接调用；子类继承必须使用 super 方法，否则报错；使用 super 后才能使用 this 关键字

## ES6 模块和 CommonJS 模块的差异

1. 运行时和编译时
    - CommonJS 模块运行时加载，import 是静态加载，编译时确定模块依赖关系；注意有 import() 是动态的
    - CommonJS 加载的是对象，该对象在脚本运行完成才会生成
2. CommonJS 输出的是值的拷贝，ES6 输出的是值的只读引用
3. ES6 模块自动使用严格模式
4. require 可以做动态加载，import 语句（非import函数）做不到
6. 使用 require 加载某个模块时，就会运行整个模块的代码
7. 当使用 require 命令加载同一个模块时，会使用缓存，除非删除缓存，否则不会得到新值

ps: export default 可以理解为将变量赋值给 default，最后导出 default

## 如何让 Promise.all 在抛出异常后仍然有效

给每个作为参数的 promise 实例添加 catch 方法，则其在抛出异常并经过 catch 处理后会转换为 fullfilled 状态

```js
const tasks = promises.map(p=>Promise.resolve(p).catch(e=>e));
Promise.all(tasks) //...
```

## Promise如何满足多个异步进程的同步顺序

1. then 链式调用，若前一个回调函数返回的是 promise 对象，则后一个回调会等待该 promise 对象的状态发生变化，后者才会被调用
2. async await 语法糖

## Promise 的状态

pending、fullfilled、rejected

## 对 Object.defineProperty 的理解

Object.defineProperty(obj, prop, descriptor) 会在对象上定义新属性，或者修改一个对象的现有属性，返回该对象

- obj 要定义属性的对象
- prop 要定义或者修改属性的名称
- descriptor 要被定义或者修改的属性描述符

descriptor 属性描述符的形式：数据描述符、存取描述符，描述只能是两者之一

共同特性：

- configurable 可设置性，能否被删除；除了 value 和 writable 之外的属性能否被修改，默认为 false； 能否修改为访问器属性
- enumerable 能否在 for...in 循环和 Object.keys() 中被枚举，默认为 false

数据描述符：

- value 该属性对应的值，可以是任何有效的 JS 值，默认为 undefined
- writable 当且仅当该属性的 writable 为 true 时，value 才能被复制运算符改变，默认为 false

存取描述符：

- get 一个给属性提供 getter 的方法，如果没有 getter 则为 undefined。当访问该属性时，该方法被执行。方法执行时，只有 this 可用，无参数。默认为 undefined
- set 一个给属性提供 setter 的方法，如果没有 setter 则为 undefined。当属性值修改时，触发执行该方法。将接受 = 后的值为参数。默认为 undefined

## 对 Proxy 的理解？

proxy 对象用于定义基本操作的自定义行为，如属性查找、赋值、枚举、函数调用等。相当于拦截并修改某些操作的默认行为。使用方法为 `const proxy = new Proxy(target, handler)`，其中：

- target 是用 proxy 包装的目标对象
- handler 是一个对象，其属性是当执行一个操作时，定义代理的行为的函数，也就是自定义行为；handler 可以为 {} 但是不能为 null，否则会报错

proxy 的操作，其中 receiver 代表在原型链上最初被调用的对象, newTarget 表示最初被调用的构造函数

- get(target, property, receiver) => getter
- set(target, property, value, receiver) => setter
- has(target, property) => in
- getPrototypeOf(target) => getPrototypeOf
- setPrototypeOf(target, prototype) => setPrototypeOf
- isExtensible(target) => Object.isExtensible Reflect.isExtensible（必须是对象，否则报错）
- preventExtensions(target) => Object.preventExtensions Reflect.preventExtensions
- getOwnPropertyDescriptor(target, prop) => Object/Reflect.getOwnPropertyDescriptor
- deleteProperty(target, property) => delete obj.prop, Reflect.deleteProperty
- constructor(target, argumentsList, newTarget) => Reflect.construct new proxy(...args)
- ownKeys(target) => Reflect.ownKeys Object.keys Object.getOwnPropertySymbols Object.getOwnPropertyNames
- apply(target, thisArg, argumentsList) => proxy(...args) Function.prototype.apply Function.prototype.call Reflect.apply

## Object.defineProperty 和 Proxy 的区别

Object.defineProperty

- 不能监听数组 length 的变化，因为它的 configurable 为 false
- 不能监听对象的添加和删除
- 只能劫持对象的属性

Proxy

- 可代理整个对象
- 多达 13 种操作

## js 哈希存储结构的构成方式 ?

hash 常用构造方法：

1. 直接地址法
2. 除数留余法
3. 数字分析法
4. 平方求和法
5. 分段求和法

哈希冲突的解决方法：

1. 开放定址法：可能造成二次聚集，装填因子较小
    - 线性探测再散列
    - 二次探测再散列
    - 伪随机探测再散列
2. 再 hash 法：增加了计算时间
3. 链地址法：处理简单，无堆积；适用于无法确定表长，各节点动态申请，适合于造表前无法确定表长；需要额外空间
4. 公共溢出区

Map、Set、WeakSet、WeakMap 小于八个线性查找，多于之后变成 hashmap。在 V8 中，散列码是一个随机数，与对象无关，不能重新计算它。对于将 JS 对象作为 key 的情况，V8 将哈希码作为私有对象存储在对象上。V8 的私有符号和 Symbol 类似，但是不可枚举，只能在 V8 内部使用。

- 当属性存储是空的时候，可以直接在 JSObject 的偏移量上存储 hashcode
- 当属性存储是数组：
    - 最低有效位是指针标志位，其余 31 位表示实际整数值，最大小于 2^31 的整数在 JS 上更加高效；- 通常数组将长度存储为 SmallInteger ，因此最大长度为 10 比特即可表示，可以使用剩下的 21 位来存储 hashcode
- 当属性是字典：将字典大小增加一个字，以便将哈希吗存储在字典起始位置的专用槽中

# 浏览器 & WebAPI

## 输入 URL 到渲染完成

域名解析-TCP分包-IP寻路-握手-滑动窗口传输-持久化连接-挥手-解析-构建dom树与cssom-构建渲染树-回流-重绘-渲染

## 重绘和重排

### 渲染过程

1. DOM Tree -> Render Tree
2. Style struct -> Render Tree
3. Render Tree -> Paint

浏览器解析 HTML 源码，创建 DOM 树，每个 HTML 标签在树上都有一个对应的节点；标签中的文本也有对应的文本节点；DOM 树上根节点式 documentElement；浏览器解析 CSS；合并创建渲染树，类似 DOM 树，但并不一一对应；渲染树上的节点使用对应的格式化上下文进行渲染。一旦渲染树被创建成功，浏览器就可以在屏幕上绘制渲染树节点。

### 重绘和重排

在第一次排列和绘制之后，每次改变用于构建渲染树的信息都会导致至少：

- 部分渲染树的节点尺寸需要重新计算 —— 重排
- 由于节点几何属性或者样式发生改变，更新屏幕内容 —— 重绘

这些操作代价高昂

### 触发的场景

1. 无格式化上下文几何变化的变化 -> 重排和重绘
2. 单纯样式变化 -> 重绘

### 浏览器的优化

创造变化队列，在一定时间内同种可覆盖变化会发生覆盖；但在可能需要几何信息的情况下会立即触发变化，可以先缓存值

### 最小化重排和重绘

- 不要逐个改变样式
- 离线的批量改变和表现 DOM
- 通过 documentFragment 来保留临时改动
- 通过复制改变替换的方式
- 通过 display: none 属性隐藏元素，添加足够多的变化后，再显示
- 不频繁计算样式，如果有需要，可以缓存

## 浏览器一个tab页卡住了，为什么其他tab没事 

chrome 使用多个进程来隔离不同的网页，保证安全性、健壮性和响应速度，但也造成了内存较大的损失。

## 浏览器有哪些线程

- GUI 渲染线程：负责渲染界面的 HTML 元素，当页面回流或者重绘时，该线程会执行；在脚本执行期间，该线程挂起
- JavaScript 引擎线程：负责处理 JS 脚本程序，例如 V8 引擎
- 定时器触发线程：为保证计时的准确性
- 事件触发线程：当一个事件触发时，该线程会把事件添加到待处理队列的队尾，等待 JS 引擎的处理。
- 异步 HTTP 请求线程：在 XMLHttpRequest 连接后，浏览器新开一个线程请求，检测的状态变更后，如果设置有回调函数，异步线程就产生状态变更事件放到 JS 引擎的处理队列中等待处理

## png、jpg、gif、webp、heic、bmp 等图片的使用

1. bmp 无损，支持索引色和直接色的点阵图。几乎没有压缩数据。
2. gif 无损，索引色，点阵图。LZW 算法，支持透明，大小小，色彩素质低，支持动图。
3. jpeg，有损，直接色，点阵图。不支持透明，压缩率较高。
4. png-8，无损、索引色、点阵图。支持透明，不支持动画，压缩率较低。
5. png-24，无损，直接色，点阵图。支持透明，压缩率较低。
6. svg，无损、矢量图。
7. webp，支持有损、无损，直接色，点阵图。支持透明，压缩率较高。
8. heic，有损、无损，直接色，点阵图。支持透明，压缩率更高。

## 判断浏览器是否支持 webp

- 创建 image 对象，设置 src，判断是 onload 还是 onerror
- canvas 判断，创建 canvas 对象，通过 canvas 的 toDataURL 设置为 webp 格式，判断返回值中是否含有 imgae/webp 字段，如果包含则支持 webP 反之不支持。

## 什么是 Cookie 隔离？

网站请求相同域名的静态资源会带上 cookie 信息，非常浪费资源。隔离操作：使用非主域名承载静态资源

## 移动端的点击事件延迟 300 毫秒？

判断用户是否再次点击屏幕

## cookie 和 session 的区别

cookie 是客户端中保存少量信息的机制，用于记录用户的信息，也是实现 session 的一种方法

session 是开发者为了实现中断和继续等操作，将 user agent 和 service 之间的交互，抽象为的会话及其会话状态。常见实现方式为，服务端中保存的数据结构，用来记录用户的状态，也可以通过 JWT 的方式实现

## 浏览器的事件代理机制的原因是？

事件流：DOM2 级事件将事件流分为是三个阶段，捕获阶段、目标阶段、冒泡阶段

事件代理机制的原理：在祖先 DOM 绑定一个元素，当触发子孙级 DOM 元素的事件时，利用事件冒泡的原理来触发绑定在祖先级 DOM 的事件，一层层冒泡到 document 对象

事件代理的原因：

1. 减少实际添加到网页上的事件，维护性能
2. 事件代理时，子孙动态，不需要为新添加的子孙绑定事件
3. 子孙移除后，不需要手动清理事件
4. 允许一个事件注册多个监听
5. 提供更精细的手段控制 listener 的触发阶段
6. 对任何 DOM 有效，而不仅仅是 HTML

addEventListener:

```js
target.addEventListener(type, listener[, options]);
target.addEventListener(type, listener[, useCapture]);
```

options 对象

- capture：true 表示在捕获阶段触发，否则在冒泡阶段触发
- once: 在添加后调用一次，后自动移除
- passive: 永远不应该调用 preventDefault() 如果调用，则会忽略并得到控制台警告

## 浏览器里除了 js 还能运行什么

webassembly

# 网络 & 安全

## 同源的定义

同协议，同抽象主机，同端口号

## http 状态码

### 200

200 OK 请求成功 
201 Created 资源创建：POST、PUT
202 Accepted 请求收到，但未响应：异步无回调
204 No Content 成功处理请求，但不需要返回实体内容：如元信息的更新，缓存存活
205 Reset Content 请求者重置文档视图
206 Partial Content 部分请求成功

### 300

300 Multiple Choice 请求的资源有一系列可供选择的回馈信息，各有特定的地址和浏览器驱动的商议信息
301 Move Permanently 永久移动到新位置
302 Found 请求的资源现在从不同的 URI 响应请求
303 See Other 对应当前的请求可以在另一个 URI 上找到，且客户端应该使用 GET 方式访问
304 Not Modified 请求了带条件的 GET 已被允许，但文档的内容相对上次没有变化

### 400

400 Bad Request 语义有误；或者请求参数有误
401 Unauthorized 当前请求需要用户验证
403 Forbidden 服务器已经理解请求，但是拒绝执行，可能是权限不足等
404 Not Found 请求失败，该资源未在服务器上发现
405 Method Not Allowed 该方法不能用于请求对应资源
406 Not Acceptable 资源的特性不满足头的要求
407 Proxy Authentication Required 客户端必须在代理服务器上进行身份验证
408 Request Timeout 请求超时
409 Conflict 被请求的资源和当前状态之间存在冲突，如重复主键的用户
410 Gone 资源永久不再可用，如被删除
411 Length Required 需要长度
412 Precondition Failed 在验证请求字段中先决条件时，一个或者多个没有满足
413 Payload Too Large 服务器拒绝处理当前请求
414 URL Too Long
415 Unsupported Media Type 提交了不支持的媒体类型
426 Upgrade Required 服务器升级后可能能够执行当前请求
428 Precondition Required 原始服务器的请求有条件，旨在防止丢失更新
429 Too Many Requests 在给定时间中发送了太多请求
431 Request Header Fields Too Large 请求头太大
451 Unavailable For Legal Reasons 用户请求非法资源

### 500

500 Internal Server Error 服务器内部错误
501 Not Implement 此方法不被服务器支持且无法处理
502 Bad Gateway 服务器作为网关需要得到一个处理这个请求的响应
503 Service Unavailable 服务器没有准备好请求，可能是服务器在维护或者重载而停机
504 Gateway Timeout 网关服务器不能及时得到相应

## HTTPS 安全问题

```config
Client                          Server
  | ----> (1) Client Hello ---->   |
  | <---- (2) Server Hello <----   |
  | <---- (3) Certificate  <----   |
  | <---- (4) Server Key Exchange  |
  | ----- (5) Server Hello Done    |
  | ----> (6) Client Key Exchange  |
  ---------------------------------- Part. 1
  | ----> (7) Change Cipher Spec   |
  | ----> (8) Finish       ---->   |
  | <---- (9) Change Cipher Spec   |
  | <---- (10) Finish      -----   |
  ---------------------------------- Part. 2
        Shakehand Finish
```

1. 密钥交换与密钥协商：TLS1.3 会将制作密钥需要的材料预先发给服务器
    1. Client Hello： 
        - \[Version/Random/SessionId/Cipher Suite/Compression Method/Extensions\]
    2. Server Hello: 
        - \[Version/Random/SessionId/Cipher Suite/Compression Method（存在安全漏洞，在 TLS1.3 中被禁用）/Extensions\]
    3. Certificate：服务器将证书发送给客户端
    4. Server Key Exchange
    5. Server Hello Done
    6. 计算预主密钥，Client Key Exchange
    7. 计算主密钥：对称加密算法中，相同的密钥的情况下，中间人可以通过对双方流量监听，获得明文（RC4 算法）；MAC 用于防止篡改
        - 客户端写入加密密钥：客户端来加密数据，服务器来解密数据
        - 服务器写入加密密钥：服务器来加密数据，客户端来解密数据
        - 服务器写入 MAC 密钥：客户端创建 MAC，服务器用来验证 MAC
        - 客户端写入 MAC 密钥：服务器创建 MAC，客户端用来验证 MAC
2. 通信测试：服务器通过 Change Cipher Spec 报文通知服务器接下来的报文会用协商好的密码套件加密，然后将加密好的套件通过 Finish 发送给服务器。服务器接收验证数据，用密钥解密，先发送 Change Cipher Spec 报文通知客户端会用协商好的密码套件加密，然后将加密过的验证数据通过 Finish 报文传输给客户端。
    1. TLS 会话复用：服务器将加密过的 SessionID 发给客户端，客户端发送的 SessionId 可以解密则可跳过协商阶段
    2. 密码套件：
        - 密钥交换协议：规定通信双方如何交换通信时使用的对称加密算法的密钥。影响 Server/Client Key Exchange
        - 对称加密算法：规定双方通信时如何加密信息。影响通信测试和正常通信。
        - 消息认证码算法：规定如何对内容进行哈希，生成 MAC。用于保证消息传输过程中不被篡改
        - 伪随机算法：用于生成主密钥和相关密钥

## 浏览器如何缓存

1. localStorage/sessionStorage/indexDB
2. ServiceWorker/CacheAPI
3. 设置 http 的头部 Cache-Control Expires

## 详述http 1.0, 1.1, 2.0的区别

1. HTTP/1.0 - 特性：
    - 协议信息随着每个请求发送
    - 状态码在响应开始时发送
    - 引入 HTTP 头
    - 可以使用 Content-Type 头来传输除 HTML 文件外其他类型文档的能力
2. HTTP/1.1
    - 特性：
        - 连接可以复用
        - 增加流水线
        - 支持响应分块
        - 引入缓存控制
        - 引入内容协商
        - 使用 Host 头，使得不同域名可以配置在同一个 IP 地址的服务上
    - 拓展：
        - 用于安全传输：SSL 和 TLS
        - 用于复杂应用：
            - REST
            - Server-sent events
            - WebSocket
        - 放松 Web 的安全模型：CORS、DNT、CSP
3. HTTP/2 - SDPY
    - 特性：
        - HTTP/2 是二进制协议。不可再读，也不可无障碍的手动创建
        - 并行的请求能够在一个链接中处理，移除了 1.x 中的顺序和阻塞（但是仍然存在队首阻塞）
        - 压缩了 headers，一系列的请求中部分 headers 可以压缩
        - 允许服务器在客户端缓存中填充数据，通过有限的服务器推送机制来提前请求
    - 拓展：
        - 对 Alt-Src 的支持允许了给定资源的位置和资源鉴定，允许更加智能的 CDN 缓存机制
        - Client-Hints 的引入允许客户端和服务器主动交流它的需求和硬件约束的信息
        - 在 Cookie 头中引入安全相关的前缀，可以帮助一个安全的 Cookie 没有被修改过

## 为什么禁止跨域

- 使用 drawImage 等 canvas 绘制函数、以及 WebGL 贴图，可能存在攻击的风险：img、video 等方式访问第三方网页，会带上 cookie
- 使用 XMLHttpRequest 和 Fetch 存在攻击的风险：XSS、CSRF
- 使用 Web 字体，存在攻击的风险：二进制攻击和 CDN 滥用
- 使用 iframe，存在攻击的风险：ClickJacking - 利用嵌入到你的文档或者将你的文档嵌入它的文档，以 iframe 的形式
- 本地数据访问 - Cookie、Storage、indexDB

## OSI 七层

1. 物理层：建立、维护和断开物理连接
2. 数据链路层：建立逻辑连接、硬件寻址、差错检验
3. 网络层：端到端逻辑地址寻址，实现不同网络之间的路径选择
4. 传输层：端口到端口的数据传输，可能有流控、数据校验等其他功能
5. 会话层：建立、管理和终止会话
6. 表示层：数据的标识、安全和压缩
7. 应用层：网络服务于用户的接口

## TCP 三次握手、四次挥手

### 三次握手

LISTEN -> SYSSENT -> ESTAB
LISTEN -> SYN RCVD ->ESTAB

1. Client -> Server SYN, seq=x：客户端发送网络包，服务器收到；服务端确认客户端发送能力和服务端接收能力
2. Server -> Client SYN + ACK, ack=x+1, seq=y：服务端发送网络报，客户端收到；客户端确认自己的发送能力、服务器接收能力、服务器发送能力、客户端接收能力
3. Client -> Server ACK, ack=y+1, seq=x+n：客户端发送网络报，服务器收到；服务端确认服务端发送能力和客户端接收能力

服务端第一次收到 SYN 后，就会处于 SYN_RCVD 状态，称为半连接状态；若发送完 SYN-ACK 包，未收到回复，会指数重传，若超出最大重传次数，将会将半连接队列中删除。初始序号 ISN 会通过一定算法（计数器）随机选择，动态生成，避免攻击。第一次不发送数据，避免攻击。

SYN 攻击是发送很多 SYN，使得正常的 SYN 的半连接队列因为已满而被丢弃，防御方法为：

- 缩短 SYN Timeout 世间
- 增大最大半连接数
- 过滤网关防护

### 四次挥手

客户端 ESTAB -> FIN_WAIT_1 -> FIN_WAIT_2 -> TIMED_WAIT -> CLOSED
服务端 ESTAB -> CLOSE_WAIT -> LAST_ACK -> CLOSED

1. Client -> Server FIN, seq=x：客户端发送一个网络包，服务器收到，确认第 x 个包结束
2. Server -> Client ACK, ACKnum = x+1：服务端发送一个网络包，客户端收到，确认第 x 个包已经被服务器收到
3. Server -> Client FIN, seq=y：服务端发送一个网络包，客户端收到，确认第 y 个包收到
4. Client -> Server ACK ACKnum = y+1: 客户端发送一个网络包，服务器收到，确认客户端第 y 个包收到，同意断开，此时客户端要等待计时器设置的 2MSL （报文段最大生存时间）后才进入 CLOSED 状态，等待服务器未收到确认的重传，同时防止下一个连接中不会出现这种就得请求报文段

两方都要通知对方自己已经结束需要断开，并等待对方同意

## 详述TCP如何保证传输完整性

- 定时器 + ACK + 重传
- 首部校验和
- 重排序
- 流量控制
- 序号丢弃重复
- 有连接

## UDP和TCP有什么区别

- TCP：有连接，可靠，流控
- UDP： 无连接，不可靠

## 为什么使用 UDP

- 可靠性排序靠后
- 时效性较重要
- 减少流控的影响

## WebSocket？

复用 http 1.1 的握手协议，完成后后续交换按照 WebSocket 协议。

首部：

- Connection: Upgrade 表示升级协议
- Upgrade: websocket 表示升级到 Websocket
- Sec-WebSocket-Version: 13 表示支持的版本号，如果服务器不支持，需要返回其 Sec-WebSocket-Version
- Sec-WebSocket-Key 与响应的 Sec-WebSocket-Accept 配套

## RTT 问题？

tcp 1.5 RTT
tls 1.5 RTT/ tls1.3 1 RTT
http 1 RTT

http1.x = 2.5RTT
https + http1.x  = 4RTT
http2 = 队首 4RTT / 其它 1RTT（重用 TCP 连接，但有队首阻塞）
http3/quic
    - 页面整体加载时间 1RTT + 1RTT
    - 重连时间 1RTT

    ## 浏览器 & HTTP 缓存策略

### 强缓存

指在缓存期间，请求不会发送到放服务器，浏览器直接返回缓存结果，需要设置 Header：

- Expires：是 HTTP/1.0 中用于控制网页缓存的字段，表示过期时间；此时使用的是客户端时间和缓存失效时间对比，当用户修改客户端时间时可能导致强缓存失效
- Cache-Control：是 HTTP/1.1 中设置的。其常见值为：
    - public 所有内容都被缓存（客户端和代理服务器）
    - private 所有内容只有客户端可以缓存，默认为 private
    - no-cache 客户端缓存内容，但是否使用缓存需要经过协商来决定
    - no-store 所有内容都不会被缓存
    - max-age=xxx 缓存内容将在 xxx 秒后失效

上述两者同时存在，只有后者生效；其仅仅为兼容作用

### 内存缓存和磁盘缓存

1. 大文件，优先缓存到 disk，否则 mem
2. 当内存占用率高时，优先缓存到 disk

### 协商缓存

当请求没有命中缓存，或者强缓存失效后，需要向服务器发起请求，验证资源是否有更新。如果资源没有改变，返回 304，并更新浏览器缓存有效期；资源改变，返回 200 响应码，并返回响应资源，并更新浏览器缓存有效期。使用的 HTTP 头如下：

1. Last-Modified & If-Modified-Since：前者表示文件最后的修改日期，由服务器添加到响应头中；后者由浏览器添加到请求头中，是上次该资源的 Last-Modified 值。浏览器收到请求后，会将后者和服务器上该文件修改时间戳对比，如果超过缓存时间，那么返回最新的资源，200 状态码；缺点如下：
    - 如果文件打开过，修改时间会发生改变，导致该对失效
    - 精确度只有一秒
2. etag 和 If-None-Match：etag 类似文件指纹，由服务器添加到响应头中，浏览器会在请求头中添加 If-none-match 头，和 etag 比较；如果不一样，则返回 200 和资源；如果一样，返回 304。

当任意缓存策略都没有设置，浏览器会采取启发式策略，通常会读取 Response Header 中的 date 头，减去 Last-Modified 值的 10% 作为缓存时间。

### 实际场景

对于频繁变动的资源：

1. 完全不缓存: no-store
2. 协商缓存：cache-control：no-cache，是浏览器每次都会走服务器，然后配合 etag 或者 last-modified 来验证资源是否有效，这样对于完全不缓存来说，虽然无法减少 HTTP 请求次数，可以显著减少响应数据大小。

文件：

1. HTML 文件不设缓存；
2. CSS、JS 等文件可以设置一个较长的缓存期，比如一年，使用文件名_hash 的方法来更新文件；

## 跨域访问

当一个资源从与该资源本身所在不同的域、协议或者端口请求一个资源时，称为跨域访问。

## OAUTH、JWT、Open?

见认证、授权和凭证

## XSS，CSRF，数据库注入怎么防范？

XSS：控制前端渲染
CSRF：控制后端处理
数据库注入：预编译

## CSRF 防御

1. 判断请求头中的 Referer，但是 Referer 可以设置，在较老的浏览器中该漏洞依然存在
2. 新增 CSRF Token
3. 使用 JWT

## 什么是 XSS 攻击？分为哪几类？怎么防范？

### 什么是 XSS 攻击

cross site scripting，跨站脚本攻击。攻击者在目标网站植入恶意代码，利用用户对网站的信任，当攻击者登录网站时就会执行恶意代码。

### XSS 分类和方法手段

- 反射型 XSS：攻击者构造特殊 URL，包含恶意代码；用户打开具有恶意代码的 URL 时，恶意代码被从 URL 中取出，在客户端浏览器中执行，恶意代码窃取用户数据并发送到攻击者网站或者冒充用户执行指定操作
    - 对字符串进行编码
    - cookie 设置 httpOnly 和 Secury
- DOM 型 XSS：前端 JS 代码不够严谨，将不可信内容插入了页面上
    - 对 url 链接，使用 encodeURIComponent 转义
    - 对 html 内容进行转义
- 存储型 XSS：恶意脚本永久存储在服务器上，被返回并执行
    - 前端传递数据给服务器前，先转义/过滤
    - 服务器收到数据，存储到服务器前，先转义/过滤
    - 前端接收到服务器数据展示前，先转义过滤

其它防范 XSS 攻击的手段：

```config
Content-Security-Policy: default-src 'self'
<meta http-equiv="Content-Security-Policy" content="form-action 'self';">
```

- Content Security Policy，后者无法设置 report
    1. 禁止加载外域代码
    2. 禁止外域提交
    3. 禁止内联脚本执行
    4. 禁止未授权脚本运行
    5. 合理使用上报可以尽快发现 XSS
- 输入长度控制
- 输入内容控制
- 动作确认
- HTTPonly 和 Secure
- 验证码

# 算法 & 系统

## 分布式系统用什么算法排序？

map-reduce、归并

## 进程和线程的区别

进程是包含独立资源上下文的 CPU 工作时间单位，线程是共享进程资源上下文的 CPU 工作时间单位。

## 稳定排序

- 冒泡：稳定
- 选择：不稳定
- 插入：稳定
- 快速：不稳定
- 归并：稳定
- 基数：稳定
- 希尔：不稳定
- 堆：不稳定

## 死锁的四要素和定义

死锁定义：两个或者以上的进程在执行过程中，因资源争夺而造成的互相等待的现象，若无外力作用，无法继续推进。

死锁原因：

1. 系统资源不足
2. 请求顺序不适当
3. 资源分配不当

四要素：

1. 循环等待
2. 请求并保持
3. 互斥资源
4. 不可剥夺

死锁的解除和预防：

1. 预防
    - 优先级
    - 尝试获取的超时
    - 可抢占
    - 可共享资源不要设置为互斥
2. 避免
    - 进程启动拒绝
    - 资源分配拒绝
3. 解除
    - 剥夺资源
    - 撤销进程

# 框架 & 库 & 优化

## React 面试题

1. 类组件和函数组件之间的区别
    - 类组件可以使用 state 和钩子等特性
    - 函数式组件是建立 props 和页面渲染之间的映射的函数，也可以使用 hooks 接口实现部分上述功能
2. React 中的 refs？
    - 提供一种访问在 render 方法中创建的 DOM 节点或者其他 React 函数的方法。即这些元素的引用。
3. React 中如何处理事件？
    - 为了解决跨浏览器的兼容性问题，SyntheticEvent 实例被传递给事件处理函数，它是跨浏览器的事件包装器，拥有和浏览器原生事件相同的接口
4. state 和 props 的区别？
    - state 是组件自己管理的数据，控制自己的状态，可变
    - props 是外部传入的数据参数，不可变
5. 如何创建 refs？
    - 类组件：
        - 声明及初始化：`this.myRef = React.createRef();`
        - 绑定： `<div ref={this.myRef}></div>`
        - 函数绑定：`<div ref={this.input = input}></div>`
    - 函数式组件及 hooks：
        - 声明和初始化 `const ref = useRef(null);` 
        - 函数绑定 `<div ref={(divRef)=>ref.current = divRef}></div>`
        - 绑定 `<div ref={ref}></div>`
6. 什么是高阶组件？
    - 是接受并返回一个新组件的函数
    - 可以动态接受任何提供的子组件，但不会修改或者复制输入组件中的任何行为
7. 在构造函数调用 super 并将 props 作为参数传入的作用是？
    - 在调用 super() 之前，子类构造函数无法使用 this 引用（ES6 知识）
8. 什么是控制组件？
    - React 在诸如表单元素等元素时，包含表单的组件将跟踪其状态的输入值，并在每次回调函数触发时重新渲染组件。以这种方式由 React 控制其值的输入表单元素称为控制组件
9. React.createElement？
    - 参数：
        tag 或者组件名
        选项
        子节点
10. 什么是 JSX？
    - 新的语法糖，主要用于在 JavaScript 中直接加入 XML 内容，主要被 React 使用
    - 新的语法糖，主要用于在 JavaScript 中直接加入 XML 内容，主要被 React 使用
11. React 的生命周期？组件函数
    - 挂载：
        - constructor(props)：
            - 如果不初始化 state，或者不进行方法绑定，则不需要为 React 组件实现构造函数
            - 只能在构造函数中给 this.state 赋值；其它方法中赋值，应当使用 this.setState() 代替
        - getDerivedStateFromProps：
            - 在调用 render 之前调用，返回一个对象来更新 state, 如果返回 null 则不更新任何内容
            - 用于实现 state 的值任何时候都取决于 props 的情况，例如实现 `<transition></transition>`
            - 派生状态会产生冗余：
                - 执行副作用响应 props 的更改，使用 componentDidUpdate
                - prop 更改时，重新计算某些数据，使用 memoization helper
                - props 更改时重置某些 state，使用受控组件或者使用 key 使组件完全不受控
        - render：见下文
        - componentDidMount：
            - 依赖 DOM 节点的初始化
            - 挂载完成后，进行的副作用和订阅；记得在 componentWillUnmount 中移除
            - 在此处调用 setState 将触发额外渲染，但此渲染会发生在浏览器更新屏幕之间，避免闪烁；会造成性能问题；当渲染依赖于 DOM 节点的大小和位置时可以使用该模式
    - 更新：
        - getDerivedStateFromProps
        - shouldComponentUpdate(nextProps, nextState)：
            - 是否应该进行这次更新
            - 首次渲染和使用 forceUpdate 的时候不会调用此方法
            - 仅仅用于性能优化
        - render：
            - 该函数是 class 组件中唯一必须实现的方法
            - 检查 this.props 和 this.state 的变化
            - 返回以下类型之一
                - React 元素
                - 数组或者 fragments
                - Porals
                - 字符串或者数值类型
                - 布尔类型或者 null（什么都不渲染，为了支持返回 `test && <Child/>` 的模式）
            - 该函数是纯函数，在不修改组件 state 的情况下，每次调用时都会返回相同的结果，且不与浏览器直接交互
        - getSnapshotBeforeUpdate：
            - 用于在 DOM 更新前获取一些之前的 DOM 状态，应该返回 snapshot 的值和 null
        - compoentDidUpdate(prevProps, preState, snapshot)：
            - 更新后立即调用，首次渲染不会执行此方法
            - 对于 prop 、state 变化引发的变更，需要包裹在条件语句中，否则会触发死循环
            - 如果实现了 getSnapshotBeforeUpdate 则其返回值将作为第三个参数，否则为 undefined
    - 卸载：
        - componentWillUnmount：
            - 在组件卸载和销毁之前调用，在此函数中执行销毁操作
            - 不应该调用 setState，因为该组件永远不会重新渲染
    - 错误处理：如果组件定义了错误处理生命周期方法，则其为 Error boundaries，其会在子组件树中的任何位置捕获 JS 错误，记录错误并展示降级 UI（无法捕获本身的错误）
        - getDerivedStateFromError(error)
            - 在后代组件抛出错误后被调用。将抛出的错误作为参数，返回一个值以更新 state
            - 在渲染阶段出现，不允许出现副作用
        - componentDidCatch(error, info)
            - 在后代组件抛出错误后被调用，接收两个参数：
                - error —— 抛出的错误
                - info —— 带有 componentStack Key 的对象，包含有关组件引发错误的栈信息
            - 在提交阶段被调用，允许执行副作用，用于记录错误等
    - 其它 API
        - setState(updater, [callback])
            - 将对组件 state 的更改排入队列，并通知 React 需要使用更新后的 state 重新渲染此组件及其子组件。它是用于更新用户界面以及相应事件处理器和处理服务器数据的主要方式
            - setState 不会保证立即生效，会合并变更，延迟生效；
            - 不要建立多个 state 之间的值依赖，可能导致更新问题;可以使用 componentDidUpdate 或者 setState 回调函数；如果使用想要避免更新的性能损失，则可以使用 componentShouldUpdate
            - updater 为 (state, props) => stateChange 形式的函数；如果为对象类型，则使用此对象类型浅拷贝更新；可以使用 Object.assign 的形式合并多次变更
        - forceUpdate(callback)
            - 默认情况下，当组件 state 和 props 发生变化时才会更新组件；当依赖外部某个状态才会更新时，可以使用 forceUpdate 强制组件重新渲染
            - 会跳过 componentShouldUpdate
    - 类属性
        - defaultProps
            - 可以为类组件添加默认 props，用来设置 props 未赋值时的默认状态
        - displayName
            - 用于显示更多调试信息
    - 实例属性
        - props 义的 props
            - this.props.children 是特殊的 prop，通常由 JSX 表达式中子组件组成，而非组件本身定义
        - state
            - 包含了随时可能变化的数据，由用户定义，是普通 JS 对象
            - 不用于渲染和数据流的对象，直接设置在组件实例上
12. React portal?
    - ReactDOM.creatPortal(child, container)
    - 概述：Portal 可以将子节点渲染到存在于父节点以外的 DOM 节点
    - 参数：
        - 第一个参数是任何可渲染的 React 子对象；
        - 第二个参数是一个 DOM 元素
    - 用途：
        - 将子元素插入到 DOM 节点中不同位置，而不是最近的父节点；方便在父组件拥有 overflow: hidden 和 z-index 样式时能够在视觉上跳出容器
        - 无论子节点是否是 portal，内部触发的事件将沿着 React 组件树进行冒泡，而非 DOM 树；context 等功能特性不变
13. componentWillReceiveProps？
    - 反面模式：
        - 直接复制 prop 到 state：
            - 只要父组件重新渲染，getDerivedStateFromProps 和 componentWillReceiveProps 就会重新调用，而无论 props 是否变化；一个 prop 改变，会造成整体的 props 刷新，最终造成丢失 state
        - 在 props 变化后修改 state
            - 可能导致拥有同样账户的两个用户切换时，输入框不会重置
    - 建议的模式：
        - 完全受控的组件：从组件中完全删除 state，组件只响应 props
        - 有 key 的非可控组件：使用 userId 或者 hash 或者随机值作为 key 重新创建一个用户框（当有重逻辑时，该种方法避免 diff 反而加快）
        - 在 key 受限的情况下，使用 getDerviedStateFromProps 观察键的变化
        - 内部方法强制更新组件，使用 ref 进行调用
        - 仅在输入变化时，重新计算 render 需要的值，可以使用 memorization 
14. PureComponents？
    - 只有在 props 和 state 变化才会修改
15. Memorization？
    - 当参数改变时才会改变，否则返回旧值
    - 每个组件内部都要引入 memorized 方法，避免实例之间相互影响
    - memoization 帮助函数的缓存空间会有限制
16. Fragments？
    - 返回多个元素的通用模式
    - 写法 `<Fragments>` 或者 `<>`，不生成实际组件
    - key 是唯一可以给予 Fragments 的属性
17. 代码风格？
    - import("./xxx").then() 模式
    - const OtherComponent = React.lazy(() => import('./OtherComponent')) 
        - 可以像常规渲染组件一样异步返回组件
        - 等待时可以使用 Suspense 组件中渲染进行优雅降级，fallback 设置失败后的表现
        - 模块加载失败触发的错误，可以使用异常捕获边界来处理
    - 基于路由的代码分割
    - React.lazy 目前只支持默认导出，命名导出需要经过中间组件包装，tree-shaking 才不会出错
19. Context？
    - 提供了一种无须为每层组件手动添加 props，就能在组件树之间进行数据传递的方法
    - 何时使用 context
        - 对于组件树是全局的数据的共享
        - 只是想避免层层传递一些属性，可以使用组件组合；将组件自身传递下去可以减少需要传递的 props 的数量
        - 需要更高阶的使用，可以使用 render props
    - API
        - const XxxContext = React.createContext(defaultValue)
            - 创建一个 Context 对象，当 React 渲染了一个订阅了该 Context 对象的组件，这个组件会自动从组件树离自身最近的 Provider 中读取当前值；
            - 只有组件树中无匹配 Provider 时，defaultValue 才会生效；undefined 传递给它时，默认值不生效
        - Context.Provider `<MyContext.Provider value={}>`
            - 接收一个 value 属性，传递给消费组件。
            - 当值发生变化时，内部的所有消费组件都会重新渲染，不受制于 shouldComponent
            - 使用 Object.is 来检测变更
        - Class.contextType
            - 挂载在 class 上的 contextType 属性会被重新赋值为一个由 React.createContext() 创建的对象。使得自身可以在 this.context 上消费最近 Context 上的值，可以在任何生命周期中访问到，包括 render
        - Context.Consumer
            - `<MyContext.Consumer> {value => /* 基于 context 值进行渲染*/} </MyContext.Consumer>`
            - 需要函数作为子元素的做法
        - Context.displayName 可以在调试时提供更好的名字
    - 使用案例：
        - 动态 Context
        - 在嵌套组件中更新 Context：创建 context 时传递函数，来进行修改（依赖注入？）
        - 消费多个 Context：为了确保 context 快速进行重渲染，需要使得每一个消费者组件在组件树中称为一个单独的节点
20. Refs 转发？
    - 该技术可以自动地通过组件传递到子组件
    - 使用：
        - 通过 React.createRef 创建 React.ref
        - 将组件的 ref 绑定到该变量
        - 使用 React.forwardRef 创建绑定转发的组件
        - 绑定到内部节点
    - 在高阶组件中转发 refs：
        - refs 不会投传，因为其类似 key ，并不是 prop 属性，该方法可以转发
21. 高阶组件？
    - 组件是将 prop 转换为 UI，高阶组件是将组件转换为另一个组件
    - 不要试图在高阶组件中修改组件原型，而是使用组合方式修改
    - 约定：
        - 将不相关的 props 传递给被包裹的组件
        - 最大化可组合性
        - 包装显示名称便于调试
    - 问题：
        - 在 render 中使用高阶函数会导致无法 diff 而直接替换
        - HOC 后，无静态方法，需要手动复制
22. Profiler？
    - 可以以组件的形式使用测量性能
    - `<Profiler id="Navigation" onRender={callback}> <Navigation {...props} /></Profiler>`
    - onrender 回调
23. Render Props？
    - 在 React 组件之间使用一个为函数 prop 共享代码
    - 仅仅是将一个函数传递作为 props
    - 注意：
        - 可能抵消 PureComponent 的优势
        - 该模式可能需要设置 `Mouse.propTypes = {children: PropTypes.func.isRequired};`
24. 使用 PropTypes 可以帮助检查类型
25. Hooks 规则？
    - 本质就是 JS 函数
    - 只在最顶层使用 Hook，不在函数、条件和嵌套函数中使用 hooks，否则不能保证 Hook 在每一次渲染中按照相同顺序调用
    - 只在 React 函数中调用 Hook，不在普通的 JS 函数中调用 Hook
    - React 是通过 Hook 调用的顺序得知哪个 state 对应哪个 useState
    - 想要有条件的执行 effect，可以将判断放到 Hook 内部
26. 常用 Hooks？
    - useState
        - 函数式更新, setXxx(oldvalue=>newvalue)
        - 惰性初始化，传递一个函数
        - 使用 State Hook 的更新函数传入当前 state 时，React 将跳过子组件的渲染
    - useEffect
        - 执行副作用
        - 返回函数会被执行，可以帮助清除副作用
        - 浏览器完成布局绘制之后，传给 useEffect 的函数延迟执行
        - 传递第二个参数，是依赖的函数，若未改变，则不会执行
    - useContext
        - 接收的参数必须是 Context 本身
    - useReducer
        - 接收 reducer 为 (state, action) => newState，返回当前 state 和 dispatch 方法
        - 使用 useReducer 给触发深度更新的组件做性能优化
        - 将初始化函数作为第三个参数传入，可以惰性初始化
        - 如果返回值和当前 state 相同，跳过子组件渲染和副作用执行
    - useCallback
        - 返回 memoized 回调函数
    - useMemo
        - 返回 memoized 值，在渲染期间执行，不要执行无关操作
    - useRef
        - 返回可变 ref 对象
    - useImperativeHandle
        - 在使用 ref 时自定义暴露给父组件的实例值，应当与 forwardRef 一起使用
    - useLayoutEffect
        - dom 更新后同步触发重渲染
    - useDebugValue
        - 在 React 开发工具中显示自定义 hook 标签
27. 自定义 Hook？
    - 定义一个 use 开头的函数，内部可以定义其它 hooks
28. redux 架构？dispatch？
    - action => 描述如何改变 store，一般同步，可以借助中间件实现异步
    - reducer => 纯函数，根据旧 store 计算新 store
    - dispatch => 发起 action
    - store => 仓储，唯一，存储一颗状态数
    - state => 状态，只读
    - subscribe => 订阅更新，其它组件可以通过此刷新自己的视图
    - connect 将 store 的 getState 和 dispatch 包装成组件 props

## Angular 脏数据检测的原理

早期 angular 使用从树顶到底下进行脏检查，每个组件拥有自己的检查函数

zone.js 对异步事件做代理包裹。ApplicationRef 类，监听 ngZone 中的 onTurnDone 事件，只要触发事件，执行 tick 方法对变更做检测

## VueX 相关问题汇总

1. **VueX 是什么？** vuex 是 vue 的插件，目的是实现跨组件的状态量管理；唯一改变状态的方法是提交 mutation
2. **VueX 解决了什么问题？**
    - 多个组件依赖同一状态时，对于多层嵌套的组件的传参非常繁琐，并且兄弟组件之间传递状态无能为力
    - 通过引用方式维护则非常脆弱
    - 注：（react可以通过redux、context、组件传递来解决部分；angular 可以通过服务+依赖注入来解决问题；甚至可以自己实现消息总线来解决部分）
3. **什么时候使用 VueX？** 
    - 多个组件依赖同一个状态
    - 不同组件的行为需要变更同一个状态
4. **怎么引用 VueX**
    - npm install
    - import VueX from `vuex`
    - Vue.use(VueX);
    - const store = new Vue.Store({/* */})
    - new Vue({store /* */})
    - this.$store
5. **vuex 的五个核心属性**
    - state
    - getters(state, gettersRef)
    - mutations(state, data) 同步 commit(fnName, data)
    - actions(context, data) 异步 dispatch(fnName, data)
    - modules
6. **状态存储在哪，怎么改变？** 存储在 state、唯一改变的途径是显式提交 mutation
7. **状态时对象时，要注意什么？** 注意顶层 const 和 底层 const
8. **在组件中批量使用 state 状态？** `computed: { ...mapState(['price', 'number']) }`
9. **要从属性中派生出一些共用属性？** 使用类似于 computed 的 getters(state, gettersRef)
10. **怎样给 getters 传递参数？** 利用高阶函数
11. **批量利用 getters？** `computed: { ...mapGetters(['price', 'number']) }`
12. **批量给 getters 取别名？** `computed: { ...mapGetters({a:'price', c:'number'}) }` 
13. **批量利用 mutations?** `methods: { ...mapMutations({/**/}) }`
14. **action 和 mutation 区别？** action 提交 mutation，action 可以包含异步动作，action 第一个参数是 context，包含 state、rootState、commit、dispatch、getters、rootGetters
16. **action 和 mutation 相同点？** 可以改变参数和接收状态
17. **多次提交 action？** methods mapActions
18. **异步 action 处理？** 返回 promise
19. **嵌套异步 action？** async await
20. **vuex 模块系统？** 
    - 每个模块拥有自己的状态和方法，通过在 Store 的 modules 中注册引入；
    - 模块中的 state、mutation 都是局部的
    - 想要访问全局，getter 可以通过第三个参数 rootState 第四个参数 rootGetters 访问
    - mutation 不能访问全局
    - action 可以通过 context 访问
21. **vuex 命名空间？** 
    - 添加 namespaced: true 可以避免 action、mutation 的全局分发，只分发最细化的
    - 若要提交全局，传递 options `{root: true}`
    - 在命名空间内定义全局 action，使用对象 actionA: `{root: true, handler: fn() {}}`
    - 提交模块 mutation: `commit('module/mutation', data)`
    - 映射模块 state 等 `const { mapState, mapActions } = createNamespacedHelpers('moduleA')`
22. **VueX 插件？** 一个可以映射 store 的函数，在 Store 中以 plugins 引入
23. **VueX 监听提交变更?** 使用插件，在 store 上订阅 `subscribe((mutation, state)=>{})` 和 `subscribeAction({before:(action,state)=>{}, after: (action: state=>{})})`
24. **VueX 严格模式？** `strict: true`

## Vue-Router 相关问题汇总

1. **基本使用？** `VueRouter({routers: [{ path: '', component: xxx }]})`
2. **重定向?**
    - `redirect: '/path'`
    - `redirect: { name: 'xxx' }`
    - `redirect: ({hash, params, query}) => Route`
3. **配置 404 页面？** `path: '*', redirect: { path: '/' }`
4. **切换路由，保存草稿？**
    - 后端保存
    - `<keep-alive :include="include"><router-view></router-view></keep-alive>`
5. **路由模式？** 
    - hash 兼容所有浏览器，hash 改变会触发 hashchange 事件，event 包含 newURL 和 oldURL
    - history/browser 浏览器的 HTML5 History API，初次访问或者刷新会向服务器请求，服务器需要设置通配符
    - abstract 没有浏览器下的模式
6. **导航守卫流程？**
    1. 导航被触发
    2. 在失活的组件里调用离开守卫 beforeRouteLeave(to,form,next)。
    3. 调用全局的 beforeEach( (to,form,next) =>{} )守卫。
    4. 在重用的组件里调用 beforeRouteUpdate(to,form,next) 守卫。
    5. 在路由配置里调用 beforeEnter(to,form,next)路由独享的守卫。
    6. 解析异步路由组件。
    7. 在被激活的组件里调用 beforeRouteEnter(to,form,next)。
    8. 在所有组件内守卫和异步路由组件被解析之后调用全局的 beforeResolve( (to,form,next) =>{} )解析守卫。
    9. 导航被确认。
    10. 调用全局的 afterEach( (to,form) =>{} )钩子。
    11. 触发 DOM 更新。
    12. 用创建好的实例调用beforeRouteEnter守卫中传给 next 的回调函数
7. **导航守卫和实例生命周期钩子？** 导航守卫在所有实例生命周期钩子前执行
8. **导航守卫的三个参数** 
    - to 上一个路由
    - from 下一个路由
    - next
        - 无参数调用，跳转
        - false，中断
        - Router，进行新的导航
9. **afterEach 不接受 next**
10. **全局守卫？路由独享守卫？**
    - 全局 beforeEach, beforeEnter, beforeResove, afterEach
    - 路由独享 beforeRouterEnter, beforeRouterLeave, beforeRouterUpdate 
11. **全局、路由守卫使用方式？**
    - 在路由定义中定义对应守卫、钩子
    - 全局守卫使用方式 router.hooksName(fn)
12 **beforeRouterEnter 可以使用 this 吗？** 不可以，组件生命周期未开始；但可以在 next 中传递回调函数
13. **router-link？** 是  Vue-Router 内置组件，用于声明式导航。to、active-class、exact-active-class、exact、tag、append、replace 等属性
14. **组件中监听路由参数的变化？**
    - beforeRouteUpdate
    - watch: $route
15. **切换路由后新页面滚到顶部或者保持当前位置？** scrollBehavior(to、from、savedPosition) => {} 或者在钩子里设置
16. **嵌套路由？** 跨页面通用组件（顶部栏/左侧菜单栏）
17. **命名视图？** `<router-view name="">` 对于同个路由，过个视图需要多个组件，需要在 components 上正确设置 `resolve => require([], resolve)`
18. **获取路由传递参数？**
    - meta, `this.$route.meta.title`
    - query, `this.$router.query.userID`
    - params, 只能对命名路由传参，`this.$router.push({ name: 'home', params: { userId } })`，`this.$route.params.userId`
19. **路由组件和路由为何解耦？怎么解耦合？** 避免组件只能在特定 URL 上使用；使用 props 来接耦合，对于包含命名视图的路由，必须分别为每个命名函数视图添加 props 选项
20. **动态加载路由？** `component: ()=>import('xx') vm.$router.options.routes.push(...routes); vm.$router.addRoutes(routes);`
21. **懒加载？**
    - ()=>import('xx')
    - resolve => require(`xxx`, resolve);
22. **路由跳转？**
    - `<router-link>`
    - `router.push/replace`
23. **跳转打开新窗口？** `const {href} = this.$router.resovle(obj); window.open(href, '_blank');`

## Vue 相关问题汇总

1. 兼容版本？
    - IE10 Object.defineProperty() ES5
    - 后续版本使用 Proxy ES6
2. 生命周期？
    - beforeCreate
    - created
    - beforeMount
    - mounted：$el 可用，等待视图更新完毕使用 vm.$nextTick
    - beforeUpdate
    - updated
    - activated
    - deactivated
    - beforeDestory
    - destoryed
3. data computed methods 能否重名？
    - 不能重名
4. 箭头函数：
    - 不能使用，this 指向定义时的 this
5. watch 监听后立即调用？
    - 参数中指定 immediate: true
6. watch 深度监听变化？
    - 参数中指定 deep: true
7. 强制刷新组件
    - this.$forceUpdate
    - 组件加上 key，变化 key 的值
8. 组件绑定自定义事件无效？
    - 加上修饰词 .native
9. 访问实例？
    - this.$refs.name
    - this.$parent
    - this.$root
10. 组件销毁？
    - 没有 keep-alive
    - v-if
    - vm.$destory
11. is 属性？
    - 动态组件
12. 组件通信
    - props
    - $emit
    - $someRef.$on $someRef.$emit
    - $someRef
    - $root
    - $parent
    - provide, inject
    - vueX
13. prop 的验证 type 类型
    - 常见基本类型和构造函数 instanceof
14. prop 的验证和默认值
    - `[Type1, Type2]`
    - `{type:Type,default:''}`
    - `Type`
15. event 的使用方法
    - `@click="handlerOpen"`
    - `@click="handleOpen(0, $event)"`
16. $event.target 和 $event.currentTarget
    - 发生元素
    - 绑定元素
17. 表单修饰符
    - .number .lazy .trim
18. 事件修饰符
    - .stop .prevent .capture .self .once .passive
19. 事件修饰符顺序很重要
20. .lazy 的理解
    - 不会理解监听变化，失去焦点后监听
21. 键盘事件监听
    - .enter .tab .delete .esc .space .up .down .left .right
22. 常用指令
    - v-show v-if v-else v-else-if v-for v-on v-bind v-model v-once v-slot v-html v-text
23. v-once 的使用场景
    - 只渲染一次的元素和组件
24. v-show 和 v-if 
    - 隐藏和消失
25. v-on 绑定多个方法
    - 接一个对象，不支持事件修饰符
26. v-cloak
    - 在页面渲染时将未编译标签显示，用于调试
27. v-pre
    - 跳过该元素和子元素的编译过程
28. class 和 style 的动态绑定方式
    - 数组
    - 对象
    - 数组和对象混合
    - 对象和计算属性
29. vue 文件中 style 和 script 组件是必须的吗？
    - 非
30. 只对当前组件生效的样式？
    - style scoped
31. 公共组件使用 scoped 的修改样式需要使用 deep
32. scoped 原理？
    - 在 DOM 结构和 CSS 加前缀
33. 保留 html 注释
    - comments: true 组件级
    - `<template comments>`
34. vm.$nextTick？
    - 下次 DOM 更新循环后执行
35. 重置 data？
    - `Object.assign(this.$data, this.$options.data)`
36. Vue 的 created 和 mounted 生命周期中请求数据的区别
    - created 页面视图未出现，如果请求信息过多，会长时间白屏
    - mounted 已经挂载，但渲染不一定完成，若需要请求非紧急任务，可以使用 $nextTick 函数
37. v-model 的原理
    - on 和 bind 的语法糖
38. keep-alive 的理解
    - keep-alive 是抽象组件，本身不会渲染 DOM 元素，也不会出现在父组件链中，使用 keep-alive 包裹动态组件时，会缓存不动的组件实例
    - 参数：
        - include 定义缓存白名单
        - exclude 定义缓存黑名单
        - 参数可以是逗号分隔字符串、数字或者正则表达式
        - 匹配首先检查组件自身的 name 选项，如果不可用，则匹配它局部注册的组件名称。匿名组件不能被匹配
        - max 最多可以被缓存多少实例，即缓存大小
        - 当组件在内被切换时，其 activated 和 deactivated
39. v-for 和 v-if 的优先级
    - v-for 优先级高于 v-if ，如果需要批量 v-if 可以在外层设置，或者使用 `<template>`
40. v-for 遍历对象的顺序？
    - Object.keys()
41. v-for 中使用 key ，会提升性能吗？
    - 简单的列表，提升不大甚至有所降低
    - 复杂的列表或者存在其它有复杂原位操作的节点，有提升
42. key 除了 v-for 之外的其它使用场景？
    - 强制替换元素而非重复使用，修改某节点的 key
43. key 使用的注意
    - 不要使用对象或者数组之类的非基本类型值作为 key
    - 不要使用数组的 index 来作为 key
44. 组件的命名规范？
    - 字符串模板可以使用 `<my-component>` 或者 `<MyComponent>`
    - 非字符串可以使用 `<MyComponent>` 遵循 W3C 规范，自定义组件应该全小写且包含连字符，防止和未来 html 标签冲突
45. 组件中的 data 必须使用函数返回对象？
    - 重用组件时，数据对象指向同一个引用，使用函数返回对象避免修改同一引用
46. Vue 父子组件双向绑定的方法
    - bind、props 和 on、emit
    - v-model
    - .sync 修饰符，父组件 `<myComponent :show.sync="show"></myComponent>`，子组件 `this.$emit('update:show',data)`
47. 组件的 name 选项
    - 递归组件时，组件调用自身
    - is 特殊属性
    - keep-alive 的 include 和 exclude
48. 递归组件？
    - 组件调用自身，注意配合 v-if 避免死循环，常见场景有 NavMenu
49. 插槽的使用？
    - 待插入组件使用 `<slot>` 或者 `<slot name="">` 的方式定义插槽
    - 调用时，默认插槽直接在标签内插入内容，其它插槽按照 `v-slot:name` 或者 `#name` 的方式声明 `<template></template>` 及其中的插入内容
50. $attrs 和 $listeners 的使用场景？
    - $attrs 包含副作用中不作为 prop 被识别的特性绑定。在创建基础组件时候经常使用，可以和组件选项inheritAttrs:false和配合使用在组件内部标签上用v-bind="$attrs"将非prop特性绑定上去
    - $listener 包含了父作用域中不含 .native 的 v-on 事件监视器
51. 组件的依赖注入？
    - provide 写在祖先组件中，inject 写在需要注入的子孙组件中；
    - provide 是一个对象或者返回对象的函数
    - inject 是一个字符串数组或者是一个对象（对象的key是本地绑定名，value是provide的key）
55. EventBus 注册在全局上时，路由切换时重复触发事件的解决方式？
    - 使用 $on 的组件中要在 beforeDestory 钩子中使用 $off 销毁
56. Vue 组件里写的原生 addEventListener 监听事件，要手动销毁吗？
    - 要，否则会多次绑定和内存泄露
57. Vue 组件的定时器的销毁？
    - 可以定义在一个定时器数组中，在组件销毁前依次销毁
58. Vue 中可以监听数组变化的方法
    - push pop shift unshift splice sort reverse vue 拦截并代理了这些方法
    - fiter、concat、slice 会返回新数组
59. Vue 不可以监听哪些数组方法？
    - 使用索引直接设置一个数组项时，Object.defineProperty() 理论上可以监听下标访问，但是代价很大，且未知位置的索引不行；可以使用 this.$set(target, property, data) 来解决
    - 修改数组的长度时，Object.defineProperty() 无法监听 length；可以使用 splice(startIndex, count, ...data) 解决
60. vue 中无法监听变化的对象？为什么？怎么解决？
    - 对象属性的添加和删除无法监听，它们只能追踪现有属性的变化，而无法监听新增和删除
    - 可以使用 this.$set 来添加，Object.assign 来删除
61. delete 和 Vue.delete 的区别？
    - delete 删除对象成员
    - Vue.delete 直接删除了对象成员，并触发视图更新
62. watch 和 computed 的区别？
    - watch，一个数据影响对个数据，当需要数据变化时，执行异步或者开销较大的操作时
    - computed，一个数据受多个数据影响
63. computed 和 methods 的区别？
    - 前者响应式 + 缓存，后者每次更新都会触发
64. 自定义指令和自定义指令钩子？
    - Vue.directive('color', options)，使用 v-color 调用
    - 钩子：
        - bind：在指令第一次绑定到元素时调用
        - inserted：被绑定元素插入父节点时调用
        - update：所绑定节点的 VNode 更新时调用，调用时指令的值不一定改变，通过比较更新前后的值来忽略不必要的模板更新
        - componentUpdated：指令所在组件 VNode 及其子 VNode 全部更新之后调用
        - unbind 指令与元素接绑时调用
    - 钩子函数的参数：
        - el：指令所绑定的元素，可以直接操纵 DOM
        - binding：
            - name 指令名
            - value 绑定的指令值
            - expression 指令的绑定表达式
            - arg 传给指令的参数
            - modifiers 一个包含修饰符的对象
            - oldValue 指令绑定的前一个值
        - vnode Vue 编译生成的虚拟节点
        - oldVnode 上一个虚拟节点，仅在 update 和 componnentUpdated 钩子中可用
65. vue 定义全局方法？
    - 挂载在 Vue 的 prototype 上
    - 利用全局混入 mixin
    - 用 this.$root.$on 绑定方法，$off 解绑方法，$emit 全局调用
66. 对 DOM 选项 el、template、render 的理解
    - el 提供一个 CSS 选择器或者 HTMLElement 实例作为 Vue 实例的挂载对象，该元素会被 Vue 生成的 DOM 替换；如果在 const vm = new Vue({}) 中存在该选项，实例将立即进入编译过程，否则需要显示调用 vm.$mount() 手动编译
    - template 一个字符串模板作为 vue 实例的标识，如果 el 存在，vue 使用其中的内容替换 el
    - render 函数若存在，则 Vue 会首先使用 render 方法进行渲染
67. `<template>` 的作用
    - 当作不可见的包裹元素，减少不必要的 DOM 元素
68. 改变插入模板的分隔符
    - delimiters 选项，默认是 `["{{","}}"]`
69. 以 _，$ 开头的 Vue 属性发生问题？怎么访问到值？
    - 它们不会被 Vue 代理，因为可能和 Vue API 冲突，可以使用 vm.$data._property 来访问
70. Vue 组件错误信息的绑定？
    - errorCaptured 是组件内部钩子，捕获子组件的错误，接收 error、vm、info 三个参数，return false 可以阻止向上冒泡
    - errorHandler 为全局钩子，使用 Vue.config.errorHandler 配置，接收参数与 errorCaptured 一致
71. Vue.observable？
    - 让对象可响应，作为最小化的跨组件状态存储器
72. favicon 的配置？
    - 静态配置 `<link rel=icon href="<%= BASE_URL %>favicon.ico">`
    - 动态配置 `<link rel="icon" type=<mime> href="">` 
73. 打包后生成文件路径？
    - publicPath
74. 打包后静态资源失效，webpack -> resolve -> alias -> 'xx' : resolve('path')
75. 动态设置 img 的 src 不生效，被当做静态资源处理，没有贬义，需要在 data 中设置，并加上 require('path')
76. Vue 的模板引擎
    - Mustache.js
77. Vue 的核心
    - Vue.js 的核心是一个允许采用简单的模板语法来声明式地将数据渲染进 DOM 的系统
78. 单向数据流和双向数据流的理解？
    - 单向是数据只能通过父级向子级传递
    - 双向是数据向子级流动，通过事件等方式向父级流动
79. vue 中实现虚拟 DOM？
    - 新建 VNode 类，添加对应 DOM 节点上将要存在的属性。并将这些节点进行分类
    - 通过编译将模板转换成渲染函数 render，执行渲染函数 render，生成不同的 VNode 类
    - 通过 patch 将 vnode 和 oldVNode 比较后，生成真实 DOM
80. vue 的 diff？
    - 见 diff、react diff 和 vue diff 比较
81. vue.nextTick 的原理？
    1. 用异步队列的方式控制 DOM 更新和 nextTick 回调先后执行
    2. 利用 microtask 队列的高优先级特性，确保任务中的微任务在一次时间循环前被执行完毕
    3. 使用 promise、使用 setImmediate 、messageChannel、setTimeout 作为降级方案
    4. 设置异步锁，使得该轮任务执行完毕，新的才能加入任务队列
82. 说说你对Vue的template编译的理解？
    1. 通过编译器将 template 转化为 AST 树
    2. AST 树生成 render 函数，render 函数生成 VNode
83. Vue实例挂载的过程是什么？
    - 检测不是根节点（因为是替换）
    - 调用 render 生成虚拟 Node，实例化一个渲染 Watcher，回调函数中调用 updateComponent，最终调用 vm._update 更新 DOM，设置 vm._isMounted 表示已经挂载
84. 说下你对指令的理解？
    - 是带有 v- 前缀的特殊 atrribute，当表达式改变时，将其值改变时，将其产生的连带影响，响应式的作用于 DOM
85. 组件为何只能有一个根元素？
    - 树状结构要求只能有一个根，方便对 VDOM 进行 diff
86. EventBus？
    - 一个 Vue 组件
    - 通过对其进行事件触发和事件监听进行通信
87. 使用Vue后怎么针对搜索引擎做SEO优化？
    - ssr 单页面服务器渲染
    - meta 信息
    - 主动向搜索引擎提交 site.xml
    - nuxt
    - phantomjs
88. 为何官方推荐使用axios而不用vue-resource？
    - vue-resource 不再维护了
    - axios 更加强大和通用
    - 有 nodejs 版本
    - 可以拦截、转换 JSON、防御 XSRF、取消请求、支持 Promise
89. 使用 vue 如何做接口管理？
    - 对上下文访问路径、超时时间、错误逻辑、拦截逻辑做统一处理，封装成文件，引入封装后对象
90. 说说你对Vue组件的设计原则的理解
    - TypeScript 万岁，可是 Vue2 支持不佳
    - 容错处理要做好
    - 颗粒化
    - 高可配置性
    - 语义化、命名良好
    - 规范化
91. 写出多种定义组件模板的方法
    - 字符串
    - 模板字面量
    - `<script type=x-template></script>`
    - 文件组件模板
    - inline-template
92. 你有使用过render函数吗？有什么好处？
    - 更加自由、动态函数式的生成 VNode
    - 尤其在生成动态表格时好用
    - 便于 react 迁移
93. 你了解什么是函数式组件吗？
    - `createElement(tag/element-name, options { props, on, styles ... }, children)`
94. 你有使用过JSX吗？说说你对JSX的理解？
    - 新的语法糖，主要用于在 JavaScript 中直接加入 XML 内容，主要被 React 使用
95. 如果想扩展某个现有的Vue组件时，怎么做呢？
    - Vue.extend 拓展
    - Vue.mixin 混入
    - slot 拓展
    - HOC 封装
96. 你了解什么是高阶组件吗？可否举个例子说明下？
    - React 中返回组件的函数
    - 设计原则：
        - 无副作用的纯函数
        - 不关心传递的 props
        - 接收到的 props 透传给包装组件
    - Vue 中可以通过 mixin 实现，也可以通过 render 实现
97. 怎么在Vue中使用插件？
    - vue.use
    - vue.$plugin
98. 组件和插件有什么区别？
    - 组件用来生成 VNODE tree
    - 插件用来提供某种全局功能
99. 为什么Vue使用异步更新组件？
    - 多次进行修改后，实际只需进行最后一次修改
    - 减少阻塞
100. 你有看过Vue推荐的风格指南吗？列举出你知道的几条？
    - 组件名为多个单词组件数据：组件的 data 必须是一个函数。
    - 细致的 Prop 定义
    - 总是用 :key 配合 v-for
    - 避免 v-if 和 v-for 用在一起
    - 为组件样式设置作用域
    - 私有属性名：自定义私有属性使用 $_ 前缀。并附带一个命名空间以回避和其它作者的冲突 (比如 $_yourPluginName_)
101. 为什么我们写组件的时候可以写在.vue里呢？可以是别的文件名后缀吗？
    - webpack 的 vue-loader，检测逻辑就是 .vue，使用该后缀，IDE 和开发插件支持和识别更好
    - 也可以写在 js, jsx, ts, tsx 只要检测逻辑和配置支持
102. vue-loader是什么？它有什么作用？
    - 是一个 webpack 的 loader
    - 可以解析和转换 vue 文件，识别提取并分离模板、样式和逻辑并分别交给对应 loader 处理
103. 分析下Vue项目本地开发完成后部署到服务器后报404是什么原因呢？
    - 后端资源映射不对
    - vue 配置中指向输出路径的 publicPath 不对
    - resolve 有问题
    - history 模式，后端没有进行范围匹配
104. vue打包成最终的文件有哪些？
    - index.html
    - vendor.js
    - app.js
    - app.css
105. 如何解决vue打包vendor过大的问题？
    - 在 externals 配置中配置 CDN 引入的文件
    - 路由懒加载
106. webpack打包vue速度太慢怎么办？
    - 升级到支持多进程的 webpack4
    - 视条件减少 babel 和 polyfill 转译条件
107. vue部署上线前需要做哪些准备工作？
    - history 的服务端范围匹配
    - publicPath
    - cdn
    - assetsPublicPath
108. 你有使用过vue开发多语言项目吗？说说你的做法？
    - vue-i18n
109. 用vue怎么实现一个换肤的功能？
    - 动态引入 css，重新加载页面
110. 对于即将到来的vue3.0特性你有什么了解的吗？
    - proxy 代替 definePropertyOf
    - composition api
    - 更好的依赖注入
    - 原生 typescript
    - API 变得太快 = class component -> functional API -> composition API

## Vue 的父子组件之间的通信方式

1. 父组件向子组件 props，单向数据流
2. 子组件向父组件 emit event, 事件

children:

```vue
props: {
    msg: {
        type: String,
        default: () => {
            return ' ';
        }
    }
},
methods: {
    say() {
        this.$emit('say', this.data1);
    }
}
```

parent:

```vue
<children :msg=msg @say=parentSay>
```


此外父组件可以通过子组件的 refs 触发子组件的事件，还可以使用 VueX 和自己写总线的方式进行沟通。

```vue
<children ref="childrenRef"></children>
//...
this.$refs.childrenRef.func()
```

## SPA 的理解，优缺点？

SPA 是一种将单个页面加载到服务器中的 WEB 应用，当浏览器向服务器发出请求时，客户端 APP 会随着首个 index.html 一起返回。url 变化不向服务器请求页面，通过路由实现页面切换。

### 优点

1. 交互体验良好，不需要重新刷新页面、获取资源，数据通过 ajax 异步获取，比较流畅
2. 前后端分离
3. 减小服务端动态渲染的成本192

### 缺点

1. SEO 难度高
2. 首屏加载速度慢

## SPA 的实现方式？

- hash 模式， window 上监听 hashchange 事件，驱动界面变化
- history 模式，window 上监听 popstate 事件，驱动界面变化，监听 a 链接点击事件用 history.pushState、history.replaceState 方法驱动界面变化

## 对 MVC、MVP、MVVM 模式的理解

早期开发中，界面布局、界面交互逻辑、业务逻辑代码混杂，随着业务需求扩大，代码复杂化，开发变困难，维护代码困难：

- MVC M 代表 Model，专门处理、存储数据；V 代表 View，将处理好的数据库发到 View。C 代表控制器，处理业务逻辑；用户操作 View，其发送指令到 Control，Control 处理业务逻辑后，要求 Model 处理响应数据并将数据发送给 View，View 将数据展示给用户 —— 三者相互影响，修改麻烦
- MVP P 代表 Presenter，Presenter 将数据发送给 View，要求 View 把数据展示；Presenter 和 View 相互依赖；使得 View 和 Model 不再相互耦合 —— Presenter 和 View 仍然相互依赖，无法做单元测试
- MVVM，VM 代表 ViewModel，于是只要建立 ViewModel 和 View 的映射，ViewModel 和 Model 的映射即可，相互之间的耦合，相对解除

## hybrid是什么？

抽象上说是混合模式移动应用，常见的有 原生 + WebView、ReactNative、Ionic 等；实际上国内常代指某一早期实现

## hybrid js如何调用native接口？

例如使用 mWebView 的  @JavascriptInterface 定义 interface，调用为 window.Android.functionName()

## webpack编译和构建原理 ?

流程概括：

1. 初始化参数：从配置文件和 shell 语句中读取和合并参数，得到最终的参数
2. 开始编译：用上一步得到的参数初始化 Compiler 对象，加载所有配置的插件，执行对象的 run 方法开始执行编译
3. 确定入口：根据配置中的 entry 找到所有的入口文件
4. 编译模块：从入口文件出发，调用所有配置的 Loader 对模块进行翻译，再找出该模块依赖的模块，递归本步骤直到所有入口依赖的文件都经过本步骤的处理
5. 完成模块编译：在经过第四步使用 Loader 翻译完所有模块后，得到每个模块被翻译后的最终内容和他们之间的依赖关系
6. 输出资源：根据入口和模块之间的依赖关系，组装成一个个包含多个模块的 chunk，将每个 chunk 转化成一个单独的文件加入到输出列表
7. 输出完成：确定好输出内容后，根据配置确定输出的路径和文件名，把文件内容写入到文件系统

## babel转码流程 ？

babel 及其插件会按照参数被加载到 Webpack 的 Compiler 对象中（使用 webpack + babel 的一般流程）

- 词法分析和语法分析，将字符串流转换成令牌流，再转换成 AST 树
- 然后经由 Transformer（babel-traverse） 转换成目标语言的 AST 树，过程中有添加、更新和移除等操作；babel-traverse 使用 visitor 对象来供我们获取 AST 中具体节点，使用 path 表示对节点之间连接的访问
- 最终使用 Generator（babel-generator）深度优先遍历，生成目标语言，同时还会按要求生成源码映射

## 什么是虚拟 DOM

虚拟 DOM 是将状态映射为视图的众多解决方式中的一种，通过状态生成一个虚拟节点树，然后使用虚拟节点树生成 DOM，生成之前会使用不同的 diff 算法，只渲染不同的部分

## 传统 diff、Vue diff、React diff

### 传统 diff

递归循环每个节点，O(n^3)

### React 的 diff

相同类型的两个组件产生的 DOM 树结构相似，对于同一层级的一组子节点，先序深度优先遍历树并标号，遍历过程中，对比新旧节点，将差异记录下来在 patches 中；

#### tree diff:

分层求异

#### component diff

- 同类型的组件，进行 tree diff，其间检查 shouldComponentUpdate 钩子来判断是否需要更新
- 不同类型的组件，替换整个组件下的所有节点

### element diff

差异类型：

1. 插入
2. 移动
3. 删除
4. 文本内容改变
5. 属性改变（包括 className 改变）

列表对比，设置 element key，默认为标号；顺序从前到后，注意减少最后一个节点向前的移动

### Vue 的 diff

同层级比较，使用 VNode 描述节点，调用 patch 函数：

- 对于不同元素、不同 className 认为是不同节点
- 让 vnode.el 引用现在的真实 el
- 如果节点引用相同，认为无变化
- 如果文本都是文本节点且文本不同，更新节点文本
- 如果挂载出现变化，更新挂载
- 对子节点进行插入、删除或者更新

更新子节点的方式：

- 新旧子节点各有 startIdx 和 endIdx
- 从两端到中间相互比较，四种比较方式，若都未匹配且设置 key，则用 key 比较
- startIdx > endIdx 时比较结束

## axios 是什么？怎么使用？怎么解决跨域问题？

是一种和 ajax 类似的异步请求包，包括 get、post、put、patch、delete 方式，解决跨域可以在服务器响应中添加 Access-Control-Allow-Origin

## ajax、fetch、axios

- ajax 是概念，传统上指基于 XMLHttpRequest 的一种实现，通过 success 和 error 回调函数返回
- fetch 是浏览器实现的 webAPI，基于 Promise 对象原生封装
- axios 是一个基于 ajax 封装的库，返回 Promise

## 你有封装过axios吗？主要是封装哪方面的？

统一上下文请求路径、超时时间、路径、错误处理、拦截、提示和 loading

## 如何中断axios的请求？

和 C# 一样，cancelToken，当调用 cancel 即可

## 如果将axios异步请求同步化处理？

1. then 链
2. async await 
3. asyncIterable
4. Observable Promise

## 你了解axios的原理吗？有看过它的源码吗？

对 ajax 的封装，添加了拦截、自动 json、错误处理、promise 化等内容；没看过

## 如何优化首页的加载速度？
   
- ssr
- cdn
- pwa
- 懒加载和延迟加载
- 骨架屏
- 渐进、并发（WebWorker、React Concurrency）、部分渲染
- 分块、分包、tree shaking

## 渲染大量数据时应该怎么优化？

- 设置 key
- 分页
- 虚表（只渲染需要的数据）
- Object.freeze 避免 vue 的响应式工作
- 时间分片，渐进式渲染（处理部分数据 -> 渲染 -> 处理部分数据 -> 渲染）

## 你有使用过babel-polyfill模块吗？主要是用来做什么的？

babel 默认只转译语法，babel-polyfill 转换新 API 以保证兼容

## Webpack 打包优化

- webpack 4+ 多进程打包
- externals 部分使用 CDN
- 减少不必要的 混淆、 babel 和 polyfill
- 分包、按需懒加载
- tree shaking
- splitChunks
- 提取 css


# 手写代码

## 用 JS 描述一棵树

```js
// 二叉树
class TreeNode {
    constructor(value, left, right) {
        this.value = value;
        this.left = left;
        this.right = right;
    }
}

// 多叉树
class TreeNode {
    constructor(value) {
        this.value = value;
        this.children = [];
    }
}
```

## 深拷贝

```js
const isInTypes = function(obj, ...types) {
    for(const t of types) {
        const protoStr = Object.prototype.toString.call(t.prototype);
        if( (Object.is(obj.constructor, t)) || 
        ((protoStr!=='[object Object]') &&
         (Object.prototype.toString.call(obj)===protoStr))
        ) {
            return true;
        }
    }
    return false;
}

const isPrototype = function(value) {
    return value === (value.constructor.prototype || Object.prototype);
}

const GeneratorFunction = Object.getPrototypeOf(function*(){}).constructor;

const baseClone = function(obj, memo = new Map()) {
    // for primitive
    if(Object.is(obj, null) || (typeof obj!=='function' && typeof obj!=='object')) {
        return obj;
    }
    // for memo
    if(memo.get(obj)!==undefined) {
        return memo.get(obj);
    }
    const ctor = obj.constructor;
    let target = null;
    // for wrapped primitive
    if(isInTypes(obj, Number, String, BigInt)) {
        target = new ctor(obj);
    }
    else if(isInTypes(obj, Boolean, Date)) {
        target = new ctor(+obj);
    }
    else if(isInTypes(obj, Symbol)) {
        target = Object(obj.valueOf());
    }
    else if(isInTypes(obj, Array)) {
        target = new ctor(obj.length);
        memo.set(obj, target);
        target.splice(0, 0, Array.prototype.map.call(obj, item => baseClone(item, memo)));
    }
    else if(isInTypes(obj, Buffer, ArrayBuffer, SharedArrayBuffer)) {
        console.log(obj);
        target = obj.slice();
    }
    else if(ArrayBuffer.isView(obj)) {
        target = new ctor(obj.buffer.slice(), obj.byteOffset, obj.byteLength);
    }
    else if(isInTypes(obj, Map)) {
        target = new Map();
        memo.set(obj, target);
        obj.forEach((v, k)=> {
            target.set(k, baseClone(v, memo));
        });
    }
    else if(isInTypes(obj, Set)) {
        target = new Set();
        memo.set(obj, target);
        for(const i of obj) {
            target.add(baseClone(i, memo));
        }
    }
    else if(isInTypes(obj, RegExp)) {
        target = new ctor(obj.source, /\w+$/.exec(obj));
        // skip: target.lastIndex = obj.lastIndex;
    }
    else if(isInTypes(obj, WeakMap, WeakSet, Error)) {
        memo.set(obj, target);   
        return {};
    }
    else {
        if( !isInTypes(obj, Function, GeneratorFunction) && (typeof ctor==='function' && !isPrototype(obj))) {
            target = Object.create(Object.getPrototypeOf(obj));
        } else {
            target = {};
        }
    }
    // arguments function and objects
    if(memo.get(obj)===undefined) {
        memo.set(obj, target);
    }
    const props = Object.getOwnPropertyDescriptors(obj);
    const newProps = {};
    for(const k of Object.keys(props)) {
        if(!target.hasOwnProperty(k)) {
            newProps[k] = props[k];
            newProps[k].value = baseClone(props[k].value, memo);
            if(props[k].get) {
                newProps[k].get = baseClone(props[k].get, memo);
            }
            if(props[k].set) {
                newProps[k].set = baseClone(props[k].set, memo);
            }
        }
    }
    Object.defineProperties(target, newProps);
    if(Object.isFrozen(obj)) {
        Object.freeze(target);
    }
    if(Object.isSealed(obj)) {
        Object.seal(target);
    }
    if(!Object.isExtensible(obj)) {
        Object.preventExtensions(target);
    }
    return target;
}
```


## 清除 float

1. 添加新元素，应用 clear: both
2. 父级 div 定义 overflow: auto 或者 hidden（根据 BFC 的规则，计算 BFC 高度时，浮动也参与计算）
3. 使用 :after 伪类

```css
.clear{clear:both; height: 0; line-height: 0; font-size: 0}
```

```css
.outer::after {
    clear: both;
    content: '';
    display: block;
    width: 0;
    height: 0;
    visibility: hidden;
}
```

## 模拟 call

假设为浏览器，非严格模式：

```js
Function.prototype.myCall = function(context, ...args) {
    context = (context??window) || new Object(context);
    const key = Symbol();
    context[key] = this;
    const result = context[key](...args);
    delete context[key];
    return result; 
}
```

非浏览器，则 window 改为 global；严格模式，context 不需要判断和 new Object 转换，而在下文调用时注意判断 null 的情况；

## 模拟 apply

假设为浏览器，非严格模式：

```js
Function.prototype.myApply = function(context, args) {
    context = (context??window) || new Object(context);
    const key = Symbol();
    context[key] = this;
    args = args ? args: [];
    // 应对类数组对象
    if(!(args instanceof Array)){
        const newArgs = [];
        for(let i=0;i<args.length;i++) {
            newArgs.push(args[i]);
        }
        args = newArgs;
    }
    const result = context[key](...args);
    delete context[key];
    return result;
}
```

非浏览器和严格模式见上文

## 模拟 bind

假设为浏览器，非严格模式：

```js
Function.propotype.myBind  = function(context, ...args) {
    const fn = this;
    const bindFn = function(...newFnArgs) {
        return fn.call(
            // 应对被当做构造函数使用
            this instanceof bindFn ? this : context,
            , ...args, ...newFnArgs)
    }
    bindFn.prototype = Object.create(fn.prototype);
    return bindFn;
}
```

## 模拟 new

假设为非严格模式：

```js
const createNew = (Con, ...args) => {
    const obj = {};
    Object.setPrototypeOf(obj, Con.prototype);
    const result = Con.apply(obj, args);
    return result instanceof Object ? result : obj;
}
```

## 模拟 instanceof

```js
const myInstanceOf = (left, right) => {
    let leftValue = Object.getPrototypeOf(left);
    let rightValue = Object.prototype;
    // Object.prototype === null
    while(true) {
        if(leftValue===null) {
            return false;
        }
        if(leftValue===rightValue) {
            return true;
        }
        leftValue = Object.getPrototypeOf(leftValue);
    }
}
```

## 手写 AJAX

```js
function formateData(data) {
    const arr = [];
    for(let key in data) {
        arr.push(`${encodeURIComponent(key)}=${data[key]}`);
    }
    return arr.join('&');
}

function ajax(params) {
    params = params || {};
    params.data = params.data || {};
    params.type = (params.type || 'GET').toUpperCase();
    params.data = formateData(params.data);
    const xhr = new XMLHttpRequest();
    return new Promise((resolve, reject)=>{
        xhr.onload = function() {
            if(xhr.status===200 || xhr===304 || xhr.status===206) {
                const res = JSON.parse(xhr.responseText);
                if(params.success && params.success instanceof Function) {
                    params.success.call(xhr, res);
                }
                resolve(res);
            } else {
                const res = JSON.parse(xhr.responseText);
                if(params.error && params.error instanceof Function) {
                    params.error.call(xhr, res);
                }
                reject(res);
            }
    }
    })
    if(params.type==='GET') {
        xhr.open(params.type, `${params.url}?${params.data}`, true);
        xhr.send();
    }
    else if(params.type==='POST') {
        xhr.open(params.type, params.url, true);
        xhr.setRequestHeader("Content-Type", "application/x-www-form-urlencoded");
        xhr.send(params.data);
    }
}
```

## 手写 JSONP

```js 
function formateData(data) {
    const arr = [];
        for (let key in data) {
        arr.push(encodeURIComponent(key) + '=' + encodeURIComponent(data[key]))
    }
    return arr.join('&');
}

function jsonp(params) {
    params = params || {};
    params.data = params.data || {};
    const callbackName = params.jsonp;
    const head = document.querySelector('head');
    const script = document.createElement('script');
    params.data['callback'] = callbackName;
    const data = formateData(params.data);
    script.src = `${params.url}?${data}`;
    window[callbackName] = (jsonData) => {
        head.removeChild(script);
        clearTimeout(script.timer);
        delete window[callbackName];
        params.success && params.success(jsonData);
    }
    if(params.time) {
        script.timer = setTimeout(()=>{
            window[callbackName] = null;
            header.removeChild(script);
            params.error && params.error({
                message: 'time out'
            });
        }, params.time);
    }
    head.appendChild(script);
}
```

## 手写快排

```js
function partition(arr, l, r) {
    const pivot = arr[l];
    while(l<r) {
        while(arr[r] >= pivot && l < r) {
            r--;
        }
        if(l<r) {
            arr[l] = arr[r];
            l++;
        }
        while(arr[l] <= temp && l < r) {
            l++;
        }
        if(l<r) {
            arr[r] = arr[l];
            r--;
        }
    }
    arr[l] = pivot;
    return l;
}

function quickSort(arr, l, r) {
    if(l<r) {
        const boundary = partition(arr, l, r);
        quickSort(arr, l, boundary);
        quickSort(arr, boundary + 1, r);
    }
}
```

## 让 (a==1 && a==2 && a==3) 为 true

### 利用隐式转换规则

== 操作符在左右数据类型不一致的时候，会进行隐式转换，转换规则如下：

- 如果部署 `[Symbol.toPrimitive]` 接口，那么调用此接口
- 如果非 Date 类型，调用 valueOf，如果返回非基本数据类型，返回调用 toString，如果 toString 返回非基本类型，那么抛出错误
- 如果 hint 为 string，调用顺序为 toString >>> valueOf
- 如果 hint 为 number，调用顺序为 valueOf >>> toString

```js
const obj = {
    [Symbol.toPrimitive]: (function(hint) {
        let i = 0;
        return () => i++;
    })()
}
```

```js
const obj = {
    reg: /\d/g,
    valueOf: function(hint) {
        return this.reg.exec('123')[0];
    }
}
```

### 利用数据劫持

```js
let i = 1;
Object.defineProperty(window, 'a', {
    get: function() {
        return i++;
    }
});
```

```js
const a = new Proxy({}, {
    i: 1,
    get: function() {
        return () => this.i++;
    }
})
```

### 利用数组的 join 方法

```js
const a = [1, 2, 3];
a.join = a.shift;
```

## 防抖函数作用、应用场景、实现

debounce 每当执行调用之后，更新计时，只有到计时结束时，才能触发动作

1. 调节窗口
2. 字符输入
3. 表单验证

```js
function myDebounce(fn, wait, immediate = true) {
    let timer;
    const later = (context, args) => setTimeout(()=>{
        timer = null;
        if(!immediate) {
            fn.apply(context, args);
        }
    }, wait);
    const debounced = function (...params) {
        const context = this;
        const args = params;
        if(!timer) {
            timer = later(context, args);
            if(immediate) {
                fn.apply(context, args);
            }
        } else {
            clearTimeout(timer);
            timer = later(context, args);
        }
    }
    debounced.cancel = function() {
        clearTimeout(timer);
        timer = null;
    };
    return debounced;
}
```

## 节流的作用、应用场景、实现

throttle 规定一个时间，一段时间内只能执行一次：

1. 按钮点击
2. 拖拽事件
3. 计算鼠标的距离

时间戳实现：

```js
function throttle (func, delay) {
    let lastTime = 0;
    const throttled = throttled(...args) {
        const context = this;
        const nowTime = Date.now();
        if(nowTime > lastTime + delay) {
            func.apply(context, args);
            lastTime = nowTime;
        }
    }
    throttled.cancel = function() {};
    return throttled; 
}
```

计时器实现：

```js
function throttle(func, delay) {
    let timeout = null;
    const throttled = function(...args) {
        const context = this;
        if(!timeout) {
            timeout = setTimeout(()=>{
                func.apply(context, args);
                clearTimeout(timeout);
                timeout=null
            }, delay);
        }
    }
    throttled.cancel = function() {
        clearTimeout(timeout);
        timeout = null;
    };
    return throttled;
}
```

## 异步加载 JS 脚本的方法有

### defer/async 属性

```html
<script src="../xxx.js" defer/async></script>
```

- defer: 等到页面在内存中正常渲染结束，其它脚本完成执行，window.onload 之前执行，如果多个 defer，会按照顺序加载
- async：下载完，渲染引擎中断渲染，执行该脚本后渲染，多个 async 不能保证顺序

### preload/prefetch 预加载

```html
<link rel=preload/prefetch href="../xxx" as=media-type type=mime-type>
```

preload 提前获取之后该页面要使用的资源，prefetch 提前获取之后其它页面要使用的资源

### 动态创建 script 标签

```js
let script = document.createElement('script');
script.src = 'XXX.js';
document.body.append(script);
```

### XHR 或者 fetch 异步加载 JS 并使用 eval 执行

```js
let xhr = new XMLHttpRequest();
xhr.open("get", "js/xxx.js",true);
xhr.send();
xhr.onreadystatechange = function() {
    if (xhr.readyState == 4 && xhr.status == 200) {
        eval(xhr.responseText);
    }
}
```

### import() 结合 fetch

## 实现 flattenDeep 函数，将嵌套数组扁平化

### arr.flat

```js
function flattenDeep(arr, deepLength) {
    if(deepLength>Number.MAX_SAFE_INTEGER) {
        deepLength = Number.MAX_SAFE_INTEGER;
    }
    return arr.flat(deepLength);
}
```

### reduce 和 concat

```js
function flattenDeep(arr) {
    return arr.reduce((acc, val)=> Array.isArray(val) ? arr.concat(flattenDeep(val)): acc.concat(val), []);
}
```

### 利用 stack 实现


## Promise.all 用法和实现

Promise.all(PromiseArray).then() 等待全部任务完成后执行，或者出现第一个失败

```js
function myPromiseAll(arr) {
    return new Promise((resolve, reject)=>{
        const result = new Array(arr.length);
        arr.forEach((p, index)=>{
            Promise.resolve(p).then(d=>{
                result[index] = d;
                count ++;
                if(count===arr.length) {
                    resolve(result);
                }
            }, err=> reject(err));
        });
    });
}
```

## 实现 `JSON.stringify`

```js
function jsonStringify(data, set) {
    const dataType = typeof data;
    if (dataType!=='object') {
        if(Number.isNaN(data) || data===Infinity || data===-Infinity) {
            return "null";
        } else if(dataType==="function" || dataType==="symbol" || dataType==="undefined") {
            return "undefined";
        } else if(dataType==="string") {
            return `"${data}"`;
        } else if(data.toString && typeof data.toString === "function") {
            // such as bigint
            return data.toString();
        } else {
            // unreachable in es10
            return "undefined";
        }
    } else {
        if(data===null) {
            return "null";
        } else if(data.toJson && typeof data.toJson === "function") {
            return jsonStringify(data.toJSON());
        } else if(data instanceof Array) {
            const result = [];
            data.forEach((item)=>{
                const itemType = typeof item;
                if(itemType === "undefined" || itemType==="function" || itemType === "symbol") {
                    result.push("null");
                } else {
                    result.push(jsonStringify(item));
                }
            });            
            result = `[${result}]`;
            return result.replace(/'/g, '"');
        } else {
            if(set) {
                set = new Set();
            }
            if(set.get(data)!==null) {
                return "undefined";
            }
            set.add(data);
            const result = [];
            Object.keys(data).forEach((item)=>{
                if(typeof item!=='symbol') {
                    if (data[item] !== undefined && typeof data[item] !== 'function'
                        && typeof data[item] !== 'symbol') {
                        result.push('"' + item + '"' + ":" + jsonStringify(data[item], new Set(set)));
                    }
                }
            });
            return ("{" + result + "}").replace(/'/g, '"');
        }
    }
}
```

## 实现 JSON.parse

```js
(new Function('return'+json))();
```

基于 eval，必要时需要做过滤，避免 XSS

## 实现 Observable 和 Subject

### Observable:

```js
function myObservable(observer) {
    const datasource = new DataSource();
    datasource.ondata(e) => observer.next(e);
    datasource.onerror(err) => observer.error(err);
    datasource.oncomplete() => observer.complete();
    return () => {
        datasource.destroy();
    };
}
```

观察者约定：

1. 不在 complete 或者 error 后调用 next
2. 取消订阅后，不能使用任何方法
3. 调用 complete 和 error 需要取消订阅逻辑
4. 如果 next、complete 或者 error 处理方法抛出异常，要调用取消订阅逻辑，以确保不会泄露资源
5. next、error 和 complete 实际上都是可选的。你无需处理每个值、错误或完成。你可能只是想要处理其中一二

### Subjects 实现

```js
class MySubject extends Rx.Observable {
 
 constructor() {
   super();
   this.observers = [];
 }
 
 subscribe(observer) {
   this.observers.push(observer);
 }
 
 next(value) {
   this.observers.forEach(observer => observer.next(value));
 }
 
 error(error) {
   this.observers.forEach(observer => observer.error(error));
 }
 
 complete() {
   this.observers.forEach(observer => observer.complete());
 }
 
}
```

## 用 CSS 写无限循环动画

```css
@keyframes example {
    from { background: red; }
    to { background: blue; }
}

#example1 {
    width: 10px;
    height: 10px;
    animation-timing-function: ease-in;
    animation-name: example;
    animation-duration: 1s;
    animation-iteration-count: infinite;
}
```

## Promise.race 及其实现

只要有一个参数 fullfilled 或者 reject，则 fullfilled 或者 rejected

实现：

```js
function myRace(iterable) {
    const arr = Array.from(iterable);
    return new Promise((resolve, reject)=> {
        if(arr.length===0) {
            return resolve();
        }
        arr.forEach(p=> Promise.resolve(p).then((d)=>resolve(d), err=>reject(err)));
    });
}
```

## 数组去重

```js
function unique(array) {
    return [...new Set(array)];
}
```

其它方法时间复杂度较高

## 编写通用 currying

```js
function currying(fn, ...args) {
    return (...others) => fn(...args, ...others);
}
```

## 大文件上传实现？

基本思路：文件分块，每块计算 MD5，块向后端传输，后端返回 MD5，验证符合后表明该块传递成功，否则失败；整体传输完毕后，计算整体的 MD5，成功则成功，否则失败。

断点续传：记录成功传递的块的 MD5，重新建立连接时，验证这些 MD5。验证失败的和未验证的重传。

安全问题：避免过多上传导致服务器崩溃，可设置缓存队列、大小检查和超时时间，还可以由服务器给客户端发送 token 记录，客户端上交 token 记录。

并发：多个异步任务即可，完成后修改计数。

# HTML & CSS & 样式

## 伪类和伪元素

- 伪类用于当已有元素处于某个状态时，为其添加对应的样式
- 伪元素用于创建一些不在文档树中的元素

## 什么情况下css会使用gpu加速

DOM 树和 CSS 结合后形成浏览器构建页面的渲染树。渲染树中包含了大量的渲染元素，每一个渲染元素会被分到一个图层中，每个图层又会被加载到 GPU 形成渲染纹理，图层在 GPU 中的 transform 是不会触发 repaint 

- video 和 canvas 元素
- CSS filter（滤镜：修改饱和度和模糊等）
- opacity
- 元素覆盖，使用 z-index 属性

## rpx是什么

微信小程序的单位 1rpx = 屏幕宽度 / 750

## 什么是 sass 和 less 、postcss

都是 CSS 预处理器

- sass 最早和最成熟的预处理器，其后续 scss 完全兼容 css，较强大
- less 后期之秀，可编程性较弱但较为简单，兼容 css
- postcss 最强大，提供使用 js 处理 css 的方式，它提供了 css 解析成抽象语法树的解析器

## React 中使用 CSS

styled-components，发挥 js 的强大性

## shadow dom 是什么

是浏览器的一种能力，允许浏览器在渲染文档的时候向 DOM 结构中插入一颗 DOM 元素子树，它不在主 DOM 树中。目的主要是屏蔽组件内部的复杂性。

其结构为：

- document
- shadow host 宿主元素
- shadow root 通过 createShadowRoot 返回的文档片段
- contents 各子组件 dom 的具体实现

可以使用伪元素控制 shadow-dom 样式，或者自己创建 shadow-dom 元素

## canvas图层怎么用

原生没有图层，替代方案：

- 多个重叠图层，设置渲染覆盖方案
- WebGL 操作

## dom 渲染的性能损耗在哪里

重排和重绘

## 使用 CSS 让一个元素水平垂直居中

### 利用 flex 布局

```css
.container {
    display: flex;
    aligh-items: center;
    justify-content: center;
}
```

### 子元素是单行文本

```css
.container {
    height: 100px;
    line-height: 100px;
    text-align: center;
}
```

### 利用 absolute + transform

```css
.container {
    position: relative;
}

.box {
    top: 50%;
    left: 50%;
    transform: translate(50%, 50%);
}
```

### 利用 grid 布局

```css
.container {
    display: grid;
}

.box {
    justify-self: center;
    align-self: center;
}
```

### 利用绝对定位和 margin: auto

```css
.container {
    position: relative;
}
.box {
    position: absolute;
    left: 0;
    right: 0;
    bottom: 0;
    margin: auto;
}
```

## 如何隐藏页面中元素

- display: none 不占据空间的完全隐藏
- hidden 属性，CSS3 新增，不占据空间，完全隐藏
- position：absolute/fixed + left/top: -9999999px，向左、上移动，不占空间
- position: relative + left/top: -9999999px，占据空间，若要不占空间，可以设置 height: 0
- margin-left: -999999px; 占据空间
- transform 
    - scale(0) 不占空间
    - translateX/Y(-9999999px) 占据空间，可设置 height
    - rotateX/Y(90deg) 占据空间
- height: 0, width: 0, line-height: 0, font-size: 0, overflow: hidden 不占空间
- opacity: 0 占据空间
- visibility: hidden 占据空间
- z-index: -999 + position: relative 占据空间
- clip-path: polygon(0 0, 0 0, 0 0, 0 0); 占据空间
- aria-hidden 属性，语义上隐藏，占空间可见

## HTML5 语义化的理解

优点：

- 代码结构清晰，易于理解、开发和维护
- 利于屏幕阅读器和爬虫等其他类型客户代理阅读
- 有益于 SEO

标签：

- title
- header
- nav
- main
- section
- article
- h1-h6
- ul/ol/li
- table/thead/tbody/tfoot/...
- aside
- figure
- picture
- details
- mark
- dialog
- address
- img/audio/video/canvas/picture/source

## 如何理解 BFC？BFC 的布局规则为？如何创建 BFC？

### 如何理解 BFC

盒子是 CSS 引擎根据文档中内容所创建的，不同类型的盒子在文档上占据不同类型的上下文。格式化上下文是页面的一块渲染区域及其渲染规则，包括它子元素的定位以及其自身与其它盒子的关系。一个块级盒子一定会参与格式化上下文的创建。

### BFC 的布局规则为

- 内部，盒子依次垂直排列
- 两个盒子的距离由 margin 决定，上下相邻的 margin 会发生重叠（其实内部接触也会重叠）
- BFC 内每个盒子的左 margin-left-edge 接触外部盒子的 content-left-edge，即使在左浮动情况下也是如此
- BFC 内的行上下文会自动伸缩以适应两侧浮动
- BFC 内计算好后，不会影响外部元素
- 计算 BFC 高度时，浮动元素也会计算

### BFC

- 根元素
- 浮动元素
- position: absolute 或者 fixed
- display: block, inline block, table-cell, table-caption, inline table, display: flow-root, contain: layout/content/paint 的元素，弹性元素、网格元素、多列元素, overflow: visible
- 匿名块盒子

## 如何响应式布局

- 媒体查询
    - 设置 viewport
    - 选择屏幕大小分割点
    - 针对不同的范围和类型的设备，复用部分资源，设置不同的网页
- 百分比布局
    - 计算困难
    - 复杂：position 类型，元素类型和属性类型都可能造成负面影响，有副作用
- rem 布局
    - 思想：
        1. 一般不给元素设置具体的宽度
        2. 高度可以按照设计图固定值设计
        3. 所有设置固定的时候用 rem 做单位
        4. JS 设置真实屏幕的宽度，除以设计稿的宽度，作出比例
    - 缺点：
        1. 必须通过 JS 来动态控制根 font-size 大小，存在 JS 和 CSS 的耦合性
- 视口单位：
    - 思想：使用 vm 和 vh，vmin 和 vmax 来布局
- 图片响应式
    - srcset/src
    - background-image
    - picture
- 成型方案：
    - flex
    - grid
    - columns

## 低版本 IE 的盒模型和 W3C 的盒模型有什么区别？

两种盒模型：

1. IE 盒模型 border-box
2. W3C 盒模型 content-box

IE 老版本声明 DOCTYPE 会使得变为 W3C

## CSS 选择符有哪些？

- id 选择器 #id
- 类选择器 .classname
- 标签选择器 li
- 后代选择器 li a
- 子选择器 li>a
- 兄弟选择器 li~a
- 相邻兄弟选择器 li+a
- 属性选择器 a[rel="xxx"][disabled]
- 伪类选择器 a:hover, li:nth-child(2)
- 伪元素选择器 ::before ::after
- 通配符选择器 *

## ::before 和 :after 中的双冒号和单冒号有什么区别？

单冒号用于 CSS3 伪类，双冒号用于 CSS 伪元素；浏览器为兼容性考虑，仍然坚持就得伪元素的单冒号写法。

## 哪些 CSS 属性可以继承？

每一个属性的定义中都给出了该属性可否定义，一个没有继承性的属性会在没有指定值的时候，选择继承值作为自己的值。当元素不是继承属性的时候，可以设置 inherit 来使得进行继承。

1. 字体系列属性
2. 文本系列属性
3. 表格布局属性
4. 列表选择
5. 光标属性
6. 元素可见性
7. 元素存在性

## CSS 优先级算法如何计算？

确定是否使用 !important 如果只有一个有，使用它；否则进入优先级规则。

一条匹配规则一般由多个选择器组成，一条规则的特殊性由组成它的选择器的特殊性累加而成。内联样式 1000，id 选择器 100，类选择器、伪类选择器 10，元素选择器、伪元素选择器 1。值无法进位；

相同值则按前后顺序计算。

## LVHA 伪类

1. link 未访问过
2. visited 访问过
3. active 活跃状态
4. hover 鼠标悬停

可以改变 LVHA 的活动顺序保证在超链接访问过后，hover 仍然出现

## CSS3 新增伪类？

- nth-child(n) 选中父元素下的第 n 个子元素，并且该子元素标签名为 elem，n 可以是数值也可以是函数
- nth-last-child(n) 作用同上，反向查找
- last-child 最后一个子元素
- only-child 是父元素的唯一一个子元素
- nth-of-type(n) 父元素下第一个元素为 elem 的类型元素
- first-of-type 父元素下第一个类型为 elem 类型元素
- last-of-type
- only-of-type
- target 选择当前活动的 elem 元素（使用 a href="#xxx" 进行跳转时）
- not(elem) 选择非 elem 元素的每个元素
- enabled 控制表单控件的禁用状态

## postition 的定位原点

- absolute：生成绝对定位的元素，相对于值不为 static 的第一个父元素的 paddingbox 左上角进行定位
- fixed：相对于视口左上角
- relative：相对于 static 位置
- static：默认值，元素正常出现在流中
- inherit：规定从父元素继承 position 属性的值

## CSS3 有哪些新特性

- 新增各种选择器
- 圆角
- 多列布局
- 反射和阴影
- 文字特效
- 文字渲染
- 线性渐变
- 图形学变换
- 透视和 3D 变换

## 请简要解释 CSS3 的 flexbox 弹性盒布局模型和适用场景？

flex 是 CSS3 新增的一种布局方式，可以通过将一个元素的 display 属性设置为 flex 将其设置为一个 flex 容器，所有的子元素都变成其项目。

一个容器默认有两条轴，以 flex-direction 作为主轴，其垂直方向作为交叉轴。可以使用 justify-content 来指定元素在主轴上的排列方式，使用 align-items 来指定在交叉轴的排列方式，align-content 指定在多个交叉轴的排列方式，flex-wrap 设定当一行排列不下时的换行方式。

对于容器中的项目，可以使用 order 来指定项目的排列顺序，还可以使用 flex-grow 来指定排列项目的放大比例。可以使用 flex-shrink 来指定当排列空间不足时，项目的缩小比例

## 如何用纯 CSS 创建一个三角形

### 传统方法

利用 CSS 的绘制原理，取消三条边

```css
#demo {
    width: 0;
    height: 0;
    border-with: 20px;
    border-style: solid;
    border-color: transparent transparent red transparent;
}
```

### clip-path

```css
#demo {
    height: 30px;
    width: 30px;
    background: blue;
    clip-path: polygon(50% 0, 0 100%, 100% 100%);
}
```

## 绘制椭圆

```css
#demo {
    height: 30px;
    width: 30px;
    background: blue;
    clip-path: ellipse(10% 30% at 50% 50%);
}
```

## CSS 多列等高

1. flex align-items 默认为 stretch，如果项目未设置高度或者设置 auto，将占满整个父容器的全部高度
2. 利用 table-cell
3. 利用 grid 的 align-items
4. 多列布局

## 经常遇到的浏览器兼容性问题有哪些？

- 早期 IE 版本不支持 ES6，包括 promise、observableMutation、proxy、defineProperty 等
- 早期 IE 盒模型为 border-box，但是该问题只要使用 DOCTYPE 就会自动切换成 W3C 盒模型比较好解决
- 不支持的 CSS 属性，加浏览器前缀
- 移动浏览器点击的 300ms 延时
- ……

## 为什么要初始化 CSS 样式

由于浏览器兼容性问题，不同的浏览器对不同元素和属性的默认值不同，如果没有初始化，会造成表现不同。

## 什么是包含块？

包含块是元素用来计算和定位的框。即如何确定自身定位的框。详细见 css 复习笔记。

## visibility 的 collapse 属性？

1. 对于一般的元素，表现和 visibility: hidden 相同。不可见但是仍然占据资源
2. 对于表格元素，占用的空间会释放

## 使用图片 base64 编码的优点和缺点

图片处理格式，可以将图片转换为字符串

优点：

1. 减少图片的 HTTP 请求

缺点：

1. 大小较大
2. 无法直接缓存
3. 更改图片必须修改 html

## margin 重叠问题的理解

1. 相邻兄弟元素的 margin-top 和 margin-bottom，设置 BFC
2. 父元素和子元素的上外边界重叠，设置父元素的 padding 或者 border、为父元素设置 BFC
3. 无高度的父元素和子元素的下边界重叠，如上操作或者设置高度
4. 无内容元素自身上、下边界重叠

## CSS 优化、提高性能的方法？

加载性能：

- CSS 压缩
- CSS 单一样式
- 减少 @import 多使用 link

选择器性能：

- 关键选择器
- 减少通配符

渲染性能：

- 慎重使用高性能属性：浮动、定位
- 减少页面重排和重绘
- 去除空规则
- 属性值为 0，不加单位

## 浏览器怎样解析 CSS 选择器

从右到左确定选择器，从关键选择器开始

## 简单说一下 CSS3 的 all 属性？

所有的属性都如何，支持 initial、inherit 和 unset

## 为什么不建议使用通配符初始化 CSS 样式？

性能损失严重，某些特性无法初始化，如 chrome 中的搜索组件默认是圆角的

## absolute 的 containingblock 的计算方式和正常流的不同？

1. 内联元素可以作为包含块所在元素
2. 包含块是最近非 static 父元素
3. 边界是 paddingbox 而不是 contentbox

## 元素竖向百分比设定是相对于容器的高度吗？

- height 相对于包含块的高度
- padding 和 margin 相对于包含块的宽度


## 全屏滚动的原理？

```css
overflow: hidden;
transition: all 1000ms ease;
```

## 视差滚动效果，如何给每页做不同的动画？

视差滚动是指多层背景以不同的速度移动，形成立体的运动效果，带来非常出色的视觉体验

## 如何修改 chrome 记住密码后自动填充表单的黄色背景？

修改 chrome 的 input:-webkit-autofill 私有属性即可

## 怎么让 chrome 支持小于 12px 的文字

使用 -webkit-text-size-adjust:none 私有属性解决；

或者使用 transform 缩放，注意整个元素会一起缩放

## 怎样让某些层不可拖动？（electron）

1. z-index
2. -webkit-app-region: drag

## 让页面字体变得清晰，变细用 CSS 怎么做？

font-smoothing 用于字体抗锯齿

## font-style 的 italic 和 oblique 的区别？

前者使用斜体字体形状，后台使用切变算法将字体变斜

## 设备像素、CSS 像素、设备独立像素、dpr、ppi 之间的区别

- 设备像素指物理尺寸
- CSS 像素等同于独立像素，取决于页面缩放程度和 DPR 的大小
- dpr 指的是设备独立像素和设备像素的比值（反比）
- ppi 指的是每英寸的物理像素的密度

## layoutviewport、visualviewport 和 idealviewport 的区别？

- layoutviewport 是实际的文档大小，因为可能将页面设置的比视口大
- visualviewport 是设备视口的大小（桌面设备为浏览器窗口内边距）
- idealviewport 指的是最适合移动设备的 viewport，能够使得不出现横向滚动条也不需要缩放

## position: fixed 的在 android 下无效

在移动端默认浏览器的 viewport 是 layoutviewport，因此 fixed 是相对该值移动。

```html
<metaname="viewport"content="width=device-width,initial-scale=1.0,maximum-scale=1.0,minimum-sca
le=1.0,user-scalable=no"/>
```

可以如上设置，将视口宽度改变为 idealviewport

## 如果需要手写动画，最小间隔为多少？

对于 60hz 屏幕为 1/60s = 16.7 ms

## 如何去除 inline-block 元素的元素间间距？

移除空格、使用 margin 负值、使用 font-size: 0、letter-space、word-spacing

## overflow: scroll 不能平滑滚动？

可以开启安卓的硬件加速。原理是开启一个独立的渲染层，由于只发生 transform: translate() 所以可以触发硬件加速

```css
-webkit-overflow-scrolling:touch;
```

## 有一个高度自适应的 div，里面有两个 div，一个高度 100px，希望另一个填满剩下的高度？

1. display: flex; flex-direction: column; flex-grow: 1;
2. 容器使用 relative 内部使用 position: absolute; top: 100px; bottom: 0; left: 0; right: 0;

## style 标签卸载 body 前后

页面加载自下而上先加载样式，写在前面使得首次加载即运用样式加载。卸载后面可能导致浏览器停止之前的加载触发重绘

## 什么是 CSS 预处理、后处理器？

CSS 预处理定义了一种新的语言，基本思想是，用一门专门的编程语言，为 CSS 增加一些编程特性。预处理器包括：LESS、Sass、Stylus，增强 css 代码的复用性和编程能力。

CSS 后处理是对 CSS 进行处理，并最终生成 CSS 的预处理器。广义也是 CSS 预处理器。比如 PostCSS，能够在完成的样式表中根据 CSS 规范处理 CSS，让其更加有效。比如为浏览器添加私有前缀。

## 阐述一下 CSSSprites

将一个页面的所有图片包含到一张大图，利用 background-image 进行定位

优点：

- 减少 HTTP 请求数，加快页面加载速度
- 增加信息重复度，提升压缩比
- 更换风格方便

缺点：

- 图片合并麻烦
- 维护不便
- 不能进行渐进加载，浪费流量

## rem 布局的优缺点？

优点：

- 便于移动端适配
- 便于将 rem 和屏幕分辨率关联起来，实现整体缩放

缺点：

- 对于奇葩 dpr 的设备兼容性不好
- iframe 引用会出现问题
- 对于桌面端，无法展示更多的内容

## 画一条 0.5px 的线

- canvas，并设置次像素渲染模式
- metaviewport
- transform: scale()
- border-image

## transtion 和 animation 的区别

- transition 关注 CSS 属性的变化
- animation 作用于元素本身，可以使用关键帧

## 什么是首选最小宽度？

元素适合的最小宽度？东亚文字最小宽度为每个汉字的宽度；西方文字最小宽度为特定连续的英文字符的宽度。可以使用 word-break 来改变最小宽度。

## 为什么 height: 100% 无效？

对于普通文档流中的元素，百分比值想要起作用，父级必须有可以生效的高度值。如果包含块的高度没有显式指定，且元素非绝对定位，则计算值为高度，无法参与计算。

## min-width/max-width 和 min-height/max-height 之间的覆盖规则

- min-width 会覆盖 width，即使 width 设置了 !important
- min-width 会覆盖 max-width，此规则会发生在 min-width 和 max-width 失效的时候

## 什么是幽灵空白节点？

在 HTML5 文档声明中，内联元素的所有解析和渲染就表现的如同每个行框盒子前面有一个空白节点一样。这个空白节点永远透明，不占据任何宽度，也无法看见，又确确实实的存在。

## 什么是替换元素？

通过修改某个属性值呈现的内容就可以被替换的元素称为替换元素。其特性为：

1. 内容的外观不受页面上的 CSS 的影响
2. 有自己的尺寸
3. 在某些 CSS 属性上有一套自己的规则
4. 所有的替换元素都是内联水平元素

## 替换元素的计算规则

三种尺寸：

- 固有尺寸：替换内容原本的尺寸
- HTML 尺寸：只能通过 HTML 原生属性改变，一遍能够表示渲染的像素
- CSS 尺寸：特指可以通过 CSS 的 width 和 height 或者其它因素改变的尺寸，对应盒尺寸中的 contentbox

计算规则：

1. CSS 尺寸最高
2. HTML 尺寸其次
3. 固有尺寸最低
4. 如果固有尺寸含有固定宽高比例，且仅仅设置单方向尺寸，则使用宽高比等比例缩放
5. 如果都不符合，使用 300 * 150 像素表现

## content 与替换元素的关系？

content 属性生成的对象是匿名替换元素

- 该文本无法选中，无法复制
- 无法被阅读器读取，无法被 SEO（除非使用 aria-* ）等特殊方法
- 不能使用 :empty 伪类
- 动态生成值无法获取

## margin: auto 的填充规则

1. 如果一侧定值，一侧 auto，则 auto 为剩余空间大小
2. 如果两侧均为 auto，则平分剩余空间

## margin 无效的情景

1. display: inline 的元素垂直 margin 无效
2. `<tr>` 和 `<td>` 元素无效
3. 绝对定位元素非定位方位的 margin 无效
4. 定高容器的子元素的 margin-bottom 或者宽度定死的子元素 margin-right 的定位无效

## 什么是基线和 x-height

字母 x 的下边缘为基线，x-height 指的是消息字母 x 的高度，是字符纵向对齐的三等分线的等分单位；middle 指的是 baseline 向上 1/2x-height 的高度。ex 即为 x-height

## line-height 的特殊性

1. 对于非替换的纯内联元素，其可视高度由 line-height 决定。
2. 内联元素的高度由固定部分和不固定高度组成，不固定指的是 line-height。该行距高度分布在文字的上方和下方。
3. 行距等于 line-height - font-height
4. border 和 line-height 等传统 CSS 属性无小数概念
5. 对于纯文本元素，line-height 决定最终高度。如果同时有替换元素，则 line-height 决定最小高度
6. 对于块状元素，line-height 没有任何作用
7. 其默认值为 normal，百分比时是于 font-size 相乘后的值
8. 如果数值作为 line-height 的值，则子元素会继承
9. 无论内联元素 line-height 如何设置，最终父级元素的高度都由数值大的那个 line-height 决定

## vertical-align 的特殊性

1. 默认值为 baseline，文字对齐 x 下边缘，内联元素对齐下边缘。
2. top 上边缘
3. middle 中间
4. 支持数值属性，是相对于 baseline 向上或者向下偏移
5. 百分比相对于 line-height 计算得到
6. 只能应用于内联元素以及 display 为 table-cell 的元素
7. table-cell 元素设置 vertical-align 对齐的是子元素，但作用的是 table-cell 本身

## overflow 的特殊性

1. 设置了 overflow:hidden 的元素，假设同时存在 border 和 padding，则当子元素内容高度限制的时候，裁减的边界是 border-box 的内边缘，而非 paddingbox 的内边缘
2. HTML 的根元素和文本框默认可以产生滚动条
3. 滚动条会占用容器的可用宽度或者高度
4. 元素设置了 overflow:hidden 声明，当内容高度溢出的时候，滚动依然存在，只是滚动条不存在

## 无依赖绝对定位是什么？

没有设置left/top/right/bottom属性值的绝对定位称为“无依赖绝对定位”。

## absolute 和 overflow 的关系？

1. 如果overflow不是定位元素，同时绝对定位元素和overflow容器之间也没有定位元素，则overflow无法对absolute
元素进行剪裁。
2. 如果overflow的属性值不是hidden而是auto或者scroll，即使绝对定位元素高宽比overflow元素高宽还要大，也都不会出现滚动条。
3. overflow元素自身transform的时候，Chrome和Opera浏览器下的overflow剪裁是无效的。

## clip 裁减是什么？

最佳可访问性隐藏，内容肉眼不可见，但是其他辅助设备却能够进行识别和访问

## relative 的特殊性？

1. 相对定位元素的 left/top/right/bottom 百分比是相对于包含块计算的，而不是自身
2. top 和 bottom 这两个垂直方向值时是相对高度计算的
3. 当相对定位元素同时应用对立方向定位值的时候，也就是top/bottom和left/right同时使用的时候，只有一个方向的定位属性会起作用。而谁起作用则是与文档流的顺序有关的，默认的文档流是自上而下、从左往右，因此top/bottom同时使用的时候，bottom失效；left/right同时使用的时候，right失效。

## 层叠相关？

见 CSS 复习笔记

## font-weight 的特殊性？

具体数值必须是 100-900 之间的整百数

## text-indent 的特殊性

1. text-indent 仅对第一行内联盒子内容有效
2. 非替换元素以外的 display 计算值为 inline 的内联元素设置 text-indent 值无效
3. `<input>` 标签按钮text-indent值无效。
4. `<button>` 标签按钮text-indent值有效。
5. text-indent 的百分比值是相对于当前元素的“包含块”计算的，而不是当前元素。

## letter-spacing 与字符间距

letter-spacing可以用来控制字符之间的间距，这里说的“字符”包括英文字母、汉字以及空格等。

letter-spacing具有以下一些特性。

1. 继承性。
2. 默认值是normal而不是0。虽然说正常情况下，normal的计算值就是0，但两者还是有差别的，在有些场景下，letter-spacing会调整normal的计算值以实现更好的版面布局。
3. 支持负值，且值足够大的时候，会让字符形成重叠，甚至反向排列。
4. 和text-indent属性一样，无论值多大或多小，第一行一定会保留至少一个字符。
5. 支持小数值，即使0.1px也是支持的。
6. 暂不支持百分比值。

## word-spacing 与单词间距

letter-spacing作用于所有字符，但word-spacing仅作用于空格字符。换句话说，word-spacing的作用就是增加空格的间隙宽度。

## white-space 与换行和空格的控制？

white-space属性声明了如何处理元素内的空白字符，这类空白字符包括Space（空格）键、Enter（回车）键、Tab（制表符）键产生的空白。因此，white-space可以决定图文内容是否在一行显示（回车空格是否生效），是否显示大段连续空白（空格是否生效）等。

其属性值包括下面这些。

- normal：合并空白字符和换行符。
- pre：空白字符不合并，并且内容只在有换行符的地方换行。
- nowrap：该值和normal一样会合并空白字符，但不允许文本环绕。
- pre-wrap：空白字符不合并，并且内容只在有换行符的地方换行，同时允许文本环绕。
- pre-line：合并空白字符，但只在有换行符的地方换行，允许文本环绕。

## 隐藏元素的 background-image 加不加载？

1. img 元素一定会加载
2. 父元素隐藏导致的隐藏不加载，自身隐藏导致的隐藏加载

## 如何实现单行、多行文本的溢出省略

单行文本

```css
text-overflow: ellipsis;
white-space: nowrap;
overflow: hidden;
```

多行文本溢出

```css
p { 
    overflow: hidden;
    line-height: 1.5em;
    height: 3em;
}
p: after {
    content: "...",
    position: absolute;
    bottom: 0;
    left: 0;
    background-color: #fff;
}
```

## 常见的元素隐藏方式

1. display: none 渲染树不渲染，不响应绑定的监听事件
2. visibility: hidden 占据空间，不响应绑定的监听事件
3. opacity: 0 占据空间，响应监听事件
4. 绝对定位移除实现隐藏
5. z-index 负值隐藏
6. clip-path 隐藏，不响应监听事件
7. transform: scale(0,0) 实现缩放，不响应绑定的监听事件

## 什么是 CSSOM？

CSS Object Model 是一组允许用JavaScript操纵CSS的API。 它是继DOM和HTML API之后，又一个操纵CSS的接口，从而能够动态地读取和修改CSS样式