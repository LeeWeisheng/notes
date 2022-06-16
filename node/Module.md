# Module

Node.js 中，一个文件即是一个模块，且文件的代码在下面的包裹器函数中运行。

```js
(function (exports, require, module, __filename, __dirname) {
  // 模块代码，即文件内容
});
```

那么，什么是一个模块呢？一个模块其实就是 Module 构造函数的实例。在文件代码运行之前，会先 `new` 一个 Module，生成实例 `module`，然后将 `module` 实例上的属性注入到包裹器函数的参数中。

## 1. `module` 实例

`module` 由 [Module 构造函数](https://github.com/nodejs/node/blob/v14.x/lib/internal/modules/cjs/loader.js#L150)实例化而来，它有以下属性和方法：

- `module.id`：通常情况下为文件的完整绝对路径。经过测试，入口文件的 `id` 为 `"."`。
- `module.filename`：当前模块文件的绝对路径，与 `__filename` 一样。
- `module.path`：当前模块所在目录的绝对路径，与 `__dirname` 一样。
- `module.exports`：模块的导出，为一个空对象 `{}`，与 `exports` 一样。
- `module.require`：加载方法，与 `require` 一样。
- `module.children`：子模块数组。
- `module.paths`：寻找 Node.js 包的路径数组，即 node_modules 的路径数组。默认从当前目录一直向上寻找。
- `module.loaded`：是否加载完毕，在当前模块运行时为 false，运行完毕后为 true。

## 2. `require` 方法

`require` 方法返回的是子模块的 `module.exports` 内容，而不是完整 `module` 实例。`require` 方法还有以下额外的属性和方法：

- `require.main`：主模块的实例。
- `require.cache`：模块缓存对象，键为绝对文件路径，值为模块实例对象。
- `require.resolve(request)`：返回目标模块的完整绝对路径。
- `require.resolve.paths(request)`：返回目标模块的 paths 数组。

## 3. 导出

模块的导出有两种方式，`exports` 和 `module.exports`。但真正的导出是 `module.exports`（`require` 方法返回的内容），`exports` 只是 `module.exports` 的短写，它们的关系如下：

```js
module.exports = exports = {};
```

## 4. 缓存

所有模块只有第一次 `require` 时才会运行，后面加载的都是从缓存对象 `require.cache` 中获取的值。也可以将缓存删除让其重新加载：

```js
delete require.cache[require.resolve("./child")];
```

## 5. 从字符串中加载

```js
const path = require("path");
const crypto = require("crypto");
const Module = require("module");

const cache = new Map();

function md5(content) {
  if (cache.has(content)) {
    return cache.get(content);
  } else {
    const value = crypto.createHash("md5").update(content).digest("hex");
    cache.set(content, value);
    return value;
  }
}

function requireFromString(content) {
  const id = path.resolve(__dirname, md5(content) + ".js");

  if (require.cache[id]) {
    return require.cache[id].exports;
  }

  const child = new Module(id, module);

  child.filename = id;
  child.paths = module.paths;
  child._compile(content, id);

  require.cache[id] = child;

  return child.exports;
}
```
