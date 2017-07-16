## Part 7

[![](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/7/part-7.svg)](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/7/part-7.svg)

<em>7.0 Part 7 (clickable)</em>

### Back to the beginning

挂载后, 我们就得到将要被插入到document的HTML元素. 事实上, `markup` (1) is generated, 但是 `mountComponent`, 虽然这么叫, 并不是真正的HTML markup. 它是个包含`children`, `node` (实际的DOM节点),等等的数据结构. 但是, 我们得把HTML元素放到容器里 (容器就是 `ReactDOM.render`调用时传入的). 把它添加到DOM里时, React会清空以前的元素. `DOMLazyTree`(2) 是一个用来处理树形数据结构操作的工具类, 就是在处理DOM时要做的.

最后一件事是`parentNode.insertBefore(tree.node)`(3), `parentNode` 是个`div` 容器节点， `tree.node` 是`ExampleAppliication` div节点. 被创建的HTML元素在挂载期间才被插进document.

仅此而已吗? 并不是这样的. 如果你还记得的话, `mount`调用是被封装在事务里的. 这意味着我们应该关闭它. 我们来看看`close`封装器列表. Mostly, 我们应该重置锁住的行为`ReactInputSelection.restoreSelection()`, `ReactBrowserEventEmitter.setEnabled(previouslyEnabled)`,但是, 我们还得通知所有的回调`this.reactMountReady.notifyAll`(4) 我们之前把 `transaction.reactMountReady`入栈了. 其中， `componentDidMount`, 将会由`close`封装器触发.

现在你清楚了‘组件已挂载’实际意味着什么. 恭喜!

### One more transaction to close

实际上, 不仅仅是事务. 我们忘了用来封装 `ReactMount.batchedMountComponentIntoNode` 的调用. 

我们来看一下用来处理`dirtyComponents`的`ReactUpdates.flushBatchedUpdates`封装器(5). 听上去很有趣,不是吗? Well, it's good or bad news. 我们只是第一挂载, 所以还没有脏组件. 这意味着它是个没用的调用. 所以, 我们也可以关闭事务， 批处理策略更新完了.

### Alright, we’ve finished *Part 7*.

Let’s recap how we got here. Let's look at the scheme one more time, then remove redundant less important pieces, and it becomes this:

[![](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/7/part-7-A.svg)](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/7/part-7-A.svg)

<em>7.1 Part 7 simplified (clickable)</em>

And we should probably fix spaces and alignment as well:

[![](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/7/part-7-B.svg)](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/7/part-7-B.svg)

<em>7.2 Part 7 simplified & refactored (clickable)</em>

Nice. In fact, that’s all that happens here. So, we can take the essential value from *Part 7* and use it for the final `mounting` scheme:

[![](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/7/part-7-C.svg)](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/7/part-7-C.svg)

<em>7.3 Part 7 essential value (clickable)</em>

And then we're done! In fact, we're done with mounting. Let's see it below!


[![](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/7/mounting-parts-C.svg)](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/7/mounting-parts-C.svg)

<em>7.4 Mounting (clickable)</em>

[To the next page: Part 8 >>](./Part-8.md)

[<< To the previous page: Part 6](./Part-6.md)


[Home](../../README.md)
