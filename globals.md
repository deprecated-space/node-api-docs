# [Global Objects](https://nodejs.org/dist/latest-v8.x/docs/api/globals.html)

有些全局变量不是 global scope，而是 module scope。如何理解两者？我的简单理解是如果在 node 命令行中能够使用，就是 global scope，比如 setTimeout，如果不能（只能在代码中用）则是 module scope。

下面列出的全局变量都是 node 中使用的，但是有一些 JavaScript 语言本身所拥有的 [全局变量](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects)，在 node 中同样可以使用。

---



node 中的全局变量主要有（没有特别注明的都是 global scope）：

- Buffer 类

- console 对象

- process 对象

- global 相当于 win 中的 `window`

- __dirname （module scope）

- __filename（module scope）

- setImmediate(callback[, …args])

- setTimeout(callback, delay[, …args])

- setInterval(callback, delay[, …args])

- clearImmediate(immediateObject)

- clearTimeout(timeoutObject)

- clearInterval(intervalObject)

- exports 同 `module.exports`（module scope）

- module 对象（文档中说是 module scope，实践了下可能是 global scope）

- require() 

  - require.cache
  - require.resolve()

  ​