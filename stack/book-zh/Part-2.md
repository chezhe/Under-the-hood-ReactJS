## Part 2

[![](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/2/part-2.svg)](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/2/part-2.svg)

<em>2.0 Part 2 (clickable)</em>

### One more transaction

这次它是 `ReactReconcileTransaction`. 对我们来说最重要的是事务包装器. 其中有三种包装器:

```javascript
//\src\renderers\dom\client\ReactReconcileTransaction.js#89
var TRANSACTION_WRAPPERS = [
  SELECTION_RESTORATION,
  EVENT_SUPPRESSION,
  ON_DOM_READY_QUEUEING,
];
```

这些包装器主要用来 **保持状态更新**, 在方法调用前锁定可变的值,然后释放它们. 所以, React 保证选择的范围 (例如input) 不会因为执行事务而被干扰 (选中时就执行`initialize` 恢复时就执行`close`). 而且, 它抑制可能由于上层DOM操作所分发的事件(blur/focus) (如临时从DOM中移除一个文本框) 所以它在 `initialize`时  **禁用 `ReactBrowserEventEmitter`**，并在`close`时启用它.

我们就快可以开始组件的挂载了, 它会返回一个标记来插入到DOM. 事实上, `ReactReconciler.mountComponent` 只是个封装器, 或者准确地说是 个中介(‘mediator’). 它向组件模块委托挂载方法.这是个要点，所以着重指出（敲黑板）:

> 在一些**依赖平台**的逻辑的实现，`ReactReconciler` 模块总是会被调用, 就像此例. 挂载在每个平台都是不同的, 所以‘主模块’和`ReactReconciler`通信，然后 `ReactReconciler` 才知道接下来做什么.

接下来，我们聊聊组件的`mountComponent`方法. 你可能已经知道了. 初始化组件, 渲染标记, 并注册事件.  你看, 很多事情，最后我们看到调用组件挂载方法. 然后, 我们就得到可以插入到document里的HTML元素了.


### Alright, we’ve finished *Part 2*.

删繁就简，复习以下内容:

[![](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/2/part-2-A.svg)](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/2/part-2-A.svg)

<em>2.1 Part 2 simplified (clickable)</em>

And we should probably fix spaces and alignment as well:

[![](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/2/part-2-B.svg)](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/2/part-2-B.svg)

<em>2.2 Part 2 simplified & refactored (clickable)</em>

Nice. In fact, that’s all that happens here. So, we can take the essential value from *Part 1* and use it for the final `mounting` scheme:

[![](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/2/part-2-C.svg)](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/2/part-2-C.svg)

<em>2.3 Part 2 essential value (clickable)</em>

And then we're done!


[To the next page: Part 3 >>](./Part-3.md)

[<< To the previous page: Part 1](./Part-1.md)


[Home](../../README.md)
