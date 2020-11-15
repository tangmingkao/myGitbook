### 变量声明

#### 介绍

let和const是JavaScript里相对较新的变量声明方式。 像我们之前提到过的， let在很多方面与var是相似的，但是可以帮助大家避免在JavaScript里常见一些问题。 const是对let的一个增强，它能阻止对一个变量再次赋值。

因为TypeScript是JavaScript的超集，所以它本身就支持let和const。 下面我们会详细说明这些新的声明方式以及为什么推荐使用它们来代替 var。

如果你之前使用JavaScript时没有特别在意，那么这节内容会唤起你的回忆。 如果你已经对 var声明的怪异之处了如指掌，那么你可以轻松地略过这节。

#### var 声明

一直以来我们都是通过var关键字定义javascript变量。

```javascript
var a = 10;
```

大家都能理解，这里定义了一个名为a值为10的变量。

我们也可以在函数内部定义变量：

```javascript
function f() {
    var message = "Hello, world!";
    return message;
}
```
并且我们也可以在其它函数内部访问相同的变量。

```javascript
function f() {
    var a = 10;
    return function g() {
        var b = a + 1;
        return b;
    }
}
var g = f();
g(); // returns 11;
```

上面的例子里，g可以获取到f函数里定义的a变量。 每当 g被调用时，它都可以访问到f里的a变量。 即使当 g在f已经执行完后才被调用，它仍然可以访问及修改a。

```javascript
function f() {
    var a = 1;
    a = 2;
    var b = g();
    a = 3;
    return b;
    function g() {
        return a;
    }
}
f(); // returns 2
```

对于熟悉其它语言的人来说，var声明有些奇怪的作用域规则。 看下面的例子：

```javascript
function f(shouldInitialize: boolean) {
    if (shouldInitialize) {
        var x = 10;
    }
    return x;
}
f(true);  // returns '10'
f(false); // returns 'undefined'
```

