## Part 9

[![](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/9/part-9.svg)](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/9/part-9.svg)

<em>9.0 Part 9 (clickable)</em>

### But let’s move back..

在图中, 可能有也可能没有外部的影响下 (也就是‘用户行为’)，有多种方式会触发`setState`方法. 来看两个例子: 第一个例子,方法调用由鼠标点击触发, 第二个, 通过在`componentDidMount`中`setTimeout`.

有什么区别吗? React通过`batches`的方式处理更新, 这表明列表里的更新总得收集起来，然后 `flushed`.当鼠标事件发生, 它是在上层被处理的, 通过几层封装器，批量更新才开始. 另外,只有当 `ReactEventListener` 是`enabled`状态 (1)才会发生, 而且当组件挂载时, `ReactReconcileTransaction` 封装器之一为了保证挂载的安全，是禁用它的.`setTimeout case`呢? 也很简单, 在把一个组件放进`dirtyComponents`列表之前，React会保证事务已经开始(opened状态), 之后, 它会被关闭和清除更新.

React通过一些‘语法糖’封装原生事件的方式来实现‘合成事件’. 之后, 它们仍然表现为常见的事件. 上源码:
> ‘To help development we can get better dev tool integration by simulating a real browser event’

```javascript
var fakeNode = document.createElement('react');

ReactErrorUtils.invokeGuardedCallback = function (name, func, a) {
      var boundFunc = func.bind(null, a);
      var evtType = 'react-' + name;

      fakeNode.addEventListener(evtType, boundFunc, false);

      var evt = document.createEvent('Event');
      evt.initEvent(evtType, false, false);

      fakeNode.dispatchEvent(evt);
      fakeNode.removeEventListener(evtType, boundFunc, false);
};
```
好吧, 回到更新的话题. 过程是这样的:

1. 调用setState
1. 如果批处理事务还没打开，打开它
1. 把受影响的组件添加到`dirtyComponents`列表,
1. 调用`ReactUpdates.flushBatchedUpdates`关闭事务, 实际就是‘处理所有`dirtyComponents`收集到的组件’.

[![](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/9/set-state-update-start.svg)](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/9/set-state-update-start.svg)

<em>9.1 `setState` start (clickable)</em>

### Alright, we’ve finished *Part 9*.

Let’s recap how we got here. Let's look at the scheme one more time, then remove redundant less important pieces, and it becomes this:

[![](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/9/part-9-A.svg)](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/9/part-9-A.svg)

<em>9.2 Part 9 simplified (clickable)</em>

And we should probably fix spaces and alignment as well:

[![](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/9/part-9-B.svg)](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/9/part-9-B.svg)

<em>9.3 Part 9 simplified & refactored (clickable)</em>

Nice. In fact, that’s all that happens here. So, we can take the essential value from *Part 9* and use it for the final `updating` scheme:

[![](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/9/part-9-C.svg)](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/9/part-9-C.svg)

<em>9.6 Part 9 essential value (clickable)</em>

And then we're done!


[To the next page: Part 10 >>](./Part-10.md)

[<< To the previous page: Part 8](./Part-8.md)


[Home](../../README.md)
