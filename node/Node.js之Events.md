# Node.js 之 Events

事件触发器 EventEmitter 上所有绑定的事件都会被同步地按注册顺序调用。事件监听函数中的 `this` 默认指向事件触发器的实例，可以使用箭头函数修改其指向。

```js
const EE = require("events");
const ee = new EE();

function listener() {}

ee.on("eventName", listener);
ee.once("eventName", listener);
ee.off("eventName", listener);

ee.prependListener("eventName", listener);
ee.prependOnceListener("eventName", listener);
ee.removeAllListeners("eventName");
```
