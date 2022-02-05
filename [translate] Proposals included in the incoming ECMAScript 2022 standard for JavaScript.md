# [译] 即将发布的 ECMAScript 2022 中包含的提案
> 原文地址：<https://medium.com/geekculture/proposals-included-in-the-incoming-ecmascript-2022-standard-for-javascript-6119a4518904>/
>
> 原文作者：Brandon Lara

ES2021 或者说 ES12 在去年夏天的早些时候已经发布了，但是许多人已经在想着 ES2022 会带来哪些新的特性。

在这篇文章中，我将会介绍并解释在规范的最新草案中已被接受的提案。

每个特性的提案都遵循着从不同阶段到第 4 阶段的这样一个过程，这意味着这些特性已经准备好包含在正式的 ECMAScript 标准中，并且会包含在最快的实用标准修订版版中。以下这些特性都已经完成，并处于第 4 阶段，且已经添加在 [ECMAScript 最新的草案中](https://tc39.es/ecma262/)。

## 类字段声明
到目前为止，在 ES 的规范中，类字段的定义和初始化都是在类的构造函数中完成的。但是通过这个新的类字段提案，类字段可以像这样在类的顶层被定义和初始化：

```js
class Post{
    title;
    content;
    shares = 0;
}
```

## 私有方法和字段
在这种新的定义类字段的方式下，私有字段现在也可以通过 `#` 前缀像下面这样来定义：

```js
class User {
    name;
    #password;
}
```

这与类方法和访问器的声明一致，所以私有方法和访问器也可以通过 `#` 前缀像下面这样来定义:

```js
class User {
    name;
    #password;
    get #password(){
        return #password;
    }
    #clear(){
        this.name = null;
        this.#password = null;
    }
}
```

## 静态公有方法和字段
这个提案在之前的类字段和私有方法提案的基础上，为 JavaScript 类新增了静态公有字段、静态私有方法字段和静态私有字段的特性。

这些特性都是为了遵循类字段和私有方法提案的“静态”方面，如下所示：

```js
class Environment {
    name;
    port;
    static hostname = 'localhost';
    static get hostname(){
        return hostname;
    }
}
```

静态方法通常都是工具类函数，例如用于创建或者复制对象，并且在当你想要字段仅在每个类中存在一次而不是在每个类的实例中存在时，静态字段是非常有用的。这对于缓存、固定的配置和其他不需要通过跨实例复制的数据来说是很有用的。

## 正则匹配索引
这个提案提供了一个新的 `/d` 标志去获取关于输入字符串中每个匹配项开始和索引位置结束的额外信息。

下面的代码片段清晰的展示了正则匹配索引提案是如何使用的：

```js
const regexp = /test(\d)/g; //without the /d flag
const regexp2022 = /test(\d)/dg; //with the /d flag
const str = 'test1test2';
const array = [...str.matchAll(regexp)];
const array2022 = [...str.matchAll(regexp2022)];
console.log(array[0]);
console.log(array2022[0]);
```

![](https://miro.medium.com/max/915/1*SUac7iADma-V6cdjEly5aw.png)

```js
console.log(array[1]);
console.log(array2022[1]);
```

![](https://miro.medium.com/max/927/1*ayMP9WtGtKkQnSWy8hHq1g.png)

## 顶层 `await`
顶层的 `await` 允许在异步函数外面使用 `await` 关键字。这个提案允许模块充当大型的异步函数，因此 ECMAScript 模块可以等待资源，以便导入了它们的其他模块在执行自己的代码之前，不得不去等待它们。

注意下面这个例子会在模块的顶层执行：

```js
import {getUser} from './data/user'

let user = await getUser();
```

这个新的提案可以正常的运行，但是旧的行为会抛出 `SyntaxError: await is only valid in async function` 的错误。

## 私有字段的检查
当你尝试去访问一个未声明的公有字段时，你将会得到 `undefined`，同时访问私有字段将会抛出异常。因此，我们在尝试去获取时，可以通过是否有异常抛出来判断类中是否有此私有的字段。但是这个提案介绍了一个更加有趣的解决方案，它使用 `in` 运算符，当指定的值或者字段存在在指定的对象或者类中时将会返回 `true` ，通过下面这个例子可以看到这个操作符在私有字段中是如何工作的：

```js
class User {
    name;
    #password;
    get #password(){
        return #password;
    }
    #clear(){
        this.name = null;
        this.#password = null;
    }
    static hasPassword(obj){
        return #password in obj;
    }
}
```

## 内置的可索引的新 `.at()` 方法
这个提案是数组的一个新方法，用于通过指定下标获取元素。当给定的索引是正值时，这个新的方法和使用括号符号访问有着相同的行为，但是当给定的索引是负值时，这个方法和 Python 的“负向索引”表现类似。这意味着 `.at()` 方法使用负整数索引时，会从数组的最后一项开始计算。所以这个方法作为 `array.at(-1)` 执行，其表现和 `arryay[array.length-1]` 一致，如下所示：

```js
const array = [0,1,2,3,4,5];
console.log(array[array.length-1]); // 5
console.log(array.at(-1)); //5
//same behaviour
console.log(array[array.lenght-2]); // 4
console.log(array.at(-2)); //4
//same behaviour
```

## 可访问的 `Object.prototype.hasOwnProperty`
有时 `Object.prototype` 是不可用或者是不可重新定义的。例如，`Object.create(null)` 会创建一个不继承自 `Object.prototype` 的对象，使其方法不可访问。此外，你也不能确定调用 `.hasOwnProperty()` 是否真的调用了内置的方法，因为如果你没有直接拥有这个对象的每个属性，它有可能会被覆盖重写掉。

为了避免这些问题，一种常见的方法是使用 `call()` 方法去调用 `hasOwnProperty()`，如下所示：

```js
const obj = { foo:'bar' }
let hasFoo = Object.prototype.hasOwnProperty.call(obj, 'foo');
console.log(hasFoo); //true
```

这个提案新增了一个 `Object.hasOwn(object, property)` 方法，这个方法的行为和 `Object.prototype.hasOwnProperty.call(object, property)` 一致。这个 `hasOwn(object, property)` 新方法提供了一种比以前的解决方案更方便检查对象属性的可访问方法，如下所示：

```js
const obj = { foo:'bar' }
let hasFoo = Object.hasOwn(obj, 'foo');
console.log(hasFoo); //true
```

## ECMAScript 类静态初始化块
类静态块提案提供了一种在类的声明或定义期间评估静态初始化代码块的优雅方法，可以访问类的私有字段。

通过下面这个例子来理解：

**没有静态块：**

```js
class User {
    static roles;
    name;
    #password;
}

try { 
    User.roles = getRolesFromDb();
} catch { 
    User.roles = getRolesFromBackup();
}
```

**有静态块：**

```js
class User {
    static roles;
    name;
    #password;
    static {
        try { 
            User.roles = getRolesFromDb();
        } catch { 
            User.roles = getRolesFromBackup();
        }
    }
}
```

正如你在这个例子中看到的，这两种解决方式并没有很大的差异，因为静态块是十分简单且小型的。但是，如果不使用静态块，随着代码复杂性的增加，这些类型的初始化将会不那么优雅、清晰或者可读。
