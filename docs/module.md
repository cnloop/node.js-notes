## 模块定义

- 现实角度：各个零部件的分开加工与组装，类似手机等流水线工厂
- 程序角度：将一大段代码拆分成小块，根据业务进行分类，利于维护与解耦



## 模块分类

- 核心模块： Node 本身提供，具名的，例如 fs 文件操作模块、http 网络操作模块
- 第三方模块：例如github上的 `mime`、`art-template`、`marked`
- 用户自定义模块



## 核心模块

> 参考文档：https://nodejs.org/dist/latest-v9.x/docs/api/

| 模块名称                                     | 作用           |
| ---------------------------------------- | ------------ |
| [fs](https://nodejs.org/dist/latest-v9.x/docs/api/fs.html) | 文件操作         |
| [http](https://nodejs.org/dist/latest-v9.x/docs/api/http.html) | 网络操作         |
| [path](https://nodejs.org/dist/latest-v9.x/docs/api/path.html) | 路径操作         |
| [url](https://nodejs.org/dist/latest-v9.x/docs/api/url.html) | url 地址操作     |
| [os](https://nodejs.org/dist/latest-v9.x/docs/api/os.html) | 操作系统信息       |
| [net](https://nodejs.org/dist/latest-v9.x/docs/api/net.html) | 一种更底层的网络操作方式 |
| [querystring](https://nodejs.org/dist/latest-v9.x/docs/api/querystring.html) | 解析查询字符串      |
| [util](https://nodejs.org/dist/latest-v9.x/docs/api/util.html) | 工具函数模块       |
| ...                                      | ...          |



## 模块通信

- 模块作用域：一个模块其作用域的范围只能是当前模块，避免了全局命名空间冲突的问题
- 模块通信规则：require、exports、module.exports

### require

```javascript
// 核心模块
var fs = require('fs')

// 第三方模块
// npm install marked
var marked = require('marked')

// 用户模块（自己写的），正确的，正确的方式
// 注意：加载自己写的模块，相对路径不能省略 ./
var foo = require('./foo.js')

// 用户模块（自己写的），正确的（推荐），可以省略后缀名 .js
var foo = require('./foo')
```

### exports

- 导出多个成员：（麻烦，不推荐）：


```javascript
// 导出多个成员：写法一
module.exports.a = 123
module.exports.b = 456
module.exports.c = 789
```

- 导出多个成员：写法二（推荐）：


Node 为了降低开发人员的痛苦，所以为 `module.exports` 提供了一个别名 `exports` （下面协大等价于上面的写法）。

```javascript
console.log(exports === module.exports) // => true
exports.a = 123
exports.b = 456
exports.c = 789
exports.fn = function () { 
}
```

导出多个成员：写法三（代码少可以，但是代码一多就不推荐了）：

```javascript
// module.exports = {
//   d: 'hello',
//   e: 'world',
//   fn: function () {
//     // 
//     // 
//     // 
//     // 
//     // 
//     // 
//     // 
//     // 
//     // 
//     // 
//     // 
//     // 
//     // 
//     // 
//     // 
//     // 
//     // 
//     // 
//     // 
//     // 
//     // 
//     // 
//     // 
//     // 
//     // 
//     // 
//     // 
//     // 
//     // fs.readFile(function () {
      
//     // })
//   }
// }
```

- 导出单个成员：（唯一的写法）：


```javascript
// 导出单个成员：错误的写法
// 因为每个模块最终导出是 module.exports 而不是 exports 这个别名
// exports = function (x, y) {
//   return x + y
// }


// 导出单个成员：必须这么写
module.exports = function (x, y) {
  return x + y
}
```

注意：导出单个只能导出一次，下面的情况后者会覆盖前者：

```javascript
module.exports = 'hello'

// 以这个为准，后者会覆盖前者
module.exports = function (x, y) {
  return x + y
}
```

### 为什么 `exports = xxx` 不行

exports 和 `module.exports` 的一个引用：

```javascript
function fn() {
  // 每个模块内部有一个 module 对象
  // module 对象中有一个成员 exports 也是一个对象
  var module = {
    exports: {}
  }

  // 模块中同时还有一个成员 exports 等价于 module.exports
  var exports = module.exports

  console.log(exports === module.exports) // => true

  // 这样是可以的，因为 exports === module.exports
  // module.exports.a = 123
  // exports.b = 456

  // 这里重新赋值不管用，因为模块最后 return 的是 module.exports
  // exports = function () {
  // }

  // 这才是正确的方式
  module.exports = function () {
    console.log(123)
  }

  // 最后导出的是 module.exports
  return module.exports
}

var ret = fn()

console.log(ret)
```

### exports 和 module.exports 的区别

- exports 和 module.exports 的区别
  + 每个模块中都有一个 module 对象
  + module 对象中有一个 exports 对象
  + 我们可以把需要导出的成员都挂载到 module.exports 接口对象中
  + 也就是：`moudle.exports.xxx = xxx` 的方式
  + 但是每次都 `moudle.exports.xxx = xxx` 很麻烦，点儿的太多了
  + 所以 Node 为了你方便，同时在每一个模块中都提供了一个成员叫：`exports`
  + `exports === module.exports` 结果为  `true`
  + 所以对于：`moudle.exports.xxx = xxx` 的方式 完全可以：`expots.xxx = xxx`
  + 当一个模块需要导出单个成员的时候，这个时候必须使用：`module.exports = xxx` 的方式
  + 不要使用 `exports = xxx` 不管用
  + 因为每个模块最终向外 `return` 的是 **module.exports**
  + 而 `exports` 只是 `module.exports` 的一个引用
  + 所以即便你为 `exports = xx` 重新赋值，也不会影响 `module.exports`
  + 但是有一种赋值方式比较特殊：`exports = module.exports` 这个用来重新建立引用关系的
  + 之所以让大家明白这个道理，是希望可以更灵活的去用它

### 特殊的导出方式

```javascript
exports = module.exports = function () {
  console.log('默认函数被调用了')
}

exports.ajax = function () {
  console.log('ajax 方法被调用了')
}

exports.get = function () {
  console.log('get 方法被调用了')
}

```
