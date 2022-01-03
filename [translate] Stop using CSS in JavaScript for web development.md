# 停止在 JavaScript 中使用 CSS 进行 Web 开发

> 原文地址：https://gajus.medium.com/stop-using-css-in-javascript-for-web-development-fa32fb873dcc
> 
> 原文作者：Gajus Kuizinas

CSS 并不在任何地方出现。许多项目因为错误的原因选择在 JavaScript 中设置样式。这篇文章列举了常见的误解以及针对这些问题现有的 CSS 解决方法。

> 在这篇文章中的任何内容都不是针对特定项目或个人的人身攻击。我将 “在 JavaScript 中的 CSS” 定义为使用 `styled-components`，因为这是最近在 React 中给组件写样式的趋势。
> 
> `styled-component` 的作者（ Max Stoiber 和 Glen Maddern; 以及其他的贡献者）是聪明人，有着聪明的想法和良好的意图。
> 
> 为了充分的透明，我也应该注明我是 [react-css-modules](https://github.com/gajus/react-css-modules) 和 [babel-plugin-react-css-modules](https://github.com/gajus/babel-plugin-react-css-modules) 的作者。

### CSS 和 JavaScript 的历史
级联样式表（CSS）语言创造出来是为了描述用标记语言编写的文档的展示。JavaScript 被创造为一种“胶水语言”，用于组装例如图片和插件等的组件。在过去几年，JavaScript 不断发展和变化为了适应新的用例。

Ajax 术语的来临标志着一个重要的里程碑。这时 Prototype，jQuery，MooTools 等库吸引了大量社区进行合作努力，为解决后台数据获取和跨浏览器不一致的问题。这使得另一个新的问题出现了：如何去管理这些数据？

快进到 2010 年，Backbone.js 作为应用状态管理的行业标准而出现。不久，Knockout 和 Angular 因双向绑定吸引了每一个人。再不久，React 和 Flux 也出现了。这开始了单页面应用的时代，即应用程序由组件组成。

### CSS 呢？
用 `styled-components` 文档中的话来说：
> 纯 CSS 的问题在于它是在 web 由文档组成的时代被创建的。在 1993 年创建的 web 用于交换更多科学的文档，并且引入 CSS 作为对这些文档样式进行设置的解决方案。然而在如今，我们正在创建丰富的，交互式的，面向用户的应用，而 CSS 并不是为了这些用例而构建的。

我并不同意。

CSS 已经发展到可以满足现代 UI 的要求了。新特性的数量在过去的十年里已经超过合理的范围了（伪类，伪元素，CSS 变量，媒体查询，关键帧，关系选择器，columns，flex，grid，计算属性等）。

从 UI 的视角来看，“组件”是文档的一个独立片段（ `<button />` 是一个组件）。CSS 设计来是为了设置文档的样式，其中包含所有的组件。这有什么问题？

俗话说：“为工作使用正确的工具”。

### Styled-components
`styled-components` 允许使用标记模板文字在 JavaScript 中编写 CSS。它移除了组件和样式之间的映射关系——组件被构造成低级的样式结构，例如：

```
import React from 'react';
import styled from 'styled-components';
// Create a <Title> react component that renders an <h1> which is
// centered, palevioletred and sized at 1.5em
const Title = styled.h1`
  font-size: 1.5em;
  text-align: center;
  color: palevioletred;
`;
// Create a <Wrapper> react component that renders a <section> with
// some padding and a papayawhip background
const Wrapper = styled.section`
  padding: 4em;
  background: papayawhip;
`;
// Use them like any other React component – except they're styled!
<Wrapper>
  <Title>Hello World, this is my first styled component!</Title>
</Wrapper>
```

结果：
![](https://miro.medium.com/max/1050/1*0skFssloR-AZvjVsNDyQBQ.png)

`stylde-components` 现在正在成为在 React 中设计组件样式的新方法。

> 让我们来把一件事情弄清楚： styled-components 只是 CSS 之上一个更高级别的抽象。它所做的只是解析你在 JavaScript 中定义的 CSS，然后创建映射到 CSS 的 JSX 元素。

我不喜欢这种趋势是因为它被许多误解所包围。

我调查过了人们在 IRC、Reddit 和 Discord 上使用 styled-components 的原因，并列出了一份选择使用 styled-components 的普遍回答列表，我称它们为假想。

#### 假想 1：它解决了全局命名空间和样式的冲突
我称这为一个假想是因为这听起来像是这些问题还没有得到解决。CSS Modules，Shadow DOM 和数不清的命名约定在很久以前在社区中都已经解决了这个问题。

> 我不建议一个新的项目去依赖命名约定。命名约定容易出现使用者错误。

`styled-components`（就像是 CSS 模块）把命名的责任从人类身上带走。人类容易犯错；而计算机可以减少犯错的频率。

就其本身而言，这并不足以成为开始使用 `styled-components` 的原因。

#### 假想 2：使用 styled-components 使代码更简洁
举个例子：

```
<TicketName></TicketName>
<div className={styles.ticketName}></div>
```

首先——这并没有直接的关系。差异可以忽略不计。

其次，这并不是真的。总字符数取决于样式的名称。

```
<TinyBitLongerStyleName></TinyBitLongerStyleName>
<div className={styles.longerStyleName}></div>
```

这也同样应用于文章后续将会提到的构建样式中。（假想 5：它可以轻松的对组件进行有条件的样式设置）。`styled-components` 仅在基础的组件情况下获胜。

#### 假想 3：使用 styled-components 会让你更多的思考语义
这个前提是错误的。样式和语义代表着不同的问题，需要不同的解决方法。引用 Adam Morese 的话：
> 内容语义和视觉风格没有任何关系。在过去我使用乐高构建一些东西的时候，我从来不会想到‘噢，这是发动机的一部分’，我会想‘噢，酷，这是一个 1×4 的蓝色乐高积木，我可以用它做任何我想做的事情’。不管我是在建造水下航海者基地还是飞机，我确切的知道如何去使用那个乐高块。

— http://mrmrs.io/writing/2016/03/24/scalable-css/

（我十分推荐阅读 Adam 的这篇关于 [可扩展的 CSS](http://mrmrs.io/writing/2016/03/24/scalable-css/) 文章。）

尽管如此，让我们举个例子。

```
<PersonList>
  <PersonListItem>
    <PersonFirstName>Foo</PersonFirstName>
    <PersonLastName>Bar</PersonLastName>
  </PersonListItem>
</PersonList>
```

语义是关于使用正确的标签来构建标记。你知道这些组件会呈现什么 HTML 标志吗？不，你不知道。

比较一下：

```
<ol>
  <li>
    <span className={styles.firstName}>Foo</span>
    <span className={styles.lastName}>Bar</span>
  </li>
</ol>
```

#### 假想 4：它使得样式更容易扩展
在第一个版本中你可以使用 `styled(StyledComponent)` 来扩展样式结构；在第二个版本中引入了 `extend` 方法来扩展已有的样式，举例：

```
const Button = styled.button`
  padding: 10px;
`;
const TomatoButton = Button.extend`
  color: #f00;
`;
```

这非常好。但是你在 CSS 中也可以做到（或者使用 CSS 模块组合，或者使用 SASS 继承 mixin `@extend`）。

```
button {
  padding: 10px;
}
button.tomato-button {
  color: #f00;
}
```

是什么使得 JavaScript 方法更加简单？

#### 假想 5：它使得对组件进行有条件的样式设置更加简单
这个想法是你可以基于它们的 props 来对组件进行样式的设置，例如：

```
<Button primary />
<Button secondary />
<Button primary active={true} /
```

这在 React 世界中非常有意义。毕竟，组件行为是通过使用 props 来控制的。但将 prop 的值直接附加到样式中是否是有意义的呢？可能吧。但是让我们先来看下组件的实现：

```
styled.Button`
  background: ${props => props.primary ? '#f00' : props.secondary ? '#0f0' : '#00f'};
  color: ${props => props.primary ? '#fff' : props.secondary ? '#fff' : '#000'};
  opacity: ${props => props.active ? 1 : 0};
`;
```

使用 JavaScript 强大的功能有条件的创建样式表给了你们很多能力。但是，这也意味着样式更难以去解释。和以下的 CSS 进行比较：

```
button {
  background: #00f;
  opacity: 0;
  color: #000;
}
button.primary,
button.seconary {
  color: #fff;
}
button.primary {
  background: #f00;
}
button.secondary {
  background: #0f0;
}
button.active {
  opacity: 1;
}
```
在这个例子中，CSS 的代码是更加精简且在主观上是更容易理解的。此外，在 CSS 中使用预处理器可以使得这段代码更加精简和有秩序的，例如：

```
button {
  background: #00f;
  opacity: 0;
  color: #000;
  
  &.primary,
  &.seconary {
    color: #fff;
  }
  &.primary {
    background: #f00;
  }
  &.secondary {
    background: #0f0;
  }
  &.active {
    opacity: 1;
  }
}
```

#### 假想 6：它允许更好的代码组织
曾经有几个人和我说他们喜欢 `styled-components` 是因为它允许在同一个文件中包含样式和 JavaScript。

我可以理解为同一个组件设置多个文件是多么乏味的，但是塞满样式并标记为一个文件也是可怕的解决方式。这样不仅使得版本控制很难去追踪，也使得在任何一个不是简单按钮的组件中需要进行长滚动。

如果你必须在一个文件中包含 CSS 和 JavaScript，那么可以考虑使用[ css-liter-loader](https://github.com/4Catalyzer/astroturf)。后者允许你在构建时使用 [extra-text-webpack-plugin](https://github.com/webpack-contrib/extract-text-webpack-plugin) 来提取 CSS，并使用你的标准加载器配置去处理 CSS.

#### 假想 7：它使 DX 更棒。工具也很棒！
你显然没有用过 `styled-components`。

- 当样式出现错误时，整个应用都会因为一长串的堆栈异常错误跟踪而崩溃。将此和 CSS 进行对比，当有样式错误时仅仅只会错误的呈现元素。
- 元素并没有可识别的 `classname`，因此当你在调试时你将在 React 元素树和 DOM 树开发工具中不断进行切换。
- 没有代码检错。
- 由于没有给出警告和错误提示，无效的样式容易被忽略（例如 `clear: both; float left; color: #f00;` 调试很困难，我花了将近 15 分钟的时间去看堆栈跟踪，即使是阅读了 `styled-components` 的源码。只有当我在聊天中复制粘贴代码去寻找帮助时，才会有人注意到丢失的 `:`。你也注意到了，对吗？）
- 少数 IDE 支持语法高亮，代码提示和其他 IDE 细节。如果你正在和金融或者政府机构合作，那么 Atom IDE 可能不在讨论的范围之内。

#### 假想 8：更好的性能，更小的包体积
- 就目前而言，`styled-components` 不能被提取为静态的 CSS 文件（例如使用 [extract-text-webpack-plugin](https://github.com/webpack-contrib/extract-text-webpack-plugin)）。这意味着你的浏览器不能够解释样式直到 `styled-components` 解析样式并添加到 DOM。
- 缺少单独的文件意味着你不能够分开缓存 CSS 和 JavaScript。
- 所有的样式组件都被额外的高阶组件所包裹。这导致了不必要的性能损失。同样的架构缺陷也是我停止使用 [react-css-modules](https://github.com/gajus/react-css-modules) 的原因（并创造了 [babel-plugin-react-css-modules](https://github.com/gajus/babel-plugin-react-css-modules)）。
- 因为高阶组件，如果你在服务端渲染，这将会导致它产生更大的标记文档。
- 和 keyframes 相比，不要让我用动态样式值来制作动画。

#### 假想 9：它允许开发响应式的组件
这个话题主要是讨论基于周围环境，例如父容器尺寸、子元素的数量等设计组件样式的能力。

首先，`styled-components` 对此并没有做什么。它在项目范围之外。在这个情况下，你最好直接设置组件样式的值以避免额外的开销。

然而，元素查询是一个有趣的话题，也是 CSS 中的一个热门话题，主要用于 [EQCSS](https://elementqueries.com/) 项目及其对应的项目。在语法层面上元素查询和 `@media` 查询十分相似，不同之处在于元素查询是对特定元素进行操作。

```
@element {selector} and {condition} [ and {condition} ]* { {css} }
```

- `{selector}` 是针对一个或多个元素的 CSS 选择器，例如：`#id` 或 `.class`
- `{condition}` 由度量和值的组成。
- `{css}` 可以包含任何有效的 CSS规则。（例如：`#id div { color: red }`）
  
元素选择器允许使用例如 `min-width`, `max-width`, `min-height`, `max-height`, `min-characters`, `max-characters`, `min-children`, `max-children`, `min-lines`, `max-lines`, `min-scroll-x`, `max-scroll-x` 以及其它属性的条件设计元素的样式（http://elementqueries.com/） 。

<div align=center> <img src="https://miro.medium.com/max/450/1*OIAv_zFIpjlunKOWcGBOwQ.gif"> </div>

总有一天，EQCSS 的变化将出现在 CSS 的规范中。

### 且慢
大多数这些事情都可以通过社区、React 中的更改或者 `styled-components` 来长期解决。但重点是什么？CSS 已经被广泛的支持，它周围拥有大量的社区并且可以正常工作。

这篇文章的重点不是为了阻止读者在 JavaScript 中使用 CSS 或者使用 `styled-components`。使用 `styled-components` 来设置样式有一个很好的用例：更好的跨平台支持。但不要出于错误的原因而使用它。

### 所以如今该使用什么？
现在使用 [Shadow DOM v1](https://caniuse.com/#feat=shadowdomv1) 还太早了（仅 51% 的支持）。搭配命名规则（我推荐 [BEM](http://getbem.com/)）使用 CSS。如果你担心类名冲突（或者太懒以至于不想使用 BEM），那么可以使用 [CSS modules](https://github.com/css-modules/css-modules)。如果你正在开发 React web，可以考虑使用 [babel-plugin-react-css-modules](https://github.com/gajus/babel-plugin-react-css-modules)。如果你正在开发 React Native，那么 styled-components 会是不错的选择。

### 在 JS 中使用 CSS 的争论是什么？
请阅读 Max Stoiber 的这篇文章 https://mxstbr.com/thoughts/css-in-js/ 。
