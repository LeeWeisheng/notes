# Node.js 之 Buffer

Node.js 的 Buffer 基于 JavaScript 的 [Uint8Array](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Uint8Array)，它在 V8 引擎之外分配固定大小（32 位操作系统下最大约为 1 GB）的原始内存，旨在处理原始二进制数据。Buffer 是一个可迭代的类数组，每项的数值范围在 0 ~ 255 之间，即一个字节的存储大小。使用 Buffer 的目的之一就是为了速度。

## 1. 创建

- `Buffer.alloc(size[, fill[, encoding]])`：安全分配内存，以 `fill` 填充每项。如果 `fill` 是字符串，则以 `encoding` 为编码解析，`encoding` 默认为 'utf8'。
- `Buffer.allocUnsafe(size)`：不安全分配内存，可能包含敏感数据，如果大小较小（小于 8 KB），Node.js 会考虑使用自身的内存池，以增强速度。
- `Buffer.allocUnsafeSlow(size)`：不安全分配内存，可能包含敏感数据，但不会使用 Node.js 自身的内存池。
- `Buffer.from`：从其他数据创建 Buffer 实例。
  - `Buffer.from(array)`
  - `Buffer.from(arrayBuffer[, byteOffset[, length]])`
  - `Buffer.from(buffer)`
  - `Buffer.from(object[, offsetOrEncoding[, length]])`
  - `Buffer.from(string[, encoding])`

## 2. 判断

- `Buffer.compare(buf1, buf2)`：比较两个 Buffer 的排序顺序，返回 0、1、-1。
- `Buffer.isBuffer(obj)`：判断一个对象是否是 Buffer 实例。
- `Buffer.isEncoding(encoding)`：检查指定字符编码是否是 Node.js Buffer 支持的编码。
- `buf.compare(target[, targetStart[, targetEnd[, sourceStart[, sourceEnd]]]])`：比较两个 Buffer 的排序顺序，返回 0、1、-1。
- `buf.equals(otherBuffer)`：判断两个 Buffer 的字节是否完全相同，返回布尔值。
- `buf.includes(value[, byteOffset][, encoding])`：判断是否包含 value 值。

## 3. 操作

- `Buffer.byteLength(string[, encoding])`：获取指定编码的字符串的字节大小。
- `Buffer.concat(list[, totalLength])`：合并 Buffer，返回一个新的 Buffer 实例。
- `buf.copy(target[, targetStart[, sourceStart[, sourceEnd]]])`：拷贝。
- `buf.fill(value[, offset[, end]][, encoding])`：填充。
- `buf.write(string[, offset[, length]][, encoding])`：填充字符串。
- `buf.indexOf(value[, byteOffset][, encoding])`：索引。
- `buf.lastIndexOf(value[, byteOffset][, encoding])`：索引，从后往前搜索。
- `buf.slice([start[, end]])` === `buf.subarray([start[, end]])`：返回一个新的 Buffer，它引用与原始的 Buffer 相同的内存，但是由 start 和 end 索引进行偏移和裁剪。

## 4. 转换

- `buf.toJSON()`：转换为类似 `{ "type": "Buffer", "data": [1, 2, 3, 4, 5] }` 的结构。
- `buf.toString([encoding[, start[, end]]])`：转换为自定编码的字符串。

## 5. 实例属性

- `buf.buffer`：底层的 ArrayBuffer 对象。
- `buf.byteOffset`：底层的 ArrayBuffer 对象的 byteOffset。
- `buf.length`：字节数，类数组长度。
