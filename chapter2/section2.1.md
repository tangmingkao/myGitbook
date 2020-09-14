### Node 基本概念

#### Node 是什么？

Node.js 是一个基于 Chrome V8 引擎的 JavaScript 运行环境(runtime),Node 不是一门让 js 运行在后端的语言,并且不包括 javascript 全集,因为在服务端中不包含 DOM 和 BOM,Node 也提供了一些新的模块例如 http,fs 模块等。Node.js 使用了事件驱动、非阻塞式 I/O 的模型，使其轻量又高效并且 Node.js 的包管理器 npm，是全球最大的开源库生态系统。事件驱动与非阻塞 IO 后面我们会一一介绍。到此我们已经对 node 有了简单的概念。

#### Node 解决了什么问题？

Node 在处理高并发,I/O 密集场景有明显的性能优势

-   高并发,是指在同一时间并发访问服务器

-   I/O 密集指的是文件操作、网络操作、数据库,相对的有 CPU 密集,CPU 密集指的是逻辑处理运算、压缩、解压、加密、解密

> Web 主要场景就是接收客户端的请求读取静态资源和渲染界面,所以 Node 非常适合 Web 应用的开发。

#### V8 JavaScript 引擎

V8 是为 Google Chrome 提供支持的 JavaScript 引擎的名称。 当使用 Chrome 进行浏览时，它负责处理并执行 JavaScript。

V8 提供了执行 JavaScript 的运行时环境。 DOM 和其他 Web 平台 API 则由浏览器提供。

很酷的是，JavaScript 引擎独立于托管它的浏览器。 此关键的特性推动了 Node.js 的兴起。 V8 于 2009 年被选为为 Node.js 提供支持的引擎，并且随着 Node.js 的爆炸性发展，V8 成为了现在为大量的服务器端代码（使用 JavaScript 编写）提供支持的引擎。

Node.js 的生态系统非常庞大，得益于此，V8 还为桌面应用程序（通过 Electron 等项目）提供支持。

##### 其他 JS 引擎

其他浏览器也有自己的 JavaScript 引擎：

-   Firefox 具有 SpiderMonkey

-   Safari 具有 JavaScriptCore（又称为 Nitro）

-   Edge 具有 Chakra

还有很多其他的。

所有这些引擎都实现了 ECMA ES-262 标准（又称为 ECMAScript），这是 JavaScript 使用的标准。

##### 对性能的追求

V8 使用 C++ 编写，并且不断地被改进。 它是可移植的，且可运行于 Mac、Windows、Linux 和其他一些系统。

在此 V8 的介绍中，省略了 V8 的实现细节：可以去更具权威性的网站（例如 V8 官方网站）上查看。

与其他 JavaScript 引擎一样，V8 也在不断地发展以加速 Web 和 Node.js 的生态系统。

在 web 上，性能竞赛一直持续了很多年，作为用户和开发者从这场竞争中受益匪浅，因为年复一年地获得了更快、更优化的机器。

##### 编译

JavaScript 通常被认为是一门解释型的语言，但是现代的 JavaScript 引擎不再只是解释 JavaScript，也会对其进行编译。

这从 2009 年开始就一直在发生，当时 SpiderMonkey JavaScript 编译器被添加到 Firefox 3.5 中，所有人都跟进了这个想法。

JavaScript 是由 V8 在其内部编译的，使用了即时（JIT）编译以加快执行速度。

自 2004 年 Google 地图的引入以来，JavaScript 已经从一门通常执行几十行代码的语言演变为能在浏览器中运行具有成千上万行代码的完整的应用程序。

现在，应用程序可以在浏览器中运行数小时，而不仅仅是一些表单验证规则或简单的脚本。

在这个新世界中，编译 JavaScript 非常有意义，因为尽管可能需要多花费一点时间来为 JavaScript 做准备，但是一旦完成，则它会比纯解释型的代码具有更高的性能。
