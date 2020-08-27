#### Vue 面试题

##### 请说下对响应式数据的理解 ?

所谓响应式数据，是指当数据发生变化时，与该数据关联的视图及时更新。

Vue 是怎么做到这一点的呢？

Vue 通过递归调用 Object.defineProperty()重新定义 data 对象(如果是函数则转成对象)进行观测。Object.defineProperty()重新定义对象的一个属性时，
当属性取值时会触发属性的 getter 函数。当重新设置值时就会触发属性的 setter 函数。做到这一步就做到了对数据变化进行观测。

如何将对象中的数据跟视图中使用的该数据进行关联呢？

Vue 首先会在 Object.defineProperty 观测数据时生成一个依赖收集的 dep 对象，dep 对象会在触发数据的 get 函数时进行依赖收集，会将 watcher 放入 dep 对象的 subs 数组中。
在数据发生变化触发 set 函数时会触发 dep 实例的 notify 方法。notify 方法处理方法是。遍历触发依赖收集数组 subs 里的 wathcer 实例的 update 方法。而每个 wathcer 实例中
的 update 方法其实就是包裹了 Vue 实例的更新视图方法。这样就达到了数据变化触发视图更新。

> 会在生命周期的 mountComponent 方法中实例化 watcher，watcher 实例中会将更新视图的函数挂载在 getter 属性上，并在实例化对象时进行初次渲染。

(ps: 老师，感觉还是没有完全弄透这个步骤，没法用自己的话很清晰的解释)

###### 答案的参考思路

♥ 核心点考察的是：数组和对象类型当值变化时如何劫持到。对象内部通过 defineReactive 方法，使用 Object.defineProperty 将属性进行劫持（只会劫持已经存在的属性），数组则是通过重写数组方法来实现。 这里在回答时可以带出一些相关知识点（比如多层对象是通过递归来实现劫持，顺带提出 Vue3 中是使用 proxy 来实现响应式数据）

♥ 核心点答出来了也可以在进行补充回答，内部依赖收集是怎样做到的，每个属性都拥有自己的 dep 属性，存放他所依赖的 watcher，当属性变化后会通知自己对应的 watcher 去更新 （其实后面会讲到每个对象类型自己本身也拥有一个 dep 属性，这个在\$set 面试题中在进行讲解）

♥ 这里可以引出性能优化相关的内容 （1）对象层级过深，性能就会差 （2）不需要响应数据的内容不要放到 data 中 （3） Object.freeze() 可以冻结数据

##### vue 如何检测数组的变化？

vue 检测数组的变化有 2 个方面

1.  数组本身的数据项是通过遍历数组，递归调用 Object.defineProperty()方式实现的。数组的每一项如果是对象，就跟对象一样的方式监测。

2.  数组还要监测改变数组本身的方法。这中情况的监测是通过函数劫持的方法(或者说切片编程思想)。对其中 7 个改变数组本身的原生方法而实现。
    即： 改变数组本身的方法有 'push','pop','shift','unshift','reverse','sort','splice' 这 7 个方法。或者说 vue 只处理了这 7 个方法。
    处理的方式就是：首先通过 Object.create(Array.prototype) 创建一个 arrayMethods 对象。因为式通过数组原型方式创建的，所以该对象通过原型向上游查找，
    是可以找到数组的所有方法的。通过这样的方法就可以创建一个拥有数组所有方法的对象，同时又不污染 Array 对象本身。然后将 arrayMethods 挂载在观测的数组对象的\_\_proto\_\_原型上。
    这样 vue 实例中的数组调用 pop 等数组原生方法时实际上时走 arrayMethods 对象提供的方法。劫持'push','pop','shift','unshift','reverse','sort','splice'这 7 个方法内部实现是：
    首先通过 apply 调用原生数组本身的方法。然后调用视图更新方法。但是数据这 7 中方法中有 3 个函数需要特殊处理因为这三个函数又向数组增加的方法'push','unshift','splice'。可能增加的又是一个对象。这里处理就是通过将这 3 个函数增加的参数再进行观测一遍。跟初始化观测一样。遇到对象就递归观测对象。如果是数组又用观测数组的方法观测一遍。

> 为什么 vue 实例中的数组调用 pop 等数组原生的方法时，走的是 arrayMethods 提供的劫持后的方法呢？

> 因为已经将 arrayMethods 对象挂载在 data 对象的\_\_proto\_\_原型上了。所以 data 对象里的数组调用这些方法时会首先在自己的原型上查找，查找不到才会取 Array 对象上查找。

如何调用视图更新方法？

是因为在 Observer 类的时候，会挂载一个 dep 实例。dep 实例中有 notify 方法进行更新视图。(所以我认为其实对数组的有几个方法并没有观测变化,只是在调用这几个方法后直接更新了一次视图)。

> 为什么可在劫持函数的内部直接调用 Observer 实例的 observeArray 方法？

> Vue 在这里做了一个处理即： 在对某个对象进行观测时，首先会 new 一个 Observer 实例。在 Observer 的构造器会将 this 指向挂载在进行观测的对象的\_\_ob\_\_属性上。这样被观测的对象
> 的\_\_ob\_\_属性上指向的就是 Observer。而在劫持方法中的 this,就是调用该方法的对象。很明显，调用该方法的对象就是被观测的对象。这样被观测的对象就可以通过\_\_ob\_\_属性找到 observeArray 方法。

###### 答案的参考思路

♥ 核心点考察的是：数组考虑性能原因没有用 defineProperty 对数组的每一项进行拦截，而是选择重写数组（push,shift,pop,splice,unshift,sort,reverse）方法进行重写。

♥ 核心点答出来了也可以在进行补充回答，在 Vue 中修改数组的索引和长度是无法监控到的。需要通过以上 7 种变异方法修改数组才会触发数组对应的 watcher 进行更新。数组中如果是对象数据类型也会进行递归劫持。

♥ 引发出的问题，那如果想更改索引更新数据怎么办？可以通过 Vue.\$set()来进行处理 =》 核心内部用的是 splice 方法

##### Vue 中的模板编译原理？

模板编译的主要流程是将 html 模板转换成 render 函数。Vue 处理这个的逻辑是：

1. 先查看初始化 Vue 实例的时候是否提供 render 方法，如果提供了就直接用 render 方法。

2. 如果没有提供 render 方法，则查看是否提供模板 template.如果提供了，就将该模板字符串转化成 render 函数

3. 如果即没有提供 render 方法也没有提供 template。则查看是否提供了 el 属性。通过 el 属性查看 HTML 中查找到该元素。如果没查到则直接报错。
   如果查到了，就使用该元素的 outerHTML 获取的字符串作为模板字符串。

4. 获取到了模板字符串，则将该模板字符串通过一系列正则（开始标签，标签名，结束标签等正则）转化成抽象语法树 AST。在这一步利用了 2 中数据结构，栈和树。

5. 标记静态节点为 dom 操作优化做铺垫

6. 通过 AST 语法树生成 code 字符串。然后再利用 with 函数和 new Function 生成 render 函数。

7. 通过 render 函数生成 Vnode 再转化为真实 dom。

##### 生命周期钩子是如何实现的？

生命周期钩子函数的实现是：

1. 初始化的时候调用 mergeOptions 函数进行属性合并。利用策略模式进行不通的合并策略。

2. callHook 函数触发钩子函数。首先从实例的\$options 中获取到需要触发的钩子名称所对应的钩子函数数组 handlers，然后遍历该数组，将数组中的每个钩子函数都执行一遍。

##### 请说下 v-if 和 v-show 的区别

v-if 是“真正”的条件渲染，因为它会确保在切换过程中条件块内的事件监听器和子组件适当地被销毁和重建。
v-if 也是惰性的：如果在初始渲染时条件为假，则什么也不做——直到条件第一次变为真时，才会开始渲染条件块。
相比之下，v-show 就简单得多——不管初始条件是什么，元素总是会被渲染，并且只是简单地基于 CSS 进行切换。
一般来说，v-if 有更高的切换开销，而 v-show 有更高的初始渲染开销。因此，如果需要非常频繁地切换，则使用 v-show 较好；如果在运行时条件很少改变，则使用 v-if 较好。

> 注：最好不能将 v-if 和 v-for 一起使用，因为 v-for 具有更高的优先级。那么 v-if 将会重复运行到每个 v-for 的循环上，造成更高的开销。

##### Vue.use 是干什么的？原理是什么？

安装 Vue.js 插件。如果插件是一个对象，必须提供 install 方法。如果插件是一个函数，它会被作为 install 方法。install 方法调用时，会将 Vue 作为参数传入。该方法需要在调用 new Vue() 之前被调用。当 install 方法被同一个插件多次调用，插件将只会被安装一次。

原理：

1. 该变量初始值是一个空数组，用来存储已安装过的插件。首先判断传入的插件是否存在于 installedPlugins 数组中（即已被安装过），如果存在的话，则直接返回，防止重复安装。

2. 接下来获取到传入的其余参数，并且使用 toArray 方法将其转换成数组，同时将 Vue 插入到该数组的第一个位置，这是因为在后续调用 install 方法时，Vue 必须作为第一个参数传入。

3. 首先，判断传入的插件如果是一个提供了 install 方法的对象，那么就执行该对象中提供的 install 方法并传入参数完成插件安装。如果传入的插件是一个函数，那么就把这个函数当作 install 方法执行，同时传入参数完成插件安装。

4. 插件安装完成之后，将该插件添加进已安装插件列表 installedPlugins 中，防止重复安装。

##### Vue-router 有几种钩子函数？具体试什么以及执行流程是怎么样的？

钩子函数种类： 1. 全局前置守卫：beforeEach 。 2. 2.5.0+版本全局守卫： beforeResolve 。 3. 全局后置钩子：afterEach 。 4. 路由独享的守卫： beforeEnter。 5. 组件内： beforeRouteEnter，beforeRouteUpdate ，beforeRouteLeave 。

完整的导航解析流程：

1. 导航被触发
2. 在失活的组件里调用 beforeRouteLeave 守卫。
3. 调用全局的 beforeEach 守卫。
4. 在重用的组件里调用 beforeRouteUpdate 守卫 (2.2+)。
5. 在路由配置里调用 beforeEnter。
6. 解析异步路由组件。
7. 在被激活的组件里调用 beforeRouteEnter。
8. 调用全局的 beforeResolve 守卫 (2.5+)。
9. 导航被确认。
10. 调用全局的 afterEach 钩子。
11. 触发 DOM 更新。
12. 调用 beforeRouteEnter 守卫中传给 next 的回调函数，创建好的组件实例会作为回调函数的参数传入。

##### Vue-router 的 2 种模式的区别？

vue-router 有 2 种模式，分别是 hash 模式和 history 模式。 hash 模式是使用使用 URL hash 值来作路由。支持所有浏览器。history 模式依赖 HTML5 History API 和服务器配置。hash 模式 url 带有"#",history 模式不带"#"号。history 模式需要后端支持。例如 nginx 的配置：

```javascript
location / {
  try_files $uri $uri/ /index.html;
}
```

##### Vue 函数式组件的优势及原理？

函数式组件的定义：

1. Stateless(无状态)：组件自身是没有状态的
2. Instanceless(无实例)：组件自身没有实例，也就是没有 this

优势：

基于函数式组件的特点，所以它比一般的组件具有更快的渲染，更低的渲染销低。更加灵活，可以利用函数式组件包装复杂组件。

原理：

组件在 Vue 实例化时会先执行 createComponent()函数，在该函数内执行 extractPropsFromVNodeData(data, Ctor, tag)从组件的基础构造器上获取到 props 信息后就会判断 options.functional 是否为 true,如果为 true 则执行 createFunctionalComponent 函数。

##### v-if 和 v-for 的优先级？

v-for 具有更高的优先级。所以如果 v-if 和 v-for 一起使用， v-if 将会重复运行到每个 v-for 的循环上，造成更高的开销。

##### 组件中写 name 选项有那些好处及作用？

1. 允许组件模板递归地调用自身。
2. 便于调试,有名字的组件有更友好的警告信息。另外，当在有 vue-devtools，未命名组件将显示成 <AnonymousComponent>，这很没有语义。通过提供 name 选项，可以获得更有语义信息的组件树。

##### Vue 事件修饰符有哪些？其实现原理是什么？

| 事件装饰符                  | 按键修饰符       | 系统修饰键 | 鼠标按钮修饰符 |
| --------------------------- | ---------------- | ---------- | -------------- |
| .stop 阻止单击事件继续传播  | .enter           | .ctrl      | .left          |
| .prevent 阻止默认事件       | .page-down       | .alt       | .right         |
| .capture 使用事件捕获模式   | .tab             | .shift     | .middle        |
| .self 只在自己身上触发      | .space           | .meta      |                |
| .once 只执行一次            | .right           | .exact     |                |
| .passive 滚动事件的默认行为 | 等等。包括按键码 | 等等       |                |

原理:

###### 建立一个映射表

```javascript
// keyCode aliases
const modifierCode = {
	stop: "$event.stopPropagation();",
	prevent: "$event.preventDefault();",
	self: genGuard(`$event.target !== $event.currentTarget`),
	ctrl: genGuard(`!$event.ctrlKey`),
	shift: genGuard(`!$event.shiftKey`),
	alt: genGuard(`!$event.altKey`),
	meta: genGuard(`!$event.metaKey`),
	left: genGuard(`'button' in $event && $event.button !== 0`),
	middle: genGuard(`'button' in $event && $event.button !== 1`),
	right: genGuard(`'button' in $event && $event.button !== 2`),
};
```

```javascript
// keyCode aliases
const keyCodes = {
	esc: 27,
	tab: 9,
	enter: 13,
	space: 32,
	up: 38,
	left: 37,
	right: 39,
	down: 40,
	delete: [8, 46],
};
```

然后找到对应的处理函数 genHandler 函数

```javascript
if (!handler.modifiers) {
    return isMethodPath || isFunctionExpression
      ? handler.value
      : `function($event){${handler.value}}` // inline statement
} else {
    let code = ''
    let genModifierCode = ''
    const keys = []
} else {
  //等等。。。
}
```

##### Vue.directive 的源码实现

##### 如何理解自定义指令

##### 谈一下你对 Vuex 的个人理解

Vuex 是一个专为 Vue.js 应用程序开发的状态管理模式。它采用集中式存储管理应用的所有组件的状态，并以相应的规则保证状态以一种可预测的方式发生变化。他解决的问题是 vue 中状态的管理。它的核心模块有 State，Getter,Mutation,Action,Module.

1. State 单一状态,作为 vuex 中的一个“唯一数据源 ”。
2. Getter 需要从 store 中的 state 中派生出一些状态。就像计算属性一样，getter 的返回值会根据它的依赖被缓存起来，且只有当它的依赖值发生了改变才会被重新计算。
3. 更改 Vuex 的 store 中的状态的唯一方法是提交 mutation。Vuex 中的 mutation 非常类似于事件：每个 mutation 都有一个字符串的 事件类型 (type) 和 一个 回调函数 (handler)。
    > mutation 必须是同步操作
4. (1)Action 提交的是 mutation，而不是直接变更状态。(2)Action 可以包含任意异步操作。
5. Module 由于使用单一状态树，应用的所有状态会集中到一个比较大的对象。当应用变得非常复杂时，store 对象就有可能变得相当臃肿。为了解决以上问题，Vuex 允许我们将 store 分割成模块（module）。

##### Vue 中 slot 是如何实现的？什么时候使用它？

1. 编译： 当解析到标签上有 slot 属性的时候，会给对应的 AST 元素节点添加 slotTarget 属性，如果是普通插槽，就直接调用函数生成 vnode，如果是作用域插槽，就直接带着 props 去调用函数生成 vnode。
2. 更新： 生产 render 函数，然后就是 vue 的更新原则。触发 update 去更新视图。

使用情形： 在开发组件库或者组件的时候，为了让组件更加灵活可定制，经常用插槽的方式让用户可以自定义内容。

##### 如何优化单页面首屏加载白屏体验问题？

1. 开启 gzip 压缩.(配置就可以)。或者开启 br(brotli)压缩。开启 br 压缩需要在服务端下载一下包。不如 gzip 压缩方便，直接配置 nginx 就可以了。但是 br 压缩具有更高的压缩率。大致比 gzip 压缩提升 20%左右。
2. 一些第三方资源走 cdn,图片也可以走 cdn。
3. 首页骨架屏或者 loading。(仅仅是优化体验)
4. 图片懒加载
5. 服务端渲染 ssr 技术
6. code-split 代码分割。
7. 路由懒加载

##### vue3.0 在响应式方面对 vue2.0 的主要优化点在哪里？

Vue2 中的变化侦测实现对 Object 及 Array 分别进行了不同的处理，Objcet 使用了 Object.defineProperty API ，Array 使用了拦截器对 Array 原型上的能够改变数据的方法进行拦截。虽然也实现了数据的变化侦测，但存在很多局限 ，比如对象新增属性无法被侦测，以及通过数组下边修改数组内容，也因此在 Vue2 中经常会使用到 \$set 这个方法对数据修改，以保证依赖更新。

Vue3 中使用了 es6 的 Proxy API 对数据代理，没有像 Vue2 中对原数据进行修改，只是加了代理包装，因此首先性能上会有所改善。其次解决了 Vue2 中变化侦测的局限性，可以不使用\$set 新增的对象属性及通过下标修改数组都能被侦测到。

##### .aync 修饰符的作用，用法以及实现原理

作用： 子组件更新父组件数据。是 update:myPropName 的模式触发事件的一种简写。

用法：

```javascript
//父组件
<text-document v-bind:title.sync="doc.title"></text-document>
//子组件
this.$emit('update:title', newTitle)
```

原理：

在 parse 的时候在 processAttrs 函数中循环 attrsList 的时候判断如果有 modifiers.sync 存在，则增加一个 handle。名字加上约定好的"updata:"。这样在子组件触发 emit 的时候就会触发这个 handle 的回调更改父组件里的属性值。

##### 谈一谈你对 Vue 性能优化的理解

1. 对路由组件进行懒加载
2. 扁平化data中的属性。因为vue观测数据是通过递归去观测的。层级越高就需要递归更多次。
3. 对一些不是经常更改状态的元素采用v-if代替v-show。减少dom。
4. 对高频更新的数据采用批量更新。例如一些高频的websocket推送数据，采用缓存列表批量更新。
5. 为列表数据添加key值。key值最好用唯一id而不是index。以便更快的定位diff。用index的话，如果向列表头部添加一个数据，会导致整个列表index变化。
6. 减少watch的数据。当watch的数据比较小，性能消耗不明显。当数据变大，系统会出现卡顿。
7. 内容类系统的图片资源按需加载，图片加载比较多的时候可以用懒加载。
8. tab切换可以利用keep-alive缓存组件
9. 打包优化，gzip压缩，代码分割。第三方插件走cdn等。
10. 