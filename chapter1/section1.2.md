### Promise A+ 规范 (Promises/A+)

###### 一个开放、健全且通用的 JavaScript Promise 标准。由开发者制定，供开发者参考。

Promise 表示异步操作的最终结果。与之交互的主要方法是 then 方法。then 方法注册了两个回调函数，用于接收 promise 的终值或者本 promise 不能执行的原因。

该规范详细说明了 then 方法的行为，提供了一个可互操作的基础，所有 Promises / A +符合诺言的实现都可以依靠该基础来提供。 因此，该规范应被视为非常稳定。 尽管 Promises / A +组织有时会通过向后兼容的微小更改来修订此规范，以解决新发现的极端情况，但只有经过仔细考虑，讨论和测试之后，我们才会集成大型或向后不兼容的更改。

从历史上看，Promises / A +阐明了早期 Promises / A 提案的行为条款，一方面将其扩展为涵盖实际行为，一方面删减了原规范的一些特例情况和有问题的部分。

最后，核心的 Promises/A+ 规范不设计如何创建、解决和拒绝 promise，而是专注于提供一个通用的 then 方法。上述对于 promises 的操作方法将来在其他规范中可能会提及。

#### 1. 术语

-   1.1 "promise" 是具有 then 方法的对象或函数，其行为符合此规范。
-   1.2 "thenable" 是定义 then 方法的对象或函数。
-   1.3 "value" 是任何合法的 JavaScript 值（包括未定义，thenable 或 promise）。
-   1.4 "exception" 是使用 throw 语句抛出的错误。
-   1.5 "reason" 是 promise 拒绝的原因。

#### 2. 要求

##### 2.1 promises 的状态

promise 必须处于以下三种状态之一：pending , fulfilled, rejected.

2.1.1 pending 状态

-   2.1.1.1 pending 状态可能转换为 fulfilled 状态 或者 rejected 状态。

2.1.2 fulfilled 状态

-   2.1.2.1 fulfilled 状态即不能变成 pending 状态也不能变成 rejected 状态
-   2.1.2.2 必须有一个不可改变的最终值

2.1.3 rejected 状态

-   2.1.3.1 rejected 状态即不能变成 pending 状态也不能变成 fulfilled 状态
-   2.1.3.2 必须有一个不可改变的最终值(拒绝的原因)

> 这里的不可变指的是恒等（即可用 === 判断相等），而不是意味着更深层次的不可变（指当 value 或 reason 不是基本值时，只要求其引用地址相等，但属性值可被更改）。

##### 2.2 then 方法

promise 必须提供一个 then 方法来访问其当前或者最终值或者拒绝的原因。

promise 的 then 方法接收两个参数

```javascript
promise.then(onFulfilled, onRejected);
```

2.2.1 onFulfilled 和 onRejected 都是可选参数：

-   2.2.1.1 如果 onFulfilled 不是函数，则必须将其忽略。
-   2.2.1.2 如果 onRejected 不是函数，则必须将其忽略。

2.2.2 如果 onFulfilled 是一个函数：

-   2.2.2.1 必须当 promise 的状态为 fulfilled 时才调用。第一个参数时 promise 执行的终值。
-   2.2.2.2 不能在 promise 的状态为 fulfilled 之前调用
-   2.2.2.3 不能多次调用，只能调用一次

2.2.3 如果 onRejected 是一个函数：

-   2.2.3.1 必须当 promise 的状态为 rejected 时才调用。第一个参数时 promise 拒绝的原因。
-   2.2.3.2 不能在 promise 的状态为 rejected 之前调用
-   2.2.3.3 不能多次调用，只能调用一次

2.2.4 在执行上下文堆栈仅包含平台代码之前，不得调用 onFulfilled 或 onRejected。 [注释 3.1]。

2.2.5 onFulfilled 和 onRejected 必须作为函数调用（即没有此值）。 [注释 3.2]

2.2.6 then 可能会在统一个 promise 中多次调用

#### 3. 注释

-   3.1 这里的“平台代码”是指引擎，环境和承诺实现代码。 实际上，此要求可确保在调用事件循环之后，使用新堆栈异步执行 onFulfilled 和 onRejected。 可以使用“宏任务”机制（如 setTimeout 或 setImmediate）或“微任务”机制（如 MutationObserver 或 process.nextTick）来实现。 由于 promise 实现被视为平台代码，因此它本身可能包含一个任务调度队列或“蹦床”，在其中调用处理程序。

-   3.2 也就是说，在严格模式下，在内部 this 的值是 undefined 。在非严格模式下，它将是全局对象。

-   3.3 代码实现在满足所有要求的情况下可以允许 promise2 === promise1 。每个实现都要文档说明其是否允许以及在何种条件下允许 promise2 === promise1 。

-   3.4 通常来说，如果 x 符合当前实现，我们才认为它是真正的 promise 。这一规则允许那些特例实现接受符合已知要求的 Promises 状态。

-   3.5 首先存储对 x.then 的引用，然后测试该引用，然后调用该引用的过程避免了对 x.then 属性的多次访问。 此类预防措施对于确保访问者属性的一致性非常重要，因为访问者属性的值在两次检索之间可能会发生变化。

-   3.6 实现不应该对 thenable 链的深度设限，并假定超出本限制的递归就是无限循环。只有真正的循环递归才应能导致 TypeError 异常；如果一条无限长的链上 thenable 均不相同，那么递归下去永远是正确的行为。
