## Part 0

[![](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/0/part-0.svg)](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/0/part-0.svg)

<em>0.0 Part 0 (clickable)</em>

### ReactDOM.render
好的，我们以ReactDOM.render开始.

入口是ReactDom.render. 我们的应用在这里渲染成DOM. 我为了方便调试，创建了一个简单的组件 `<ExampleApplication/>`. 所以, 第一件发生的事是 **JSX 会被转化诚React组件**. 它们很简单, 差不多就像结构简单的对象. 它们只是被渲染成render函数里的样子,仅此而已. 诸如 props, key, and ref的概念对于你来说应该很熟悉了. 属性类型指的是由JSX所描述的标记对象. 所以, 在我们的例子中, 就是 `ExampleApplication`, 但也可能仅仅是 字符串`button`所对应的Button标签, 等等. 而且, 在React组件创建期间, React 会合并 `defaultProps` 和 `props` (如果他们被指定了的话) 并检验 `propTypes`.

查看源代码来获取更多细节: `src\isomorphic\classic\element\ReactElement.js`

### ReactMount
你可以看见被叫做 `ReactMount`的模块 (01). 它包含组件加载时的逻辑. 事实上, 在`ReactDOM`里没有任何逻辑, 它只是和`ReactMount`一起工作的接口, 所以当你调用 `ReactDOM.render`时，你实际调用的是 `ReactMount.render`. 那么加载到底是什么呢?
> 挂载是一个 通过创建显式的DOM elements并把它们插入所提供的`container`初始化React组件的进程

至少代码注释是这么描述的. 那么，它实际意味着什么呢? 想象一下如下变换:


[![](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/0/mounting-scheme-1-small.svg)](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/0/mounting-scheme-1-small.svg)

<em>0.1 JSX to HTML (clickable)</em>

React需要**把你的组件描述变换成HTML**来插入浏览器的document里去. 我们是怎么办到的呢? 它需要处理**属性(props), 事件监听(events listeners), 嵌套组件(nested components)**, 和逻辑. 需要把你高级的描述(components)转化成为底层的数据(HTML) 才能变成一个网页. 那就是挂载做的事.


[![](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/0/mounting-scheme-1-big.svg)](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/0/mounting-scheme-1-big.svg)

<em>0.1 JSX to HTML, extended (clickable)</em>

继续. 在我们的旅途中找点乐子吧.

>  有趣的事实: 确保scroll正在监听(02)

> 有趣的事是, 在根组件第一次渲染时, React初始化scroll的监听器并缓存scroll的值，这样程序不需要触发回流(reflow)来获取它们了 . 事实上, 由于不同浏览器渲染机制的不同, 一些DOM的值并不是静态的, 它们在你每次用到它们的时候每次都现算出来. 当然, 这会影响性能. 事实上只是有些老的浏览器不支持 `pageX` 和 `pageY`.  Reacts也尽量对此做了优化. 如你所见, 制作一个快速的工具会用到很多技术, scroll就是一个例子.

###  React component 实例化

看这张图, 这是(03)创建实例的过程. 在这里创建 `<ExampleApplication />` 的实例太早了. 事实上，我们是先实例化`TopLevelWrapper` (React内部类). 我们看下一张图.

[![](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/0/jsx-to-vdom.svg)](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/0/jsx-to-vdom.svg)

<em>0.3 JSX to VDOM (clickable)</em>

你可以看见三个时期, JSX通过React elements会被转化成一个React内部组件类型:  `ReactCompositeComponent` (为我们自己的组件),  `ReactDOMComponent` (为 HTML 标签), 和 `ReactDOMTextComponent` (为文本节点). 我们将忽略`ReactDOMTextComponent` 而只关注前两点.

内部组件? 你已经听说过**虚拟DOM(Virtual DOM)**, 对吧? 虚拟DOM是一种React用来不直接操作DOM在比较计算差异等等来展示DOM. 它会让React运行很快! 但是, React源码里并没有文件或类叫‘Virtual DOM’. 那是因为V-DOM仅仅是一个概念, 一种操作真实DOM的方法. 所以有的人说V-DOM 条目指的是React元素, 但在我看来, 并不是这样的. 我认为虚拟DOM指的是这三个类: `ReactCompositeComponent`, `ReactDOMComponent`, `ReactDOMTextComponent`. 等会儿你就知道为什么了.

我们来完成实例化吧. 我们创建了`ReactCompositeComponent`的实例, 但事实上, 不是因为我们把`<ExampleApplication/>`放到`ReactDOM.render`里. React总是从`TopLevelWrapper`开始渲染组件树. 它是一个空闲的(idle) 封装, 它的 `render` (组件的渲染方法)将返回`<ExampleApplication />`.
```javascript
//src\renderers\dom\client\ReactMount.js#277
TopLevelWrapper.prototype.render = function () {
  return this.props.child;
};

```

所以，到目前为止，只是`TopLevelWrapper`被创建了. 继续
>  有趣的事实: 验证 DOM 嵌套

> 几乎每次被嵌套的组件渲染时, 他们都会被专门用来验证HTML的模块 `validateDOMNesting`检验. DOM 嵌套验证 意味着逐级检查`child -> parent` 标签. 例如, 如果父标签是 `<select>`, 子标签应该是以下中的一种: `option`, `optgroup`, or `#text`.这些规则在这里https://html.spec.whatwg.org/multipage/syntax.html#parsing-main-inselect定义的. 在工作中你可能已经看过这个模块了, 它会弹出类似这样的错误:
<em> &lt;div&gt; cannot appear as a descendant of &lt;p&gt; </em>.


### Alright, we’ve finished *Part 0*.

简述一下我们是怎么到这的. 再看一遍这张图, 然后去掉多余的不重要的部分, 它就变成这样了:

[![](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/0/part-0-A.svg)](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/0/part-0-A.svg)

<em>0.4 Part 0 simplified (clickable)</em>

我们也该修正一下空白和排版:

[![](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/0/part-0-B.svg)](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/0/part-0-B.svg)

<em>0.5 Part 0 simplified & refactored (clickable)</em>

[![](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/0/part-0-C.svg)](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/0/part-0-C.svg)

<em>0.6 Part 0 essential value (clickable)</em>

And then we're done!


[To the next page: Part 1 >>](./Part-1.md)

[<< To the previous page: Intro](./Intro.md)


[Home](../../README.md)