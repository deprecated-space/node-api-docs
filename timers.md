# [Timers](https://nodejs.org/dist/latest-v8.x/docs/api/timers.html)

timer 模块能够 **直接使用**，不需要通过`require('timers')` 去引用

## Scheduling Timers

### setImmediate(callback[, ...args])

- `callback` \<Function>
- `...args` \<any> 

### setInterval(callback, delay[, ...args])

- `callback` \<Function>
- `delay` \<number>
- `...args` \<any>

### setTimeout(callback, delay[, ...args])

- `callback` \<Function>
- `delay` \<number>
- `...args` \<any>

## Cancelling Timers

### clearImmediate(immediate)

### clearInterval(timeout)

### clearTimeout(timeout)

## Class: Timeout

调用 setTimeout 或者 setInterval，会返回一个 Timeout 的实例，可以作为 clearTimeout 或者 clearInterval 函数的参数。Timeout 的实例还有两个方法。

### timeout.ref()

默认情况下，Timeout 的实例就是 "ref'd"，不需要用 timeout.ref() 去手动开启，除非之前调用了 timeout.unref() 

### timeout.unref()

当 event loop 中没有其他事件时（只有一个 setTimeout 或者 setInterval 事件，并且调用了 timeout.unref()），就会退出 Node.js 进程。详细可见这个讨论帖子 [timer 的 unref 函数](https://cnodejs.org/topic/570924d294b38dcb3c09a7a0)

