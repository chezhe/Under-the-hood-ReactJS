## Part 8

[![](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/8/part-8.svg)](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/8/part-8.svg)

<em>8.0 Part 8 (clickable)</em>

### `this.setState`

我们知道了挂载的工作原理, 但是现在, 我们从另一个角度来讲. 没错, `setState` 方法, 小菜一碟!

首先, 为什么我们可以调用叫`setState`的方法? 再清楚不过了, 我们的组件继承自`ReactComponent`. 在React源码里找到这个类，查看`setState`方法.

```javascript
//src\isomorphic\modern\class\ReactComponent.js#68
this.updater.enqueueSetState(this, partialState)
```
如你所见, 有好几个`updater`接口. `updater`是什么?如果你查看我们分析过的挂载进程, 在`mountComponent`期间, instance receives `updater` property as a  reference to `ReactUpdateQueue` (`src\renderers\shared\stack\reconciler\ReactUpdateQueue.js`).

深入 `enqueueSetState` (1) 方法会看到, 它首先把内部实例(internal instance)的局部状态(即传入`this.setState`的对象)推入`_pendingStateQueue` (2) (just to remind: 公共实例, 也就是自定义组件`ExampleApplication` and, 内部实例`ReactCompositeComponent`是在挂载期间被创建的), 第二, we `enqueueUpdate`, 会检查更新是否正在进行，并把组件推入`dirtyComponents`列表, 否则, 初始化更新事务并把组件推入 `dirtyComponents` 列表.

总结一下, 每个组件有个pending states, 这意味着, 每次在一个事务里调用`setState`, 你只要把对象推入队列里, 然后, 过一会儿, 它们就会一个接一个地合并到组件状态里. 而且，当你调用`setState`时, 组件会被推入`dirtyComponents` 列表. 可能你会好奇, `dirtyComponents` 将会被怎么处理呢? 那是另一个重要议题...

### Alright, we’ve finished *Part 8*.

Let’s recap how we got here. Let's look at the scheme one more time, then remove redundant less important pieces, and it becomes this:

[![](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/8/part-8-A.svg)](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/8/part-8-A.svg)

<em>8.1 Part 8 simplified (clickable)</em>

And we should probably fix spaces and alignment as well:

[![](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/8/part-8-B.svg)](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/8/part-8-B.svg)

<em>8.2 Part 8 simplified & refactored (clickable)</em>

Nice. In fact, that’s all that happens here. So, we can take the essential value from *Part 8* and use it for the final `updating` scheme:

[![](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/8/part-8-C.svg)](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/8/part-8-C.svg)

<em>8.3 Part 8 essential value (clickable)</em>

And then we're done!


[To the next page: Part 9 >>](./Part-9.md)

[<< To the previous page: Part 7](./Part-7.md)


[Home](../../README.md)
