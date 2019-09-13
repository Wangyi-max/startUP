# 什么是startUP

和requireJS相似的，startUP也是用JavaScript编写的js框架，主要功能是可以按不同的先后依赖关系对 JavaScript 等文件的进行加载工作，可简单理解为js文件的加载器，它非常适合在浏览器中使用，它可以确保所依赖的js文件加载完成之后再加载当前的js文件，这在大量使用js文件的项目中可确保各个js文件的先后加载顺序，确保避免了以前因某些原因某个文件加载慢而导致其它加载快的文件需要依赖其某些功能而出现某函数或某变量找不到的问题。


# 1. 简述JavaScript模块化编程

在早期编写JavaScript时，我们只需在 script 标签内写入JavaScript的代码就可以满足我们对页面交互的需要了。但随着时间的推移，时代的发展，原本的那种简单粗暴的编写方式所带来的诸如逻辑混乱，文件依赖管理问题，全局变量暴露，开发效率低，等问题接踵而至，社区为了解决这些问题提出了很种的解决方案，其中javaScript模块化编程是目前最有效的一种方式。

## 先驱者Commonjs
2009年Node.js横空出世，主要用于服务器编程。而对于服务器端来说，必须要拥有模块化开发的方式。因此Commonjs社区制定了一个与社区同名的关于模块化的规范——Commonjs。

**Commonjs规范分为三部分:**
- module(模块标识)   require(模块引用)   exports(模块定义）
1. module变量在每个模块内部，就代表当前模块；
2. exports属性是对外的接口，用于导出当前模块的方法或变量；
3. require()用来加载外部模块，读取并执行js文件，返回该模块的exports对象；

根据CommonJS规范的规定，每个文件就是一个模块，有自己的作用域，也就是在一个文件里面定义的变量、函数、类，都是私有的，对其他文件是不可见的。

```javascript
// a.js
const name = "max";
const getName = function() {
  return name;
}
```

上面代码中，变量name和函数getName，是当前文件a.js私有的，其他文件不可见。如要在其他的文件中访问变量name，最好的方式是输出模块对象module.exports:

```javascript
// a.js
const name = "max";
const getName = function() {
  return name;
}
module.exports.getName = getName;
```
上面代码通过module.exports对象扩展了一个方法，该方法就是模块外部与内部通信的桥梁。加载模块需要使用require方法，该方法读取一个文件并执行，最后返回当前加载文件内部的module.exports对象。

```javascript
var object = require('./a.js');
console.log(object.getName());  //max
```
CommonJS规范加载模块是同步的，也就是说，加载完成才执行后面的操作，Node.js主要用于服务器编程，模块都是存在本地硬盘，由于这个特性决定了CommonJS只能在服务器端大规模使用。
而在浏览器端由于文件都放在服务器上，如果网速不够快的话，前面的文件如果没有加载完成，浏览器就会失去响应！由此不适合同步方式加载，但模块化开发管理方式势在必行，根据这个需求，针对浏览器的模块化规范就产生了 ——**AMD**就产生了！

## AMD 异步模块定义
AMD的全名叫做：Asynchronous Module Definition即异步模块定义。它采用了异步的方式来加载模块，然后在回调函数中执行主逻辑，因此模块的加载不影响它后面的模块的运行。

```javascript
define(id?, deps ?, factory);
```
**规范如下：**

1. 用全局函数define来定义模块;
2. id为模块标识;
3. deps为依赖的模块数组;
4. 如果factory为函数，模块对外暴漏API的方法有三种：return任意类型的数据;
5. 如果factory为对象，则该对象即为模块的返回值

在加载模块方面，AMD和CommonJs都是使用require。
```javascript
require([module]);
```
参数[module]，是一个数组，里面的成员就是需要加载的模块；
```javascript
//启动程序
requirejs([module], callback);
```
requirejs等同于require, 参数callback，是加载成功之后的回调函数。

虽然require.js实现了异步的模块化，解决了命名冲突，文件依赖，增加文件的独立性降低了耦合度，但它仍然有一些不足的地方，在使用require.js的时候，我们必须要提前加载所有的依赖，然后才可以使用，而不是需要使用时再加载，使得初次加载其他模块的速度较慢，提高了开发成本。

而startUP则借鉴了require.js的设计思想，并且改变了他不足的地方。

