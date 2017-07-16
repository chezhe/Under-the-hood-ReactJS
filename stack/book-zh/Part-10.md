## Part 10

[![](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/10/part-10.svg)](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/10/part-10.svg)

<em>10.0 Part 10 (clickable)</em>

### Dirty components

如你所见, React遍历`dirtyComponents`(1), 并调用 `ReactUpdates.runBatchedUpdates`(2), 通过事务! 事务? 

事务的类型是`ReactUpdatesFlushTransaction`, 我之前提到过, 我们需要查看`wrappers` 来理解事务做什么. 代码注释有个提示:
> ‘ReactUpdatesFlushTransaction's wrappers will clear the dirtyComponents array and perform any updates enqueued by mount-ready handlers (i.e., componentDidUpdate)’

总之, 我们得证明. 有两个封装器`NESTED_UPDATES` 和 `UPDATE_QUEUEING`. 在 `initialize` 时，我们保存`dirtyComponentsLength` (3) ,以便于在 `close`检查, React比较, maybe during updates a flush number of dirty components was changed, so, 显然需要再次运行 `flushBatchedUpdates`. 你看, 没什么魔法, 每件事都很直白.

下面是见证奇迹的时刻.
 `ReactUpdatesFlushTransaction` 覆盖了 `Transaction.perform` 方法, 因为需要来自`ReactReconcileTransaction`(在挂载时使用，并保证app状态的安全)的行为. 所以, 在 `ReactUpdatesFlushTransaction.perform` 方法内部, `ReactReconcileTransaction` 也被调用了,所以事务方法再次被封装.

S所以, 技术上, 看起来是这样的:

```javascript
[NESTED_UPDATES, UPDATE_QUEUEING].initialize()
[SELECTION_RESTORATION, EVENT_SUPPRESSION, ON_DOM_READY_QUEUEING].initialize()

method -> ReactUpdates.runBatchedUpdates

[SELECTION_RESTORATION, EVENT_SUPPRESSION, ON_DOM_READY_QUEUEING].close()
[NESTED_UPDATES, UPDATE_QUEUEING].close()
```

最后，我们返回到事务上去,再次看它是怎么工作的, 但现在, 我们看看`ReactUpdates.runBatchedUpdates`(2) (`\src\renderers\shared\stack\reconciler\ReactUpdates.js#125`)的内部细节。

第一件我们应该从头开始 - 给 `dirtyComponets` 数组 (4)排序. 怎么排序呢? 通过 `mount order` (在实例挂载的时候被设置的整形数值), 这意味着父节点 (它们先被挂载) 将先被更新, 然后是子节点,以此类推.
下一步, 我们增加`updateBatchNumber`, 就像是本轮调节的ID. 根据源码的注释:
> ‘Any updates enqueued while reconciling must be performed after this entire batch. Otherwise, if dirtyComponents is [A, B] where A has children B and C, B could update twice in a single batch if C's render enqueues an update to B (since B would have already updated, we should skip it, and the only way we can know to do so is by checking the batch counter).’

这种方法有助于避免同一个组件多次更新.

最后遍历`dirtyComponents` ，把每个组件传入`ReactReconciler.performUpdateIfNecessary` (5), 事实上 `ReactCompositeComponent` 实例将会调用`performUpdateIfNecessary`, 所以，再回到`ReactCompositeComponent`代码和`updateComponent`方法. 在这里你会发现有趣的事情, 所以, 我们深挖一点.

### Alright, we’ve finished *Part 10*.

Let’s recap how we got here. Let's look at the scheme one more time, then remove redundant less important pieces, and it becomes this:

[![](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/10/part-10-A.svg)](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/10/part-10-A.svg)

<em>10.1 Part 10 simplified (clickable)</em>

And we should probably fix spaces and alignment as well:

[![](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/10/part-10-B.svg)](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/10/part-10-B.svg)

<em>10.2 Part 10 simplified & refactored (clickable)</em>

Nice. In fact, that’s all that happens here. So, we can take the essential value from *Part 10* and use it for the final `updating` scheme:

[![](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/10/part-10-C.svg)](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/10/part-10-C.svg)

<em>10.3 Part 10 essential value (clickable)</em>

And then we're done!


[To the next page: Part 11 >>](./Part-11.md)

[<< To the previous page: Part 9](./Part-9.md)


[Home](../../README.md)
