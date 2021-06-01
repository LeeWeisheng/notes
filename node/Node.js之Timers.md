# Node.js 之 Timers

## 1. 方法

设置：

- `const timeout = setTimeout(callback[, ...args])`
- `const timeout = setInterval(callback[, ...args])`
- `const immediate = setImmediate(callback[, ...args])`

清除：

- `clearTimeout(timeout)`
- `clearInterval(interval)`
- `clearImmediate(immediate)`

## 2. Timeout

无论 `setTimeout` 方法还是 `setInterval` 方法，它们返回的都是 Timeout 对象：

- `timeout[Symbol.toPrimitive]()`：返回一个整数。
- `timeout.unref()`：取消定时任务与事件循环的关联，其作用就是如果此任务是**最后一次**任务，那么它不会被执行，否则无论是否执行了 `unref` 方法，只要它不是最后一次任务，它都会执行。执行多次 `unref` 方法与执行一次是一样的效果。
- `timeout.ref()`：取消 `unref` 的作用。
- `timeout.hasRef()`：判断此定时任务是否与事件循环关联，默认为 true。
- `timeout.refresh()`：重新计算定时时间。

## 3. Immediate

`setImmediate` 返回的也是对象：

- `immediate.unref()`：与 `timeout.unref()` 功能类似。
- `immediate.ref()`：与 `timeout.ref()` 功能类似。
- `immediate.hasRef()`：与 `timeout.hasRef()` 功能类似。
