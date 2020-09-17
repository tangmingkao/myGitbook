### Buffer 缓冲器

###### 源代码: lib/buffer.js

Buffer 对象用于表示固定长度的字节序列。 许多 Node.js API 都支持 Buffer。

Buffer 类是 JavaScript 的 Uint8Array 类的子类，且继承时带上了涵盖额外用例的方法。 只要支持 Buffer 的地方，Node.js API 都可以接受普通的 Uint8Array。

Buffer 类在全局作用域中，因此无需使用 require('buffer').Buffer。

当在 Buffer 和字符串之间转换时，可以指定字符编码。 如果未指定字符编码，则使用 UTF-8 作为默认值。

###### Node.js 支持的字符编码如下：

-   'utf8': 多字节编码的 Unicode 字符。 许多网页和其他文档格式都使用 UTF-8。 这是默认的字符编码。 当将 Buffer 解码为不专门包含有效 UTF-8 数据的字符串时，则会使用 Unicode 替换字符 U+FFFD � 来表示这些错误。

-   'utf16le': 多字节编码的 Unicode 字符。 与 'utf8' 不同，字符串中的每个字符都会使用 2 个或 4 个字节进行编码。 Node.js 仅支持 UTF-16 的小端序变体。

-   'latin1': Latin-1 代表 ISO-8859-1。 此字符编码仅支持从 U+0000 到 U+00FF 的 Unicode 字符。 每个字符使用单个字节进行编码。 超出该范围的字符会被截断，并映射成该范围内的字符。

使用以上方法之一将 Buffer 转换为字符串，称为解码；将字符串转换为 Buffer，称为编码。

Node.js 还支持以下两种二进制转文本的编码。 对于二进制转文本的编码，其命名约定是相反的：将 Buffer 转换为字符串通常称为编码，而将字符串转换为 Buffer 则称为解码。

-   'base64': Base64 编码。 当从字符串创建 Buffer 时，此编码也会正确地接受 RFC 4648 第 5 节中指定的 “URL 和文件名安全字母”。 base64 编码的字符串中包含的空格字符（例如空格、制表符和换行）会被忽略。

-   'hex': 将每个字节编码成两个十六进制的字符。 当解码仅包含有效的十六进制字符的字符串时，可能会发生数据截断。 请参见下面的示例。

还支持以下传统的字符编码：

-   'ascii': 仅适用于 7 位 ASCII 数据。 当将字符串编码为 Buffer 时，这等效于使用 'latin1'。 当将 Buffer 解码为字符串时，则使用此编码会在解码为 'latin1' 之前额外取消设置每个字节的最高位。 通常，当在编码或解码纯 ASCII 文本时，应该没有理由使用这种编码，因为 'utf8'（或者，如果已知的数据始终为纯 ASCII，则为 'latin1'）会是更好的选择。 这仅为传统的兼容性而提供。

-   'binary': 'latin1' 的别名。 该编码的名称可能会引起误解，因为此处列出的所有编码都是在字符串和二进制数据之间转换。 对于在字符串和 Buffer 之间进行转换，通常 'utf-8' 是正确的选择。

-   'ucs2': 'utf16le' 的别名。 UCS-2 以前是指 UTF-16 的一种变体，该变体不支持代码点大于 U+FFFF 的字符。 在 Node.js 中，始终支持这些代码点。

#### Buffer 类

> Buffer 类是一个全局变量，用于直接处理二进制数据。 它可以使用多种方式构建。

##### Buffer.alloc(size,fill,encoding)

-   size <integer> 新 Buffer 的所需长度。

-   fill <string> | <Buffer> | <Uint8Array> | <integer> 用于预填充新 Buffer 的值。默认值: 0。

-   encoding <string> 如果 fill 是一个字符串，则这是它的字符编码。默认值: 'utf8'。

分配一个大小为 size 字节的新 Buffer。 如果 fill 为 undefined，则用零填充 Buffer。

如果 size 大于 buffer.constants.MAX_LENGTH 或小于 0，则抛出 ERR_INVALID_OPT_VALUE。

如果指定了 fill，则分配的 Buffer 通过调用 buf.fill(fill) 进行初始化。

如果同时指定了 fill 和 encoding，则分配的 Buffer 通过调用 buf.fill(fill, encoding) 进行初始化 。

如果 size 不是一个数字，则抛出 TypeError。

```javascript
const buf = Buffer.alloc(5);
console.log(buf);
// 打印: <Buffer 00 00 00 00 00>

const buf = Buffer.alloc(5, "a");
console.log(buf);
// 打印: <Buffer 61 61 61 61 61>

const buf = Buffer.alloc(11, "aGVsbG8gd29ybGQ=", "base64");
console.log(buf);
// 打印: <Buffer 68 65 6c 6c 6f 20 77 6f 72 6c 64>
```

##### Buffer.allocUnsafe(size)

-   size <integer> 新建的 Buffer 的长度。

创建一个大小为 size 字节的新 Buffer。 如果 size 大于 buffer.constants.MAX_LENGTH 或小于 0，则抛出 ERR_INVALID_OPT_VALUE。

以这种方式创建的 Buffer 实例的底层内存是未初始化的。 新创建的 Buffer 的内容是未知的，可能包含敏感数据。 使用 Buffer.alloc() 可以创建以零初始化的 Buffer 实例。

如果 size 不是一个数字，则抛出 TypeError。

Buffer 模块会预分配一个内部的大小为 Buffer.poolSize 的 Buffer 实例，作为快速分配的内存池，用于使用 Buffer.allocUnsafe() 创建新的 Buffer 实例、或 Buffer.from(array)、或弃用的 new Buffer(size) 构造器但仅当 size 小于或等于 Buffer.poolSize >> 1（Buffer.poolSize 除以二再向下取整）。

对这个预分配的内部内存池的使用是调用 Buffer.alloc(size, fill) 和 Buffer.allocUnsafe(size).fill(fill) 两者之间的关键区别。 具体来说， Buffer.alloc(size, fill) 永远不会使用内部的 Buffer 池，而 Buffer.allocUnsafe(size).fill(fill) 在 size 小于或等于 Buffer.poolSize 的一半时将会使用内部的 Buffer 池。 该差异虽然很微妙，但当应用程序需要 Buffer.allocUnsafe() 提供的额外性能时，则非常重要。
