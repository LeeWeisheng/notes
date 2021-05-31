# Node.js 之 Globals

Node.js 中，一个文件即是一个模块，模块的代码运行在一个包裹器函数中，此函数注入了一些模块信息相关的参数，所以对于模块来说，全局属性包括两个部分：包裹器函数的参数和全局属性。

## 1. 全局对象 `global`

- Node.js 的全局属性都属于全局对象 `global`，它包含 [JavaScript 自身的所有全局属性](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects)。下面是 Node.js 全局属性列表：

  ```
  [
    'Array',                'ArrayBuffer',       'Atomics',
    'BigInt',               'BigInt64Array',     'BigUint64Array',
    'Boolean',              'Buffer',            'DataView',
    'Date',                 'Error',             'EvalError',
    'FinalizationRegistry', 'Float32Array',      'Float64Array',
    'Function',             'Infinity',          'Int16Array',
    'Int32Array',           'Int8Array',         'Intl',
    'JSON',                 'Map',               'Math',
    'NaN',                  'Number',            'Object',
    'Promise',              'Proxy',             'RangeError',
    'ReferenceError',       'Reflect',           'RegExp',
    'Set',                  'SharedArrayBuffer', 'String',
    'Symbol',               'SyntaxError',       'TextDecoder',
    'TextEncoder',          'TypeError',         'URIError',
    'URL',                  'URLSearchParams',   'Uint16Array',
    'Uint32Array',          'Uint8Array',        'Uint8ClampedArray',
    'WeakMap',              'WeakRef',           'WeakSet',
    'WebAssembly',          'clearImmediate',    'clearInterval',
    'clearTimeout',         'console',           'decodeURI',
    'decodeURIComponent',   'encodeURI',         'encodeURIComponent',
    'escape',               'eval',              'global',
    'globalThis',           'isFinite',          'isNaN',
    'parseFloat',           'parseInt',          'process',
    'queueMicrotask',       'setImmediate',      'setInterval',
    'setTimeout',           'undefined',         'unescape'
  ]
  ```

  其中，`Buffer`、`process`、`TextDecoder`、`TextEncoder` 为 Node.js 特有的，`console`、`URL`、`URLSearchParams`、`WebAssembly`、`setImmediate`、`setInterval`、`setTimeout`、`clearImmediate`、`clearInterval`、`clearTimeout` 为 Node.js 的实现，与浏览器（即 `window` 对象）的实现不一样。

- 在 Node.js 的执行环境中，全局对象 `global` 是寻找变量的顶层作用域。

- Node.js 中，`globalThis` 全等于 `global`。

- 可以在模块中为全局对象 `global` 添加属性，且可以在不同的模块中使用，但一般不推荐这么做。

## 2. 包裹器参数

Node.js 的[模块包裹器](https://github.com/nodejs/node/blob/v14.x/lib/internal/modules/cjs/loader.js#L184)类似于下面的代码：

```js
(function (exports, require, module, __filename, __dirname) {
  // 模块代码，即文件内容
});
```

在执行的时候，Node.js 为包裹器注入了五个与当前模块相关的五个参数：

- `exports`：导出对象。
- `require`：加载函数。
- `module`：当前模块信息。
- `__filename`：当前模块文件的绝对路径。
- `__dirname`：当前模块所在目录的绝对路径。
