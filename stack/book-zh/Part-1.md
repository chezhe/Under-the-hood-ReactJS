## Part 1

[![](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/1/part-1.svg)](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/1/part-1.svg)

<em>1.0 Part 1 (clickable)</em>

### Transaction

组件的实例应该以某种方式和React系统 **有关** ，而且, 在上面**有些影响**. 有个叫`ReactUpdates`的模块专门做这件事. 如你所知, **React以数据块(chunk)的形式执行**, 这意味着它收集操作，并把它们**集中**执行. 因为它为所有要处理的条目提供一次**前置条件(pre-condition)** 和 **后置条件(post-conditions)** 而不是每个条目，这样总是比较好的。

那么什么实际上处理前置和后置线程呢? **事务(transaction)**! 对于一些人来说这可能是个陌生的词汇, 或至少对UI来说, 我们从一个简单的例子来讨论它.

休息一下‘通信信道’. 你需要建立个连接, 发消息, 然后关闭连接. 如果你一个接一个地发消息，这可能会很多. 相反,你只能打开连接一次, 发送所有等待状态的消息, 然后关掉连接.


[![](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/1/communication-channel.svg)](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/1/communication-channel.svg)

<em>1.1 Very real example of transaction (clickable)</em>

我们来想的抽象点. 想象发送信息是你想执行的操作，打开/关闭连接是“发送信息”的前置/后置操作. 你如果单独定义了一对打开/关闭连接 ，你就可以在随表用它们了(我们可以称它为封装, 因为它们都封装了行为方法).听起来不错.

回到React. 事务在React中被广泛地应用. 除了封装行为, 事务允许程序重置事务流, 阻塞同时发生的操作，如果事务已经在进程中的话。有很多不同的事务类, 每一种都描述一种特定的行为,但所有都是扩展自`Transaction`模块. 对于某个事务列表事务之间最重要的差异是特定. 封装仅仅是拥有初始化和关闭方法的对象而已.

所以, **关键概念是**:
* 调用封装器.初始化并缓存返回值 (将来可能会被用到)
* 调用事务自己的方法
* 调用wrapper.close

[![](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/1/transaction.svg)](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/1/transaction.svg)

<em>1.2 Transaction implementation (clickable)</em>


来看看React里的**其他事务用例**:
* Preserving the input selection ranges before/after reconciliation restoring selection even in the event of an unexpected error.
* Deactivating events while rearranging the DOM preventing blurs/focuses, while guaranteeing that afterwards, the event system is reactivated.
* Flushing a queue of collected DOM mutations to the main UI thread after a reconciliation takes place in a worker thread.
* Invoking any collected `componentDidUpdate` callbacks after rendering new content.

Well, let’s come back to our exact case.

React使用`ReactDefaultBatchingStrategyTransaction` (1). 前面学过, 关键点是事务是它的封装器. 所以, 我们可以看一下封装器，弄清楚事务到底被定义成什么. 有两个封装器: `FLUSH_BATCHED_UPDATES`, `RESET_BATCHED_UPDATES`. 我们看一下源码:

```javascript
//\src\renderers\shared\stack\reconciler\ReactDefaultBatchingStrategy.js#19
var RESET_BATCHED_UPDATES = {
	  initialize: emptyFunction,
	  close: function() {
		ReactDefaultBatchingStrategy.isBatchingUpdates = false;
	  },
};

var FLUSH_BATCHED_UPDATES = {
	 initialize: emptyFunction,
	 close: ReactUpdates.flushBatchedUpdates.bind(ReactUpdates),
}

var TRANSACTION_WRAPPERS = [FLUSH_BATCHED_UPDATES, RESET_BATCHED_UPDATES];
```

 这个事务没有前置条件. `initialize` 方法是空的, 但是其中一个`close`方法很有趣. 它调用了`ReactUpdates.flushBatchedUpdates`. 这意味着什么? 它通过检验脏数据来重新渲染.我们可以调用挂载方法，并封装在特定事务里，因为挂载后, React检查那些被挂载组件影响的数据并更新它们.

我们来看一下被封装在事务里的方法. 事实上，它只是用到了另一个事务...


### *第一部分*结束.

Let’s recap how we got here. Let's look at the scheme one more time, then remove redundant less important pieces, and it becomes this:

[![](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/1/part-1-A.svg)](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/1/part-1-A.svg)

<em>1.3 Part 1 simplified (clickable)</em>

And we should probably fix spaces and alignment as well:

[![](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/1/part-1-B.svg)](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/1/part-1-B.svg)

<em>1.4 Part 1 simplified & refactored (clickable)</em>

Nice. In fact, that’s all that happens here. So, we can take the essential value from *Part 1* and use it for the final `mounting` scheme:

[![](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/1/part-1-C.svg)](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/1/part-1-C.svg)

<em>1.5 Part 1 essential value (clickable)</em>

And then we're done!


[To the next page: Part 2 >>](./Part-2.md)

[<< To the previous page: Part 0](./Part-0.md)


[Home](../../README.md)
