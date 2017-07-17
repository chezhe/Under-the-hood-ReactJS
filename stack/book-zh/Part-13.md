## Part 13

[![](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/13/part-13.svg)](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/13/part-13.svg)

<em>13.0 Part 13 (clickable)</em>

### Receive component (next element, to be more precise)

通过`ReactReconciler.receiveComponent`，React实际是从 `ReactDOMComponent`调用`receiveComponent` 并传入下一个元素. 重新指定给DOM组件实例并调用更新方法. `updateComponent`方法主要做了两件事: 基于`prev`和`next` 属性，更新DOM属性和DOM子节点. 我们已经分析了`_updateDOMProperties` (`src\renderers\dom\shared\ReactDOMComponent.js#946`)方法.这个方法主要处理HTML元素属性, 计算样式, 处理事件句柄等. 剩下的, 就交给`_updateDOMChildren` (`src\renderers\dom\shared\ReactDOMComponent.js#1076`).

### Alright, we’ve finished *Part 13*. That was a short one.)

Let’s recap how we got here. Let's look at the scheme one more time, then remove redundant less important pieces, and it becomes this:

[![](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/13/part-13-A.svg)](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/13/part-13-A.svg)

<em>13.1 Part 13 simplified (clickable)</em>

And we should probably fix spaces and alignment as well:

[![](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/13/part-13-B.svg)](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/13/part-13-B.svg)

<em>13.2 Part 13 simplified & refactored (clickable)</em>

Nice. In fact, that’s all that happens here. So, we can take the essential value from *Part 13* and use it for the final `updating` scheme:

[![](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/13/part-13-C.svg)](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/13/part-13-C.svg)

<em>13.3 Part 13 essential value (clickable)</em>

And then we're done!


[To the next page: Part 14 >>](./Part-14.md)

[<< To the previous page: Part 12](./Part-13.md)


[Home](../../README.md)
