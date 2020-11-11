### TypeScript

#### 安装 TypeScript

有两种主要的方式来获取 TypeScript 工具

-   通过 npm （Node.js 包管理器）

-   安装 Visual Studio 的 TypeScript 插件

针对使用 npm 的用户：

```javascript
npm install -g typescript
```

#### 构建你的第一个 TypeScript 文件

在编辑器，将下面的代码输入倒 greeter.ts 文件里

```javascript
function greeter(person) {
	return "Hello," + person;
}
let user = "Jane User";
document.body.innerHTML = greeter(user);
```

#### 编译代码

我们使用了.ts 扩展名，但是这段代码仅仅是 JavaScript 而已。你可以直接从现有的 Javascript 应用里复制/黏贴这段代码

在命令行上，运行 TypeScript 编辑器：

```javascript
tsc greeter.ts
```

输出结果为一个 greeter.js 文件，它包含了和输入文件中相同的 JavsScript 代码。 一切准备就绪，我们可以运行这个使用 TypeScript 写的 JavaScript 应用了！

接下来让我们看看 TypeScript 工具带来的高级功能。 给 person 函数的参数添加: string 类型注解，如下：

```javascript
function greeter(person: string) {
	return "Hello, " + person;
}

let user = "Jane User";

document.body.innerHTML = greeter(user);
```

#### 类型注解

TypeScript 里的类型注解是一种轻量级的为函数或变量添加约束的方式。 在这个例子里，我们希望 greeter 函数接收一个字符串参数。 然后尝试把 greeter 的调用改成传入一个数组：

```javascript
function greeter(person: string) {
	return "Hello, " + person;
}

let user = [0, 1, 2];

document.body.innerHTML = greeter(user);
```

重新编译，你会看到产生了一个错误。

```javascript
greeter.ts(7,26): error TS2345: Argument of type 'number[]' is not assignable to parameter of type 'string'.
```

类似地，尝试删除 greeter 调用的所有参数。 TypeScript 会告诉你使用了非期望个数的参数调用了这个函数。 在这两种情况中，TypeScript 提供了静态的代码分析，它可以分析代码结构和提供的类型注解。

要注意的是尽管有错误，greeter.js 文件还是被创建了。 就算你的代码里有错误，你仍然可以使用 TypeScript。但在这种情况下，TypeScript 会警告你代码可能不会按预期执行。

#### 接口

让我们开发这个示例应用。这里我们使用接口来描述一个拥有 firstName 和 lastName 字段的对象。 在 TypeScript 里，只在两个类型内部的结构兼容那么这两个类型就是兼容的。 这就允许我们在实现接口时候只要保证包含了接口要求的结构就可以，而不必明确地使用 implements 语句。

```javascript
interface Person {
	firstName: string;
	lastName: string;
}

function greeter(person: Person) {
	return "Hello, " + person.firstName + " " + person.lastName;
}

let user = { firstName: "Jane", lastName: "User" };

document.body.innerHTML = greeter(user);
```

#### 类

最后，让我们使用类来改写这个例子。 TypeScript 支持 JavaScript 的新特性，比如支持基于类的面向对象编程。

让我们创建一个 Student 类，它带有一个构造函数和一些公共字段。 注意类和接口可以一起共作，程序员可以自行决定抽象的级别。

还要注意的是，在构造函数的参数上使用 public 等同于创建了同名的成员变量。

```javascript
class Student {
    fullName: string;
    constructor(public firstName, public middleInitial, public lastName) {
        this.fullName = firstName + " " + middleInitial + " " + lastName;
    }
}

interface Person {
    firstName: string;
    lastName: string;
}

function greeter(person : Person) {
    return "Hello, " + person.firstName + " " + person.lastName;
}

let user = new Student("Jane", "M.", "User");

document.body.innerHTML = greeter(user);
```

重新运行 tsc greeter.ts，你会看到生成的 JavaScript 代码和原先的一样。 TypeScript 里的类只是 JavaScript 里常用的基于原型面向对象编程的简写。

#### 运行 TypeScript Web 应用

在 greeter.html 里输入如下内容：

```javascript
<!DOCTYPE html>
<html>
    <head><title>TypeScript Greeter</title></head>
    <body>
        <script src="greeter.js"></script>
    </body>
</html>
```
在浏览器里打开greeter.html运行这个应用！

可选地：在Visual Studio里打开greeter.ts或者把代码复制到TypeScript playground。 将鼠标悬停在标识符上查看它们的类型。 注意在某些情况下它们的类型可以被自动地推断出来。 重新输入一下最后一行代码，看一下自动补全列表和参数列表，它们会根据DOM元素类型而变化。 将光标放在 greeter函数上，点击F12可以跟踪到它的定义。 还有一点，你可以右键点击标识，使用重构功能来重命名。