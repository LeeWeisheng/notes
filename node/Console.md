# Console

Console 打印并不总是同步的，也不总是异步的。

## 1. 自定义 Console 的输出

```js
const fs = require("fs");
const path = require("path");

const stdout = fs.createWriteStream(
  path.resolve(__dirname, "./custom-console-stdout.log")
);
const stderr = fs.createWriteStream(
  path.resolve(__dirname, "./custom-console-stderr.log")
);

const logger = new console.Console(stdout, stderr);

logger.log("test out");
logger.error(new Error("test error"));
```

## 2. 方法

打印：

- `console.log([data][, ...args])`：输出流。
- `console.error([data][, ...args])`：错误流。

```js
console.log("%s：%d", "计数", 10);
console.error("%s：%d", "计数", 10);
```

计数：

- `console.count([label])`
- `console.countReset([label])`

```js
console.count("计数");
console.count("计数");
console.countReset("计数");
console.count("计数");
console.count("计数");
```

计时：

- `console.time([label])`
- `console.timeLog([label][, ...data])`
- `console.timeEnd([label])`

```js
console.time("计时");
console.timeLog("计时", "数据");
console.timeEnd("计时");
```

分组：

- `console.group([...label])`
- `console.groupEnd()`

```js
console.group("分组");
console.log("分组内的消息");
console.groupEnd("分组");
```

断言：

- `console.assert(value[, ...message])`：判断是否为真，为 false 时打印。

```js
console.assert(false, "断言消息");
```

跟踪，打印堆栈信息：

- `console.trace([message][, ...args])`

```js
console.trace("跟踪");
```

表格：

- `console.table(tabularData[, properties])`

```js
console.table([
  { a: 1, b: "Y" },
  { a: "Z", b: 2 },
]);
```

清除：

- `console.clear()`
