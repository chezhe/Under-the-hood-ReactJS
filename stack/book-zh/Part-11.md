## Part 11

[![](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/11/part-11.svg)](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/11/part-11.svg)

<em>11.0 Part 11 (clickable)</em>

### 更新组建

代码注释如是形容这个方法:
>‘Perform an update to a mounted component. The componentWillReceiveProps and shouldComponentUpdate methods are called, then (assuming the update isn't skipped) the remaining update lifecycle methods are called and the DOM representation is updated. By default, this implements React's rendering and reconciliation algorithm. Sophisticated clients may wish to override this.’

>“对一个已挂载的组件执行更新”。componentWillReceiveProps 和 shouldComponentUpdate方法会被调用，然后（假设更新没有被跳过的话，可能是shouldComponentUpdate被返回false）剩下的更新生命周期方法会被调用，并且DOM的显示层更新。这默认是通过React的渲染和调和算法实现的，复杂的客户端可能会希望重写它。

好吧… 听起来很合理.

第一件事，我们看看`props` (1) 是否变了, 技术上来说,如果`setState`被调用或者`props` 变了的话，`updateComponent`方法 在两种不同场景下会被调用. 如果`props`确实变了, 那么生命周期函数`componentWillReceiveProps`会被调用. 然后, React在`pending state queue`(我们之前设置的部分state对象的队列, 在我们的例子中，队列大体上是这样的 [{message: "click state message"}])的基础上重新计算`nextState` (2). 当然, 如果只有`props`更新的话，state不受影响.

那么, 下一步, 我们设定`shouldUpdate` 默认返回`true`(3). 这也是为什么当`shouldComponentUpdate`没有被指定的话, 一个组件默认更新. 然后, 检查是否`force update`. 组件可能会从内部调用`forceUpdate`来更新, 而不是改变`state` 或 `props`, 但是, 根据React官方文档, 并不建议这么做. 所以, 强制更新总是会更新, 否则, 组件方法`shouldComponentUpdate`将会被调用, 并且 `shouldUpdate`会根据结果重新复制. If it's determined that a component should not update, React still needs to set `props` and `state` but shortcut the rest of the update.

### Alright, we’ve finished *Part 11*.

Let’s recap how we got here. Let's look at the scheme one more time, then remove redundant less important pieces, and it becomes this:

[![](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/11/part-11-A.svg)](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/11/part-11-A.svg)

<em>11.1 Part 11 simplified (clickable)</em>

And we should probably fix spaces and alignment as well:

[![](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/11/part-11-B.svg)](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/11/part-11-B.svg)

<em>11.2 Part 11 simplified & refactored (clickable)</em>

Nice. In fact, that’s all that happens here. So, we can take the essential value from *Part 11* and use it for the final `updating` scheme:

[![](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/11/part-11-C.svg)](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/11/part-11-C.svg)

<em>11.3 Part 11 essential value (clickable)</em>

And then we're done!


[To the next page: Part 12 >>](./Part-12.md)

[<< To the previous page: Part 10](./Part-10.md)


[Home](../../README.md)
