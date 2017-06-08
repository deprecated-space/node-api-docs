# [Events](https://nodejs.org/dist/latest-v8.x/docs/api/events.html#events_emitter_emit_eventname_args)

Node.js 中很多 API 基于异步的事件驱动，所以 Events 模块非常重要。不同于之前的模块，`require('events')` 的结果是一个 **function**，而不是 object。

一个简单的实例：

```javascript
const EventEmitter = require('events');

class MyEmitter extends EventEmitter {}

// 必须是 EventEmitter 的实例（因为继承自 MyEmitter，所以 MyEmitter 的实例其实也是 EventEmitter 的实例）
const myEmitter = new MyEmitter();

myEmitter.on('event', () => {
  console.log('an event occurred!');
});

myEmitter.emit('event');
```

## Passing arguments and `this` to listeners

回调函数可以传参数。如果需要在事件监听的回调函数中用到 this，并且 this 需要指向 EventEmitter 的实例，请不要用 ES6 的箭头函数。

```javascript
const myEmitter = new MyEmitter();

myEmitter.on('event', function(a, b) {
  console.log(a, b, this === myEmitter); // a b true
});

myEmitter.emit('event', 'a', 'b');
```

## 异步 VS 同步

当事件抛出时，事件监听的回调会按照注册顺序依次同步触发，如果有必要我们也可以添加事件为异步触发，用 `setImmediate()` 或者 `process.nextTick()` 方法。

```javascript
const myEmitter = new MyEmitter();

myEmitter.on('event', () => {
  setImmediate(() => {
    console.log('this happens asynchronously');
  });
});

myEmitter.on('event', () => {
  console.log('this is added secondly');
});

myEmitter.emit('event');
// this is added secondly
// this happens asynchronously
```

## Error events

如果一个 EventEmitter 实例中出错，默认会抛出一个名为 `error` 的事件，如果不做处理，Node.js 进程就会中断。

```javascript
const EventEmitter = require('events');

class MyEmitter extends EventEmitter {}

const myEmitter = new MyEmitter();

myEmitter.emit('error', new Error('whoops!'));
```

可以这样处理：

```javascript
const myEmitter = new MyEmitter();

process.on('uncaughtException', (err) => {
  console.error('whoops! there was an error');
});

myEmitter.emit('error', new Error('whoops!'));
```

或者注册一个名为 `error` 的事件：

```javascript
const myEmitter = new MyEmitter();

myEmitter.on('error', (err) => {
  console.error('whoops! there was an error');
});

myEmitter.emit('error', new Error('whoops!'));
```

## Class: EventEmitter

### emitter.on(eventName, listener)

- `eventName` \<any>
- `listener` \<Function>

注册事件，默认会将该事件放到事件数组（listeners array）的最后面，eventName 可以重名。

```javascript
const myEmitter = new MyEmitter();

myEmitter.on('event', () => {
  console.log(1);
});

myEmitter.on('event', () => {
  console.log(2);
});

myEmitter.emit('event');
// 1
// 2
```

### emitter.addListener(eventName, listener)

emitter.on 的别称（个人感觉 addListener 更好理解，注册事件 …）

### emitter.once(eventName, listener)

- `eventName` \<any> 
- `listener` \<Function>

该事件只能触发一次（正确理解应该是第二次触发时，事件监听 remove）

```javascript
const myEmitter = new MyEmitter();

myEmitter.once('event', () => {
  console.log(1);
});

myEmitter.emit('event'); // 1
myEmitter.emit('event'); // 被忽略
```

### emitter.prependListener(eventName, listener)

- `eventName` \<any> 
- `listener` \<Function>

用 emitter.on 注册的事件会被放到监听数组（listeners array）最后面，用 emitter.prependListener 可以将其放到最前面。

```javascript
const myEmitter = new MyEmitter();

myEmitter.on('event', () => {
  console.log(1);
});

myEmitter.prependListener('event', () => {
  console.log(2);
});

myEmitter.emit('event');
// 2
// 1
```

### emitter.prependOnceListener(eventName, listener)

- `eventName` \<any> 
- `listener` \<Function>

很好理解，放到 listeners array 最前面，并且只会被触发一次。

### emitter.emit(eventName[, ...args])

- `eventName` \<any>
- `...args` \<any>

PS: 如果 eventName 有注册事件监听，返回 true，否则 false

### emitter.eventNames()

返回注册的事件名数组，数组元素是字符串或者 Symbols，**会去重**。

```javascript
const myEmitter = new MyEmitter();

myEmitter.on('event', () => {
  console.log(1);
});

myEmitter.on('event', () => {
  console.log(2);
});

const sym = Symbol('symbol');
myEmitter.on(sym, () => {});

console.log(myEmitter.eventNames());
// [ 'event', Symbol(symbol) ]
```

### emitter.listeners(eventName)

- `eventName` \<any>

返回事件名为 eventName 的回调函数组成的数组。

```javascript
const myEmitter = new MyEmitter();

myEmitter.on('event', () => {
  console.log(1);
});

myEmitter.on('event', () => {
  console.log(2);
});

const sym = Symbol('symbol');
myEmitter.on(sym, () => {});

console.log(myEmitter.listeners('event'));
// [ [Function], [Function] ]
```

### emitter.listenerCount(eventName)

- `eventName` \<any>

返回注册名为 eventName 的事件数。

```javascript
const myEmitter = new MyEmitter();

myEmitter.on('event', () => {
  console.log(1);
});

myEmitter.on('event', () => {
  console.log(2);
});

console.log(myEmitter.listenerCount('event')); // 2
```

### EventEmitter.defaultMaxListeners

默认情况下，我们最多只能为一个 EventEmitter 实例注册 10 个事件，再多就要报 warning，因为 EventEmitter.defaultMaxListeners 默认值为 10。我们可以手动将其开大，**不过不建议这么做**，这样会改变所有 EventEmitter 实例的最大监听数量。建议用 emitter.setMaxListeners() 方法修改单个 emitter 的默认值。

### emitter.setMaxListeners(n)

- `n` \<integer>

### emitter.getMaxListeners()

返回通过 emitter.setMaxListeners(n) 设置的数据或者 EventEmitter.defaultMaxListeners，前者优先级高。

### emitter.removeListener(eventName, listener)

- `eventName` \<any>
- `listener` \<Function>

```javascript
const myEmitter = new MyEmitter();

function callback() { console.log(1) }

myEmitter.on('event', callback);
myEmitter.on('event', () => console.log(2))
myEmitter.on('event', callback);

myEmitter.removeListener('event', callback);

myEmitter.emit('event'); 
// 1
// 2
```

需要注意的几点：

- 一次只能 remove 一个，看起来应该是 listeners array 从后往前扫描
- 回调函数需要指定函数名，不能是匿名函数

### emitter.removeAllListeners([eventName])

- `eventName` \<any>

```javascript
const myEmitter = new MyEmitter();

function callback() { console.log(1) }

myEmitter.on('event', callback);
myEmitter.on('event', () => console.log(2))
myEmitter.on('event', callback);

console.log(myEmitter.listenerCount('event')); // 3

myEmitter.removeAllListeners('event');

console.log(myEmitter.listenerCount('event')); // 0
```

### Event: 'newListener'

- `eventName` \<any> 事件注册名
- `listener` \<Function> 事件触发后的回调


当 EventEmitter 实例中有事件被注册时，在这个实例上会默认抛出一个叫做 `'newListener'`的事件。 

```javascript
const myEmitter = new MyEmitter();

myEmitter.on('newListener', (event, listener) => {
  console.log('newListener');
  console.log(event);
  listener();
});

myEmitter.on('event', () => {
  console.log('A');
});

// newListener
// event
// A
```

### Event: 'removeListener'

- `eventName` \<any> 事件注册名
- `listener` \<Function> 事件触发后的回调

当 listener 被 remove 时，会默认抛出一个叫做 `'removeListener'` 的事件。

```javascript
const myEmitter = new MyEmitter();

myEmitter.on('removeListener', (event, listener) => {
  console.log(1);
});

myEmitter.on('event', () => {});
myEmitter.on('event', () => {});
myEmitter.on('event', () => {});
myEmitter.on('event', () => {});

myEmitter.removeAllListeners('event');
// 1
// 1
// 1
// 1
```
