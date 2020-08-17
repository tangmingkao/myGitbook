# gitbook

## 安装

gitbook 的安装非常简单，详细指南可以参考 gitbook 文档。

这里的安装只需要一步就能完成！

```javascript
$ npm install gitbook -g
```

需要注意的是：用户首先需要安装 nodejs，以便能够使用 npm 来安装 gitbook。

## 基本使用

gitbook 的基本用法非常简单，基本上就只有两步：

1.  使用 gitbook init 初始化书籍目录
2.  使用 gitbook serve 编译书籍

下面将结合一个非常简单的实例，来介绍 gitbook 的基本用法。

## gitbook init

首先，创建如下目录结构：

```javascript
$ tree book/
book/
├── README.md
└── SUMMARY.md

0 directories, 2 files
```

README.md 和 SUMMARY.md 是两个必须文件，README.md 是对书籍的简单介绍：

```javascript
$ cat book/README.md
# README

This is a book powered by [GitBook](https://github.com/GitbookIO/gitbook).
```

SUMMARY.md 是书籍的目录结构。内容如下：

```javascript
$ cat book/SUMMARY.md
# SUMMARY

* [Chapter1](chapter1/README.md)
  * [Section1.1](chapter1/section1.1.md)
  * [Section1.2](chapter1/section1.2.md)
* [Chapter2](chapter2/README.md)
```

创建了这两个文件后，使用 gitbook init，它会为我们创建 SUMMARY.md 中的目录结构。

```javascript
$ cd book
$ gitbook init
$ tree
.
├── README.md
├── SUMMARY.md
├── chapter1
│   ├── README.md
│   ├── section1.1.md
│   └── section1.2.md
└── chapter2
    └── README.md

2 directories, 6 files
```

注意：在我的实验中，gitbook init 只支持两级目录！

## gitbook serve

> 如果运行 gitbook serve 失败，则可能是由于杀毒软件导致。关闭杀毒软件。

书籍目录结构创建完成以后，就可以使用 gitbook serve 来编译和预览书籍了：

```javascript
$ gitbook serve
Press CTRL+C to quit ...

Live reload server started on port: 35729
Starting build ...
Successfully built!

Starting server ...
Serving book on http://localhost:4000
```

gitbook serve 命令实际上会首先调用 gitbook build 编译书籍，完成以后会打开一个 web 服务器，监听在本地的 4000 端口。

现在，可以用浏览器打开 http://127.0.0.1:4000 查看书籍的效果

## gitbook 部署到 gitpages 上

gitbook 可以直接部署到 gitpages 页面上。因为每个 github 项目都提供了一个 gh-pages 分支去在线浏览项目效果

简单部署方案利用 gh-pages 库一键部署

安装 gh-pages 库

```javascript
npm install gh-pages
```

在 package.json 文件中编写命令

```javascript
"deploy": "gh-pages -d _book"
```

然后先构建生成\_book 文件夹

```javascript
gitbook build
```

再部署

```javascript
npm run deply
```

打开地址
https://username.github.io/projectName(myGitbook)
