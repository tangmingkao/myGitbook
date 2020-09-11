#### 浏览器的进程

-   每个页卡都是进程 (互不影响)

-   浏览器也有一个主进程(用户界面)

-   渲染进程 每个页卡里都有一个渲染进程(浏览器内核)

-   网络进程 (处理请求)

-   GPU 进程 3d 绘制

-   第三方插件的进程

#### 渲染进程（包含着多个线程）

-   GUI 渲染线程 (渲染页面的)

-   js 引擎线程 他和页面渲染时互斥

-   事件触发线程 独立的线程 EventLoop

-   事件 click 、 setTimeout 、 ajax 也是一个独立的线程

![PNG](../../../images/event_loop.jpg)

#### 宏任务、微任务

在 JavaScript 中，任务被分为两种，一种宏任务 (MacroTask) 也叫 Task,一种叫做微任务(MicroTask)

-   宏任务 宿主环境提供的异步方法 都是宏任务 script ui 渲染 (script 全部代码、setTimeout、setInterval、setImmediate)、I/O、UI Rendering

-   微任务 语言标准提供 promise mutationObserver Process.nextTick

#### 浏览器中得 Event Loop

Javascript 有一个 main thread 主线程和 call-stack 调用栈（执行栈），所有得任务都会被放到调用栈等待主线程执行

#### 同步任务和异步任务

JavaScript 单线程任务被分别分为同步任务和异步任务，同步任务会在调用栈中按照顺序等待主线程一次执行，异步任务会在异步任务有了结果后，将注册得回调函数 放入任务队列中等待主线程空闲得时候（调用栈被清空），被读取到栈内等待主线程执行。

![PNG](../../../images/event_loop1.jpg)

任务队列 Task Queue，即队列，是一种先进先出的一种数据结构。

![PNG](../../../images/event_loop2.jpg)

#### 事件循环得进程模型

-   选择当前要执行得任务队列，选择任务队列中最先进入得任务，如果任务队列为空即 null，则执行跳转到微任务(MicroTask)的执行步骤。
-   将事件循环中的任务设置为已选择任务
-   执行任务
-   将事件循环中当前运行任务设置为 null。
-   将已经运行完成的任务从任务队列中删除
-   microtasks 步骤： 进入 microtask 检查点。
-   更新界面渲染
-   返回第一步

执行进入 microtask 检查点时，用户代理会执行以下步骤：

-   设置 microtask 检查点标志为 true。
-   当事件循环 microtask 执行不为空时：选择一个最先进入的 microtask 队列的 microtask，将事件循环的 microtask 设置为已选择的 microtask，运行 microtask，将已经执行完成的 microtask 为 null，移出 microtask 中的 microtask。
-   清理 IndexDB 事务
-   设置进入 microtask 检查点的标志为 false。

执行栈在执行完同步任务后，查看执行栈是否为空，如果执行栈为空，就会去执行 Task（宏任务），每次宏任务执行完毕后，检查微任务(microTask)队列是否为空，如果不为空的话，会按照先入先出的规则全部执行完微任务(microTask)后，设置微任务(microTask)队列为 null，然后再执行宏任务，如此循环。

#### NodeJs 的 EventLoop

![PNG](../../../images/event_loop_node.jpg)

Node 中的 Event Loop 是基于 libuv 实现的，而 libuv 是 Node 的新跨平台抽象层，libuv 使用异步，事件驱动的编程方式，核心是提供 i/o 的事件循环和异步回调。libuv 的 API 包含有时间，非阻塞的网络，异步文件操作，子进程等等。 Event Loop 就是在 libuv 中实现的。

![PNG](../../../images/libuv.jpg)

Node 的 Event loop 一共分为 6 个阶段，每个细节具体如下：

-   timers: 执行 setTimeout 和 setInterval 中到期的 callback。
-   pending callback: 上一轮循环中少数的 callback 会放在这一阶段执行。
-   idle, prepare: 仅在内部使用。
-   poll: 最重要的阶段，执行 pending callback，在适当的情况下回阻塞在这个阶段。
-   check: 执行 setImmediate(setImmediate()是将事件插入到事件队列尾部，主线程和事件队列的函数执行完成之后立即执行 setImmediate 指定的回调函数)的 callback。
-   close callbacks: 执行 close 事件的 callback，例如 socket.on('close'[,fn])或者 http.server.on('close, fn)。


#### 