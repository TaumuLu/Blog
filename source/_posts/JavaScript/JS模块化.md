---
title: JS模块化
date: 2017-03-17 19:51:06
tags: JavaScript
---

## 模块化
- 模块化解决 
    1. 命名冲突
    2. 文件依赖
- 模块化设计 
    1. 信息隐藏
    2. 内聚度
    3. 耦合度

## Js模块化
- 自执行匿名函数
- 多级命名空间
- CommonJS
- AMD
- CMD
- ES6

## 模块化实现
** Modules/Wrappings规范 ** 
> 定义模块用module变量，它有一个方法declare
> declare接受一个函数类型的参数，如称为factory
> factory有三个参数分别为require、exports、module
> factory使用返回值和exports导出API
> factory如果是对象类型，则将该对象作为模块输出

### CommonJS

#### 定义
- 引用模块(require)
- 定义模块(exports)
- 模块标识(module)

#### 加载方式
- CommonJS为服务器端JS的表现来制定的规范，同步模块
- CommonJS模块间相互依赖需同步加载，限制了客户端的实现

### AMD

#### 定义
- 定义模块 define(id?, dependencies?, factory)
    + id：可选参数，用来定义模块的标识，如果没有提供该参数，脚本文件名
    + dependencies：一个当前模块依赖的模块名称数组
        * 如果dependencies省略不写，则默认为["require", "exports", "module"]，factory中也会默认传入require,exports,module
        * 在factory中传入形参需与依赖模块一一对应
        * 如果factory为函数，模块对外暴漏API的方法有三种：return任意类型的数据、exports.xxx=xxx、module.exports=xxx
        * 如果factory为对象，则该对象即为模块的返回值
    + factory：工厂方法，模块初始化要执行的函数或对象。如果为函数，它应该只被执行一次。如果是对象，此对象应该为模块的输出值
- 加载模块 require([dependencies], callback)
    + dependencies：一个要加载的模块名称数组
    + callback：加载成功之后的回调函数
        
#### 加载方式
- AMD为客户端JS的表现来制定规范，异步模块，通过回调函数实现

#### 缺点
- 会预先下载并执行所需要的模块
- 在定义模块的时候，要把所有依赖模块都罗列一遍，而且还要在factory中作为形参传进去，要写两遍很大一串模块名称
- 懒加载的话必须得实时下载代码然后在回调中才能执行

> 好的一点是，AMD保留了commonjs中的require、exprots、module这三个功能
> 也可以不把依赖罗列在dependencies数组中，而是在代码中用require来引入

#### 后续修改
> 部分兼容Modules/Wrappings的写法，dependencies数组为空
> 但是factory函数的形参必须手工写上require,exports,module，（这不同于之前的dependencies和factory形参全不写）

#### require.js
- require.config
```js
require.config({
    baseUrl: '指定基目录',
    paths: '指定个模块加载路径',
    
    // 加载非规范的模块
    shim: {
        deps: '模块的依赖',
        exports: '输出的变量名',  
    } 
})

```

- require.js自身插件
    - domready 让回调函数在页面DOM结构加载完成后再运行
    - text 加载文本
    - image 加载图片

### CMD

#### 定义
- 定义模块 define(factory)
    + factory：需手动传入形参require,exports,module
- 加载模块 
    + require(id)
        * 同步执行，预先下载
        * id: 模块名称
    + require.async(id, callback?)
        * 异步执行，执行时下载
        * id: 模块名称
        * 可选，异步加载后的回调函数

#### API
- define.cmd
> 一个空对象，可用来判定当前页面是否有 CMD 模块加载器

- require.resolve(id)
> 使用模块系统内部的路径解析机制来解析并返回模块路径。该函数不会加载模块，只返回解析后的绝对路径
> 这可以用来获取模块路径，一般用在插件环境或需动态拼接模块路径的场景下

- seajs.use(id/[], callback?)

### ES6

#### 定义
- 引用模块(import xxx from 'xxx')
- 导出模块(export)
    
#### 加载方式
- 静态加载，编译时就能确定模块的依赖关系，以及输入和输出的变量

#### API
- import
> import后面的from指定模块文件的位置，可以是相对路径，也可以是绝对路径
> import命令具有提升效果，会提升到整个模块的头部，首先执行
> import是静态执行，所以不能使用表达式和变量，这些只有在运行时才能得到结果的语法结构
> import语句会执行所加载的模块，可以直接import 模块名使用
> 多次重复执行同一句import语句，只会执行一次，而不会执行多次

- export
> export命令规定的是对外的接口，不能只是一个值
> export语句输出的接口，与其对应的值是动态绑定关系，即通过该接口，可以取到模块内部实时的值
> export命令可以出现在模块的任何位置，只要处于模块顶层就可以
    
- default export 
> export default命令，为模块指定默认输出
> import 命令可以指定任意名字
> import命令后面，不使用大括号
> export default命令其实只是输出一个叫做default的变量，所以它后面不能跟变量声明语句
> export default 本质是将该命令后面的值，赋给default 变量以后再默认，所以可以直接将一个值写在export default之后

- as
> 用as 语句后的值重命名as 前的值
    
- *
> 指定一个对象，所有输出值都加载在* 这个对象上面
    
- import()
> 动态加载函数
> import()返回一个Promise 对象
> import()类似于 Node 的require方法，区别主要是前者是异步加载，后者是同步加载

- export 与import 的复合写法
> export和import语句可以结合在一起，写成一行
> 模块的接口改名和整体输出，也可以采用这种写法

- export 和default export 的区别
> export时，对应的import语句需要使用大括号
> export default时，对应的import语句不需要使用大括号
> export default命令用于指定模块的默认输出
> 一个模块只能有一个默认输出，因此export default 命令只能使用一次
> 所以import 命令后面才不用加大括号，因为只可能对应一个方法
> 本质上，export default 就是输出一个叫做default 的变量或方法，然后系统允许你为它取任意名字

- 其他
静态加载可以做静态优化
ES6 模块之中，顶层的this指向undefined，即不应该在顶层代码使用this

#### import 声明
尽管import声明不符合解构的规则，但其表现十分类似，类似于解构但是不是解构赋值
当你使用import书写模块声明时，你可以引入模块API中的任意模块

import声明有其不一样的语法，与解构相比较，以下的import语句均不起作用

- 使用默认值，import {series = noop} from 'contra'
- 嵌套解构风格，import {map: { series }} from 'contra'
- 别名语法，import {map: mapAsync} from 'contra'

这些限制的主要原因在于import声明有一个绑定，不是一个引用或者一个值

#### ES6浏览器加载规则

##### 异步加载
- defer 要等到整个页面正常渲染结束，才会执行
- async 一旦下载完，渲染引擎就会中断渲染，执行这个脚本以后，再继续渲染

> script 标签打开defer或async属性，脚本就会异步加载。渲染引擎遇到这一行命令，就会> 开始下载外部脚本，但不会等它下载和执行，而是直接执行后面的命令
> defer是“渲染完再执行”，async是“下载完就执行”
> 如果有多个defer脚本，会按照它们在页面出现的顺序加载，而多个async脚本是不能保证加载顺序的

##### 加载规则
- 浏览器加载ES6 模块，也使用script 标签，但是要加入type="module"属性
- 浏览器对于带有type="module" 的script，都是异步加载，不会造成堵塞浏览器，即等到整个页面渲染完，再执行模块脚本，等同于打开了script标签的defer属性，也可以手动设置async属性
- ES6 模块也允许内嵌在网页中，语法行为与加载外部脚本完全一致


## 区别

### ES6与CommonJS模块的差异

#### 输出方式
- CommonJS 模块输出的是一个值的拷贝
- ES6 模块输出的是值的引用

> CommonJS 模块输出的是值的拷贝，也就是说，一旦输出一个值，模块内部的变化就影响不到这个值
> ES6 模块下JS 引擎对脚本静态分析的时候，遇到模块加载命令import，就会生成一个只读引用。等到脚本真正执行时，再根据这个只读引用，到被加载的那个模块里面去取值
> ES6 模块不会缓存运行结果，而是动态地去被加载的模块取值，并且变量总是绑定其所在的模块
> ES6 输入的模块变量，只是一个“符号连接”，所以这个变量是只读的，对它进行重新赋值会报错
> export输出的是同一个值，不同的脚本加载这个接口，得到的都是同样的实例

#### 运行方式
- CommonJS 模块是运行时加载
- ES6 模块是编译时输出接口

> 因为 CommonJS 加载的是一个对象（即module.exports属性），该对象只有在脚本运行完才会生成。而 ES6 模块不是对象，它的对外接口只是一种静态定义，在代码静态解析阶段就会生成

#### this
- ES6 模块之中，顶层的this指向undefined
- CommonJS 模块的顶层this指向当前模块

### AMD和CMD

#### 相同点

##### 导出模块
- exports.xxx = xxx
- module.exports = xxx
- return
- factory === Object

##### 定义模块
都支持Modules/Wrappings的写法，dependencies数组为空，但是factory函数的形参必须手工写上require,exports,module

#### 不同点

- AMD：提前执行（异步加载：依赖先执行）+延迟执行
- CMD：延迟执行（运行到需加载，根据顺序执行）

##### 模块定义时对依赖的处理不同
> AMD推崇依赖前置，在定义模块的时候就要声明其依赖的模块
> CMD推崇就近依赖，只有在用到某个模块的时候再去require
> 这种区别各有优劣，只是语法上的差距，而且requireJS和SeaJS都支持对方的写法

##### 对依赖模块的执行时机处理不同，注意不是加载的时机或者方式不同
> 加载模块都是异步的，只不过AMD依赖前置，js可以方便知道依赖模块是谁，立即加载，而CMD就近依赖，需要使用把模块变为字符串解析一遍才知道依赖了那些模块，这也是很多人诟病CMD的一点，牺牲性能来带来开发的便利性，实际上解析模块用的时间短到可以忽略

> AMD在加载模块完成后就会执行改模块，所有模块都加载执行完后会进入require的回调函数，执行主逻辑，这样的效果就是依赖模块的执行顺序和书写顺序不一定一致，看网络速度，哪个先下载下来，哪个先执行，但是主逻辑一定在所有依赖加载完成后才执行

> CMD加载完某个依赖模块后并不执行，只是下载而已，在所有依赖模块加载完成后进入主逻辑，遇到require语句的时候才执行对应的模块，这样模块的执行顺序和书写顺序是完全一致的

## 其他

### exports和module.exports
module.exports     真正的接口
exports            辅助工具，最终返回给调用的是module.exports而不是exports

exports 是指向的 module.exports 的引用
module.exports 初始值为一个空对象 {}，所以 exports 初始值也是 {}
require() 返回的是 module.exports 而不是 exports

> exports 起初是一个指向 module.exports 对象的变量
> exports 仅仅是 module.exports 的一个引用。在 factory 内部给 exports 重新赋值时，并不会改变module.exports 的值
> module 是一个对象，上面存储了与当前模块相关联的一些属性和方法


## 链接

### AMD
- [玩转AMD系列 - 设计思路篇](http://efe.baidu.com/blog/dissecting-amd-what/)
- [玩转AMD系列 - 应用实践篇](http://efe.baidu.com/blog/dissecting-amd-how/)
- [玩转AMD系列 - Loader篇](http://efe.baidu.com/blog/dissecting-amd-loader/)
- [AMD spec](https://github.com/amdjs/amdjs-api/wiki/AMD)
- [AMD Require](https://github.com/amdjs/amdjs-api/wiki/require)
- [AMD Common-Config](https://github.com/amdjs/amdjs-api/wiki/Common-Config)
- [AMD Loader-Plugins](https://github.com/amdjs/amdjs-api/wiki/Loader-Plugins)
- https://ponyfoo.com/articles/es6-destructuring-in-depth

