# [Console](https://nodejs.org/dist/latest-v8.x/docs/api/console.html)

该模块提供两个功能：

- 一个全局的 `console` 实例，其输出内容写入 process.stdout 和 process.stderr 中
- `Console` 类，能写入任何 Node.js 流中

## Class: Console

全局的 `console` 实例的输出内容写入 process.stdout 和 process.stderr 中，不过 `Console` 类的实例可以写入任意的 Node.js 流中（比如文件）。

引入 `Console` 类：

```javascript
const { Console } = require('console');

// 或者
const { Console } = console;
```

### new Console(stdout[, stderr])

- `stdout` \<Writable>
- `stderr` \<Writable>

创建一个新的 `Console` 实例，log 或者 info 的信息写入 stdout，warning 或者 error 写入 stderr，如果没有提供 stderr，则 warning 和 error 也写入 stdout。

```javascript
const output = fs.createWriteStream('./stdout.log');
const errorOutput = fs.createWriteStream('./stderr.log');
// custom simple logger
const logger = new Console(output, errorOutput);
// use it like console
const count = 5;
logger.log('count: %d', count);
// in stdout.log: count 5
```

全局的 `console` 其实是一个特殊的 `Console` 实例：

```javascript
// 等价于全局的 console 
new Console(process.stdout, process.stderr);
```

### console.log([data]\[, ...args])

- `data` \<any>
- `...args` \<any>

可以传入多个参数，可以格式化字符串。如果第一个参数没有类似 `%d` 的占位符，则有多少参数都直接输出，如果有，则用后面的参数依次格式化第一个参数（字符串）中的占位符。

```javascript
console.log('%d and %d', 12, 34); // 12 and 34
console.log(12, 23); // 12 23
```

### console.info([data]\[, ...args])

console.log() 别称。

### console.error([data]\[, ...args])

- `data` \<any>
- `...args` \<any>

和 console.log() 用法类似，只是会输出到 stderr 中（如果 stderr 有定义的话）

### console.warn([data]\[, ...args])

console.error() 别称。

### console.time(label) & console.timeEnd(label)

- `label` \<string>

计算时间间隔。

```javascript
console.time('time');

setTimeout(() => {
  console.timeEnd('time'); // time: 2002.551ms
}, 2000);
```

### console.dir(obj[, options])

- `obj` \<any>
- `options` \<Object>
  - `showHidden` \<boolean>
  - `depth` \<number>
  - `colors` \<boolean>

console.log() 可以用于输出任何类型的数据，但是如果是对象，最好用 console.dir() ，其还有三个可配置项。

- `showHidden` 如果设置为 true，可以打印对象的 non-enumerable 和 symbol 属性。默认为 false
- `depth` 默认情况下只能打印对象的两层（console.log() 也是一样），可以手动开大，如果要无限递归打印，可以将其值设置为 null
- `colors` 高亮输出，默认为 false

### console.trace([message]\[, ...args])

- `message` \<any>
- `...args` \<any>

代码当前位置的堆栈追踪，输出到 stderr 中。

```javascript
> console.trace('show me')
Trace: show me
    at repl:1:9
    at ContextifyScript.Script.runInThisContext (vm.js:44:33)
    at REPLServer.defaultEval (repl.js:239:29)
    at bound (domain.js:301:14)
    at REPLServer.runBound [as eval] (domain.js:314:12)
    at REPLServer.onLine (repl.js:433:10)
    at emitOne (events.js:120:20)
    at REPLServer.emit (events.js:210:7)
    at REPLServer.Interface._onLine (readline.js:278:10)
    at REPLServer.Interface._line (readline.js:625:8)
```

### console.assert(value[, message]\[, ...args])

- `value` \<any>
- `message` \<any>
- `...args` \<any>

 断言，如果 value 是 true，什么都不会发生，如果是 false，则抛出一个 AssertionError 错误，中断 Node.js. 进程，并打印 message 到控制台。需要注意的是，相同情况下在浏览器中代码继续往下执行，并不会中断。

```javascript
console.assert(true, 'does nothing');
// OK
console.assert(false, 'Whoops %s', 'didn\'t work');
// AssertionError: Whoops didn't work
```

