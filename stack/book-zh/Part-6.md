## Part 6

[![](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/6/part-6.svg)](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/6/part-6.svg)

<em>6.0 Part 6 (clickable)</em>

### Create initial children

看起来就像元素本身已经完成了, 所以现在我们讲讲子元素. 这里有两步: 子元素应该挂载 (`this.mountChildren`)(1)和连接到父元素(`DOMLazyTree.queueChild`)(2). 我们来看看子元素挂载，因为它明显更有趣

有一个叫`ReactMultiChild` (`src\renderers\shared\stack\reconciler\ReactMultiChild.js`)的独立模块来管理子元素. 我们来看看`mountChildren`方法. 它也包含两个主要任务. 第一,我们实例化子元素(通过 `ReactChildReconciler`) 并且挂载它们. 这里的子元素到底是什么呢? 它可以是简单的HTML标签或另一个自定义组件. 为了处理HTML，我们需要实例化`ReactDOMComponent`；对于自定义组件是`ReactCompositeComponent`. 挂载流取决于子元素类型.

### One more time

如果你读到这里, 大概是时候阐明和回顾总体进程了. 休息一下，回忆对象的序列.

[![](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/6/overall-mounting-scheme.svg)](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/6/overall-mounting-scheme.svg)

<em>6.1 Overall mounting scheme (clickable)</em>

1) React为每个自定义组件实例化`ReactCompositeComponent`(包含`componentWillMount`等生命周期钩子)并挂载它.

2) 在挂载期间, 首先, 一个自定义组件的实例会被创建(`constructor`被调用).

3) 然后, 它的渲染方法被调用 (例如, render返回`div`)，`React.createElement` 创建React元素. 它可以直接被调用或在Babel处理JSX之后，并替换掉渲染方法里的标签.

4) 我们需要`div`对应的DOM元素. 所以, 在实例化进程中, 我们通过元素对象（element-objects）创建`ReactDOMComponent`的实例.

5) 然后, 我们需要挂载DOM组件. 那实际意味着创建DOM元素，并添加事件监听器等等.

6) 然后, 我们处理DOM组件的初始子元素. 创建它们的实例并挂载它们.自定义元素或者HTML标签,分别递归地执行 1) 或 5). 然后对所有嵌套元素都这么做.

够直白吧.

所有挂载基本结束了. `componentDidMount`方法入列! 

### Alright, we’ve finished *Part 6*.

Let’s recap how we got here. Let's look at the scheme one more time, then remove redundant less important pieces, and it becomes this:

[![](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/6/part-6-A.svg)](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/6/part-6-A.svg)

<em>6.2 Part 6 simplified (clickable)</em>

And we should probably fix spaces and alignment as well:

[![](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/6/part-6-B.svg)](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/6/part-6-B.svg)

<em>6.3 Part 6 simplified & refactored (clickable)</em>

Nice. In fact, that’s all that happens here. So, we can take the essential value from *Part 6* and use it for the final `mounting` scheme:

[![](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/6/part-6-C.svg)](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/6/part-6-C.svg)

<em>6.4 Part 6 essential value (clickable)</em>

And then we're done!


[To the next page: Part 7 >>](./Part-7.md)

[<< To the previous page: Part 5](./Part-5.md)


[Home](../../README.md)
