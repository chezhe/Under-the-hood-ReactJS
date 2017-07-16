## Part 5

[![](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/5/part-5.svg)](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/5/part-5.svg)

<em>5.0 Part 5 (clickable)</em>

### Update DOM properties

看起来有点让人望而却步, 是吗? 关键点是把旧的和新的`props`的不同(diff)高效地应用. 
看看这里的代码注释:
> “Reconciles the properties by detecting differences in property values and updating the DOM as necessary. This function is probably the single most critical path for performance optimization.”

实际上有两个循环. 首先, 遍历旧的`props` 然后遍历新的`props`. 在外面的例子里, 挂载时, `lastProps` (旧的) 是空的 (明显, 这里我们第一次给props赋值), 尽管如此，我们仍然要看看这里到底发生了什么.

### Last `props` loop
第一步, 我们查看`nextProps`是否包含了相同的值. 如果这样, 我们就跳过, 因为之后遍历 `nextProps` 时会处理. 然后,我们重置样式, 删除事件监听器 (如果之前设置了的话), 并且删除DOM的属性及其值.对于属性, 我们得确定它们不是`RESERVED_PROPS`之一, 它实际是`prop`, 如 `children` 或 `dangerouslySetInnerHTML`.

### Next `props` loop
这里, 第一步是检查`prop`是否变了, 就是检查新的prop和旧的是否一样. 如果一样, 我们什么也不做. 对于 `styles`, (你可能注意到处理它的方式有点不一样) we update values that have changed since `lastProp`. 然后, 我们添加事件监听器 (对, 例如`onClick`事件). 我们提供更多细节来分析.

最重要的是, 所有的React应用，所有的工作都是通过‘合成的’ 事件. 没什么特别的, 只是些为了高效工作的封装器. 下一件事, `EventPluginHub`是用来管理事件监听器的中间模块(`src\renderers\shared\stack\event\EventPluginHub.js`). 它包含一个`listenerBank` map，用来缓存和管理所有监听器.
我们将添加监听器, 但不是以正确的方式. 我们应该在组件和DOM元素准备处理事件时添加事件. 看起来我们已经延迟了执行, 但你可能会问, 我们怎么才能知道什么时候? 是时候回答这个问题了! 你还记得我们什么时候把所有的方法和调用传给`transaction`? 我们那样做它对这样的情况很有帮助. 源码证明也是这样的:

```javascript
//src\renderers\dom\shared\ReactDOMComponent.js#222
transaction.getReactMountReady().enqueue(putListener, {
    inst: inst,
    registrationName: registrationName,
    listener: listener,
});
```

事件监听器之后, 我们设置DOM属性和DOM属性的值. 像前面一样, 对于属性，不能是`RESERVED_PROPS`中的一个, 它实际也是`prop`, 就像`children`或`dangerouslySetInnerHTML`.

在处理新旧prop时, 我们计算出`styleUpdates` config并传给`CSSPropertyOperations` 模块.

更新属性这部分讲完了. 我们继续.

### Alright, we’ve finished *Part 5*.

Let’s recap how we got here. Let's look at the scheme one more time, then remove redundant less important pieces, and it becomes this:

[![](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/5/part-5-A.svg)](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/5/part-5-A.svg)

<em>5.1 Part 5 simplified (clickable)</em>

And we should probably fix spaces and alignment as well:

[![](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/5/part-5-B.svg)](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/5/part-5-B.svg)

<em>5.2 Part 5 simplified & refactored (clickable)</em>

Nice. In fact, that’s all that happens here. So, we can take the essential value from *Part 5* and use it for the final `mounting` scheme:

[![](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/5/part-5-C.svg)](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/5/part-5-C.svg)

<em>5.3 Part 5 essential value (clickable)</em>

And then we're done!


[To the next page: Part 6 >>](./Part-6.md)

[<< To the previous page: Part 4](./Part-4.md)


[Home](../../README.md)
