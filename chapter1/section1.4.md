## 前端代码规范文档

### git 开发流程，分支管理，提交合并请求

##### PC 代码分支结构

| 分支             | 描述                 |
| ---------------- | -------------------- |
| dev              | 测试环境分之代码     |
| master           | 前端生产环境分支代码 |
| prod             | 国内生产代码分支     |
| prodwh           | 海外生产代码分支     |
| 个人需求代码分支 | 个人各个版本需求分支 |

##### 代码开发流程

-   master 分支是提交到正式环境的分支，一般情况不要在 master 分支上开发。保持 master 分支上的代码与 production 分支上的代码统一。

-   有新的需求，从 master 分支上 checkout \-b 一条新的需求分支。例如 A 需求，分支名称就叫 A。然后在该分支上开发。达到提测要求再合并到 dev 分支进行测试。需要每天进行同步 master 的代码到自己的开发分支。因为可能上了新的需求。避免后期合并上线的时候有冲突。

*   线上紧急 bug 修复，从 master 分支切出一条 hotfix 分支进行紧急 bug 修复。修复完成后删除本地和远程该分支。

*   代码每次提交测试之前，根据以前划分模块人员每两个结对进行代码 review。

*   大的版本完成后最好打个 tag。一般可以以 v.1.2.0 之类的命名。

*   git 提交代码，commit 的时候需要记录总结的信息。提交的内容是什么。

> 注意： 正式分支中 prod 分支和 prodwh 分支的配置文件有所不同。静态资源的域名不一样。

##### git 常用命令

除了使用 git 客服端意外，例如 sourceTree 之类的客服端外。直接使用 git 命令的。以下列一些常见 git 命令。

```javascript
1. git branch -r //查看所有远端分支
2. git branch //查看所有本地分支
3. git push origin --delete shangjin //删除远端分支 shangjin
4. git status //查看本地状态
5. git checkout -b develop //创建本地分支 develop
6. git checkout -- 'src/views/auction/auction_y.vue'  //恢复本地 acction_y.vue 文件
7. git add . //添加所有修改文件到缓存区
8. git commit -m 'update' //提交代码，说明为‘update’
9. git pull origin master //从远端 master 分支拉取代码
10. git push origin master //将本地代码推送远端 master.
11. git checkout develop //切换分支到 develop 上。
12. git merge master //将 master 分支得内容同步到 develop 分支上。 //当前处于 develop 分支上
13. git merge develop //将 develop 分支上的内容合并到 master 分支上。 //当前处于 master 分支上。
14. git remote -v/--verbose //显示出详细的 url 地址名和对应的别名.
15. git remote add name url //添加远程仓库，一旦添加以后别名就代表远程库的版本库地址，可以直接使用。//git remote add origin https://github.com/rylink/qland-frontend.git
16. git remote remove origin //删除远端 origin 库
17. git checkout -b dev origin/dev //在本地创建 dev 分支，并和远端 dev 分支关联
```

---

#### 基本标准

永远遵循同一套编码规范,尽量做到每一行代码都像是同一个人编写的。

#### 项目命名

项目名全部采用小写方式，以中划线分隔，禁止驼峰式命名。比如：my-project-name。

#### 文件命名

-   文件命名参照项目命名规则。比如: error-report.html。

-   有复数结构时，要采用复数命名法，比如： scripts, styles, images, data-models。

-   文件名中只可由小写英文字母 az 、排序数字 09 或间隔符 - 组成，禁止包含特殊符号，比如空格、\$ 等。

-   为了醒目，某些说明文件的文件名，可以使用大写字母，比如: README, LICENSE。

-   为更好的表达语义，文件名使用英文名词命名，或英文简写。

-   不允许命名带有广告等英文的单词，例如 ad,adv,adver,advertising，防止该模块被浏览器当成垃圾广告过滤掉。任何文件的命名均如此。

文件常用命名

-   index.shtml 引导页&首页

-   main.shtml 首页

-   download.shtml 下载页面

-   act.html 活动列表页面

-   video.html 视频

-   cdkey.html CDKEY 页面

-   base.css 基本样式

-   layout.css 框架布局

-   module.css 模块样式

-   global.css 全局样式

-   font.css 字体样式

-   index.css 首页样式

-   link.css 链接样式

-   print.css 打印样式

---

### HTML 规范

#### 语法

使用四个空格的缩进，这是保证代码在各种环境下显示一致的唯一方式。

嵌套的节点应该缩进（四个空格）。

在属性上，使用双引号，不要使用单引号。

不要在自动闭合标签结尾处使用斜线 \-HTML5 规范 指出他们是可选的。

```javascript
//不要忽略可选的关闭标签。例如， </li> 和 </body>。
```

#### HTML5 doctype

在每个 HTML 页面开头使用这个简单地 doctype 来启用标准模式，使其每个浏览器中尽可能一致的展现。

虽然 doctype 不区分大小写，但是按照惯例，doctype 大写

```javascript
<!DOCTYPE html>
```

#### 语言属性

```javascript
<html lang="en"></html>
```

#### 字符编码

通过明确声明字符编码，能够确保浏览器快速并容易的判断页面内容的渲染方式。这样 做的好处是，可以避免在 HTML 中使用字符实体标记（character entity），从而全部与 文档编码一致（一般采用 UTF-8 编码）。

```javascript
<meta charset="UTF-8">
```

#### IE 兼容模式

IE 支持通过特定的 标签来确定绘制当前页面所应该采用的 IE 版本。除非有强烈 的特殊需求，否则最好是设置为 edge mode，从而通知 IE 采用其所支持的最新的模 式。

```javascript
<meta http-equiv="X-UA-Compatible" content="IE=Edge">
```

#### 响应式

```javascript
<meta name="viewport" content="width=device-width, initial-scale=1">
```

#### 引入 CSS 和 JavaScript

根据 HTML5 规范, 通常在引入 CSS 和 JavaScript 时不需要指明 type，因为 text/css 和 text/javascript 分别是他们的默认值。

```javascript
<!-- External CSS -->
<link rel="stylesheet" href="code-guide.css">

<!-- In-document CSS -->
<style>
    /* ... */
</style>

<!-- JavaScript -->
<script src="code-guide.js"></script>
```

#### 实用高于完美

尽量遵循 HTML 标准和语义，但是不应该以浪费实用性作为代价。任何时候都要用尽量小的复杂度和尽量少的标签来解决问题。

#### 减少标签数量

在编写 HTML 代码时，需要尽量避免多余的父节点。很多时候，需要通过迭代和重构来使 HTML 变得更少。 参考下面的示例:

```javascript
<!-- 不好的示范 -->
<span class="avatar">
    <img src="...">
</span>

<!-- 好的示范 -->
<img class="avatar" src="...">

```

#### 属性顺序

HTML 属性应该按照特定的顺序出现以保证易读性。

1. class
2. id
3. name
4. data-\*
5. src, for, type, href, value , max-length, max, min, pattern
6. placeholder, title, alt
7. aria-\*, role
8. required, readonly, disabled

class 是为高可复用组件设计的，理论上他们应处在第一位。id 更加具体而且应该尽量少使用（例如, 页内书签），所以他们处在第二位。

#### Boolean 属性

Boolean 属性指不需要声明取值的属性。XHTML 需要每个属性声明取值，但是 HTML5 并不需要。

一个元素中 Boolean 属性的存在表示取值 true，不存在则表示取值 false。

简而言之，不要为 Boolean 属性添加取值。

```javascript
<input type="text" disabled>
```

#### JavaScript 生成标签

在 JavaScript 文件中生成标签让内容变得更难查找，更难编辑，性能更差。应该尽量避免这种情况的出现。

---

### JS 规范

1. 使用 4 个空格进行缩进。
2. 声明之后以分号结束，不要省略。
3. 完全避免 == \!= 的使用， 用严格比较条件 === \!==。
4. eval 函数 非特殊情况， 禁用\!\!\!。
5. with 函数 非特殊情况， 禁用\!\!\!。
6. 单行长度，理论上不要超过 80 列，不过如果编辑器开启"自动换行"的话可以不考虑单行长度。
7. 接上一条，如果需要换行，存在操作符的情况，一定在操作符后换行，然后换的行缩进 4 个空格。

```javascript
if (typeof qqfind === "undefined" || typeof qqfind.cdnrejected === "undefined" || qqfind.cdnrejected !== true) {
	url = "http://pub.idqqimg.com/qqfind/js/location4.js";
} else {
	url = "http://find.qq.com/js/location4.js";
}
```

#### 空行

-   方法之间加

-   单行或多行注释前加

-   逻辑块之间加空行增加可读性

#### 变量命名

-   标准变量采用驼峰标识

-   使用的 ID 的地方一定全大写

-   使用的 URL 的地方一定全大写, 比如说 reportURL

-   涉及 Android 的，一律大写第一个字母

-   涉及 iOS 的，一律小写第一个，大写后两个字母

-   常量采用大写字母，下划线连接的方式

-   构造函数，大写第一个字母

```javascript
var thisIsMyName;

var goodID;

var AndroidVersion;

var iOSVersion;

var MAX_COUNT = 10;

function Person(name) {
	this.name = name;
}
```

#### 字符常量

统一使用双引号

#### null 的使用场景

1. 初始化可能以后分配对象值的变量

2. 与一个可能或可能没有对象值的初始化变量进行比较

3. 传入一个预期对象的函数

4. 从预期对象的函数返回

#### 不适合 null 的使用场景

1. 不要使用 null 来测试是否提供参数

2. 不要测试值为 null 的未初始化变量

#### undefined 使用场景

永远不要直接使用 undefined 进行变量判断。

使用字符串 "undefined" 对变量进行判断。

```javascript
// 不好的示范
var person;
console.log(person === undefined); //true
// 好的示范
console.log(typeof person); // "undefined"
```

#### 对象字面量

```javascript
// 不好的示范
var team = new Team();
team.title = "AlloyTeam";
team.count = 25;
// 好的示范
var team = {
	title: "AlloyTeam",
	count: 25,
};
```

#### 数组声明

```javascript
// 不好的示范
var colors = new Array("red", "green", "blue");
var numbers = new Array(1, 2, 3, 4);
// 好的示范
var colors = ["red", "green", "blue"];
var numbers = [1, 2, 3, 4];
```

#### 单行注释

双斜线后，必须跟注释内容保留一个空格

与下一行代码缩进保持一致

可位于一个代码行的末尾

```javascript
// 好的示范
if (condition) {
	// if you made it here, then all security checks passed
	allowed();
}
var zhangsan = "zhangsan"; // 双斜线距离分号四个空格，双斜线后始终保留一个空格
```

#### 多行注释格式

那些场景需要多行注视

1. 难于理解的代码段

2. 可能存在错误的代码段

3. 浏览器特殊的 HACK 代码

4. 业务逻辑强相关的代码

5. 想表示产品逻辑, 合作同事

多行注释采用 jsDoc 的风格进行注释。一般包含三部分，1. 方法名称或者构造函数名称；2. 参数部分，可能多个参数；3. 函数返回。（也可以在前面加上说明）

```javascript
/**
 * here boy, look here , here is girl
 * @method lookGril 方法名称
 * @param {Object} balabalabala 参数
 * @return {Object} balabalabala 返回
 */
```

#### 括号对齐

标准示例 括号前后有空格，花括号起始不另换行，结尾新起一行

花括号必须要，即使内容只有一行

涉及 if for while do...while try...catch...finally 的地方都必须使用花括号，即使内容只有一行

##### if else 前后留有空格

```javascript
if (condition) {
	doSomething();
} else {
	doSomethingElse();
}
```

##### switch

switch 和括号之间有空格，case 需要缩进，break 之后跟下一个 case 中间留一个空白行

花括号必须要， 即使内容只有一行。

switch 的 falling through 一定要有注释特别说明，no default 的情况也需要注释特别说明况

```javascript
switch (condition) {
	case "first":
		// code
		break;
	case "second":
		// code
		break;
	default:
	// code
}
```

##### for

普通 for 循环, 分号后留有一个空格， 判断条件等内的操作符两边不留空格

前置条件如果有多个，逗号后留一个空格

for-in 一定要有 hasOwnProperty 的判断， 否则 JSLint 或者 JSHint 都会有一个 warn

```javascript
for (i = 0, len = values.length; i < len; i++) {
	process(values[i]);
}
var prop;
for (prop in object) {
	// 注意这里一定要有 hasOwnProperty 的判断， 否则 JSLint 或者 JSHint 都会有一个 warn ！
	if (object.hasOwnProperty(prop)) {
		console.log("Property name is " + prop);
		console.log("Property value is " + object[prop]);
	}
}
```

##### 变量声明 函数声明

一定先声明再使用， 不要利用 JavaScript engine 的变量提升特性, 违反了这个规则 JSLint 和 JSHint 都会报 warn.

function declaration 和 function expression 的不同，function expression 的（）前后必须有空格，而 function declaration 在有函数名的时候不需要空格，没有函数名的时候需要空格。

函数调用括号前后不需要空格

立即执行函数的写法, 最外层必须包一层括号

"use strict" 决不允许全局使用， 必须放在函数的第一行， 可以用自执行函数包含大的代码段, 如果 "use strict" 在函数外使用， JSLint 和 JSHint 均会报错

```javascript
function doSomething(item) {
	// do something
}
var doSomething = function (item) {
	// do something
};
// 好的示范
doSomething(item);
// 不好的示范
// Bad: Looks like a block statement
doSomething(item);
// 好的示范
(function () {
	"use strict";
	function doSomething() {
		// code
	}
})();
```

---

### CSS 规范

#### 语法

1. 使用四个空格的缩进。

2. 使用组合选择器时，保持每个独立的选择器占用一行。

3. 为了代码的易读性，在每个声明的左括号前增加一个空格。

4. 声明块的右括号应该另起一行。

5. 每条声明 : 后应该插入一个空格。

6. 每条声明应该只占用一行来保证错误报告更加准确。

7. 所有声明应该以分号结尾。虽然最后一条声明后的分号是可选的，但是如果没有他，你的代码会更容易出错。

8. 逗号分隔的取值，都应该在逗号之后增加一个空格。

9. 不要在颜色值 rgb() rgba() hsl() hsla()和 rect() 中增加空格，并且不要带有取值前面不必要的 0 (比如，使用 .5 替代 0.5)。

10. 所有的十六进制值都应该使用小写字母，例如 #fff。因为小写字母有更多样的外形，在浏览文档时，他们能够更轻松的被区分开来。

11. 尽可能使用短的十六进制数值，例如使用 #fff 替代 #ffffff。

12. 为选择器中的属性取值添加引号，例如 input[type="text"]。 他们只在某些情况下可有可无，所以都使用引号可以增加一致性。

13. 不要为 0 指明单位，比如使用 margin: 0; 而不是 margin: 0px;。

```javascript
/* Bad CSS */
.selector, .selector-secondary, .selector[type=text] {
    margin: 0px 0px 15px;
    background-color: rgba(0, 0, 0, 0.5);
    box-shadow: 0 1px 2px #CCC, inset 0 1px 0 #FFFFFF
}
/* Good CSS */
.selector,
.selector-secondary,
.selector[type="text"] {
    margin-bottom: 15px;
    background-color: rgba(0,0,0,.5);
    box-shadow: 0 1px 2px #ccc, inset 0 1px 0 #fff;
}
```

#### 声明顺序

相关的属性声明应该以下面的顺序分组处理：

1. Positioning
2. Box model 盒模型
3. Typographic 排版
4. Visual 外观
5. Positioning 处在第一位，因为他可以使一个元素脱离正常文本流，并且覆盖盒模型相关的样式。盒模型紧跟其后，因为他决定了一个组件的大小和位置。

其他属性只在组件内部起作用或者不会对前面两种情况的结果产生影响，所以他们排在后面。

```javascript
.declaration-order {
    /* Positioning */
    position: absolute;
    top: 0;
    right: 0;
    bottom: 0;
    left: 0;
    z-index: 100;
    /* Box-model */
    display: block;
    float: right;
    width: 100px;
    height: 100px;
    /* Typography */
    font: normal 13px "Helvetica Neue", sans-serif;
    line-height: 1.5;
    color: #333;
    text-align: center;
    /* Visual */
    background-color: #f5f5f5;
    border: 1px solid #e5e5e5;
    border-radius: 3px;
    /* Misc */
    opacity: 1;
}
```

#### Don't use @import

与<link>相比，@import 较慢，增加额外的页面请求，并可能导致其他不可预见的问题。

```javascript
<!-- Use link elements -->
<link rel="stylesheet" href="core.css">
<!-- Avoid @imports -->
<style>
    @import url("more.css");
</style>
```

#### 媒体查询位置

尽量将媒体查询的位置靠近他们相关的规则。不要将他们一起放到一个独立的样式文件中，或者丢在文档的最底部。这样做只会让大家以后更容易忘记他们。这里是一个典型的案例。

```javascript
.element { ... }
.element-avatar { ... }
.element-selected { ... }
@media (min-width: 480px) {
    .element { ...}
    .element-avatar { ... }
    .element-selected { ... }
}
```

#### 前缀属性

当使用厂商前缀属性时，通过缩进使取值垂直对齐以便多行编辑。

```javascript
/* Prefixed properties */
.selector {
    -webkit-box-shadow: 0 1px 2px rgba(0,0,0,.15);
            box-shadow: 0 1px 2px rgba(0,0,0,.15);
}
```

#### 单条声明的声明块

在一个声明块中只包含一条声明的情况下，为了易读性和快速编辑可以考虑移除其中的换行。所有包含多条声明的声明块应该分为多行。

这样做的关键因素是错误检测 - 例如，一个 CSS 验证程序显示你在 183 行有一个语法错误,如果是一个单条声明的行，那就是他了。在多个声明的情况下，你必须为哪里出错了费下脑子。

```javascript
.span1 { width: 60px; }
.span2 { width: 140px; }
.span3 { width: 220px; }
```

#### 属性简写

尽量不使用属性简写的方式，属性简写需要你必须显式设置所有取值。常见的属性简写滥用包括:

-   padding
-   margin
-   font
-   background -border -border-radius

大多数情况下，我们并不需要设置属性简写中包含的所有值。例如，HTML 头部只设置上下的 margin，所以如果需要，只设置这两个值。过度使用属性简写往往会导致更混乱的代码，其中包含不必要的重写和意想不到的副作用。

```javascript
/* Bad example */
.element {
    margin: 0 0 10px;
    background: red;
    background: url("image.jpg");
    border-radius: 3px 3px 0 0;
}
/* Good example */
.element {
    margin-bottom: 10px;
    background-color: red;
    background-image: url("image.jpg");
    border-top-left-radius: 3px;
    border-top-right-radius: 3px;
}

```

#### Less 和 Sass 中的嵌套

避免不必要的嵌套。可以进行嵌套，不意味着你应该这样做。只有在需要给父元素增加样式并且同时存在多个子元素时才需要考虑嵌套。

```javascript
// Without nesting
.table > thead > tr > th { … }
.table > thead > tr > td { … }
// With nesting
.table > thead > tr {
    > th { … }
    > td { … }
}
```

#### 代码注释

代码是由人来编写和维护的。保证你的代码是描述性的，包含好的注释，并且容易被他人理解。好的代码注释传达上下文和目标。不要简单地重申组件或者 class 名称。

#### class 命名

1. 保持 class 命名为全小写，可以使用短划线（不要使用下划线和 camelCase 命名）。短划线应该作为相关类的自然间断。(例如，.btn 和 .btn-danger)。

2. 避免过度使用简写。.btn 可以很好地描述 button，但是 .s 不能代表任何元素。

3. class 的命名应该尽量短，也要尽量明确。

4. 使用有意义的名称；使用结构化或者作用目标相关，而不是抽象的名称。

5. 命名时使用最近的父节点或者父 class 作为前缀。

6. 使用 .js-\* 来表示行为(相对于样式)，但是不要在 CSS 中包含这些 class。

#### 选择器

1. 使用 class 而不是通用元素标签来优化渲染性能。

2. 避免在经常出现的组件中使用一些属性选择器 (例如，[class^="..."])。浏览器性能会受到这些情况的影响。

3. 减少选择器的长度，每个组合选择器选择器的条目应该尽量控制在 3 个以内。

4. 只在必要的情况下使用后代选择器 (例如，没有使用带前缀 classes 的情况).

#### 代码组织

1. 以组件为单位组织代码。

2. 制定一个一致的注释层级结构。

3. 使用一致的空白来分割代码块，这样做在查看大的文档时更有优势。

4. 当使用多个 CSS 文件时，通过组件而不是页面来区分他们。页面会被重新排列，而组件移动就可以了。

#### 编辑器配置

根据以下的设置来配置你的编辑器，将这些设置应用到项目的 .editorconfig 文件，来避免常见的代码不一致和丑陋的 diffs。

-   使用四个空格的缩进。

-   在保存时删除尾部的空白字符。

-   设置文件编码为 UTF-8。

-   在文件结尾添加一个空白行。

> 注意： .gitignore 文件需要将编辑器设置忽略掉

```javascript
.DS_Store
node_modules/
npm-debug.log*
yarn-debug.log*
yarn-error.log*
/test/unit/coverage/
/config/
# Editor directories and files
.idea/
.vscode/
*.suo
*.ntvs*
*.njsproj
*.sln
.editorconfig*
.brackets.json
/.vscode/
package-lock.json
package-lock.json*

```
