# jsc: 我的新朋友

原文链接：https://furbo.org/2021/08/25/jsc-my-new-best-friend/

我的一个[朋友](https://twitter.com/danielpunkass)最近向我推荐了一个隐藏的很好的命令行工具。在 Safari 和苹果产品的其他部分使用的前端框架中，有一个工具叫做 `jsc`。它是 JavaScript 的命令行界面，使用和系统其它部分相同的代码。

你可以在这个路径下 `/System/Library/Frameworks/JavaScriptCore.framework/Versions/Current/Helpers/jsc` 找到这个工具的二进制文件。这个路径十分的冗长，因此我为这个路径取了个别名，让我在终端中输入 `jsc` 就能快速进入。

所以，我们使用 `jsc `可以做些什么？在浏览器中使用 JavaScript 你可以做许多事情，但需要注意的是，没有 `document` 和 `window` 实例。

如果你运行 `jsc -h `命令，你会看到很多用于测试和分析的 JavaScript 选项。显然，Webkit 团队在内部使用它来进行测试。但是我们仍然可以使用它来尝试我们的想法和运行简单的用例。

一张图片胜过千言万语，所以让我来给你们展示如何使用它来解决一个简单的问题。我最近需要将我们的土耳其语 [Frenzic](https://frenzic.com/) 本地化中的一些字符串转换成大写：[小写的 “i” 将被转换为无点的版本](https://en.wikipedia.org/wiki/Dotted_and_dotless_I)。

JavaScript 中的 [`toLocaleUpperCase()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/toLocaleUpperCase) 是处理这个问题最好的方法，所以我将 `jsc` 从我的工具包中拿出并投入使用。第一个挑战是如何获得输入。

幸运的是，`readline()` 方法可以获得键盘中的输入并返回值。不幸的是，输入并不是所期待的用 UTF-8 进行编码，而是以 ISO-8895-1(Latin-1) 返回。请记住，这里没有 document 实例，所以使用默认的[编码](https://stackoverflow.com/questions/52351400/why-its-necessary-to-specify-the-character-encoding-in-an-html5-document-if-the/52359266#52359266)方式。

为了解决这个限制，你可以将字符先转义为 UTF-16，然后再使用[这项技术](https://stackoverflow.com/a/5396742/132867)将它们解码为 UTF-8：

```
var text = decodeURIComponent(escape(readline()));
```

（如果有 Webkit 工程师读到这里，那么有一个像 `--encoding=utf-8` 这样的命令行选项该多好。）

生成输出和浏览器相比也有些不同。你将使用 `print()` 代替 `console.log()`。为了转换文本输入并显示它们，我会使用以下的代码：

```
print(text.toLocaleUpperCase('tr-TR'));
```

这里有一些可能证明是有用的[内置方法](https://trac.webkit.org/wiki/JSC)，但是目前，我只需要去读取和写入文本。虽然没有记录，但是 `jsc` 也接受标准的输入并且可以像 [shebang](https://en.wikipedia.org/wiki/Shebang_(Unix)) 一样使用：

```
$ echo "print(1+2);" | jsc
```

由于这部分代码我可能会再次使用，因此我创建了一个 Turkish.js 文件：

```
while (true) {
    print('Turkish text?');
    var text = decodeURIComponent(escape(readline()));
    print(text.toLocaleUpperCase('tr-TR'));
    print('-------------');
}
```

我现在可以随时使用 `jsc Turkish.js` 来运行这些代码。并且你也可以了解到在命令行中使用 JavaScript 是多么的方便。享受吧！