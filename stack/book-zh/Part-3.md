## Part 3

[![](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/3/part-3.svg)](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/3/part-3.svg)

<em>3.0 Part 3 (clickable)</em>

### Mount

`componentMount`方法是我们学习React的重头戏! 对我们来说，`ReactCompositeComponent.mountComponent`(1)方法就值得关注了.

如果你记得, 我提到**第一个被放进组件树的**是`TopLevelWrapper` (内部React类). 这里, 我们将挂载它. 但是... 它几乎是一个空封装器, 所以, 调试起来肯定很无聊. 它压根不会影响流程, 所以, 我建议先跳过它，去研究它的子节点.

它涉及一个树的挂载到底是怎么工作的,你挂在父组件, 然后它的子节点.... Just believe me, `TopLevelWrapper`挂载后, 它的子节点(`ReactCompositeComponent`, 管理`ExampleApplication` 组件) 将会按照相同的方式处理.

我们回到第一步(1). 看看其本质. 将会有一些重要的指令发生, 我们来具体讨论一下.

### Assigning instance updater

 从`transaction.getUpdateQueue()`返回的`updater` (2),实际是`ReactUpdateQueue` 模块. 那么，为什么 **在这里分配**呢? 因为`ReactCompositeComponent` (我们正在研究的) 可以在各个平台使用, 但是updaters却不同, 所以我们根据平台在挂载时来分配它们.

我们现在并不需要`updater`， 但是需要记住. `updater` 很 **重要**, 它很快就会出现在**`setState`**中.

事实上，不仅仅`updater` 在这期间分配给实例, 组件实例(your custom component)也扩展了`props`, `context`, 和 `refs`.

看一下下面的代码:

```javascript
// \src\renderers\shared\stack\reconciler\ReactCompositeComponent.js#255
// These should be set up in the constructor, but as a convenience for
// simpler class abstractions, we set them up after the fact.
inst.props = publicProps;
inst.context = publicContext;
inst.refs = emptyObject;
inst.updater = updateQueue;
```

这样, 然后你才可以从实例内部获取`props`, 如 `this.props`.

### 创建ExampleApplication实例

调用`_constructComponent` (3) ，执行几次构造方法, 最后 `new ExampleApplication()` 就被创建出来了. 这时我们的构造函数就会被调用. 所以, 这是React系统和我们的代码第一次接触.

### 执行第一次挂载

所以, 我们要完成挂载的话， (4) 这里首先调用`componentWillMount` (当然如果被指定的话). 它是我们遇到的第一个生命周期钩子的方法. 再下边是`componentDidMount`, 但它仅仅是被push进事务队列，因为它不该立刻执行. 当挂载操作完成时，它才会被调用. 此外, 你可能会在`componentWillMount`里调用`setState`. 在那个例子中, 状态当然会被重新计算, 但不会调用`render`方法 (这没有意义, 因为组件还没被挂载呢).

官方文档也是这么说的:

> `componentWillMount()`在挂载发生前立刻被调用. 它在`render()`前调用, 因此在这个方法中设定状态不会触发重新渲染.

为了确保是这样的，我们来看代码 ;)

```javascript
// \src\renderers\shared\stack\reconciler\ReactCompositeComponent.js#476
if (inst.componentWillMount) {
    //..
    inst.componentWillMount();

    // When mounting, calls to `setState` by `componentWillMount` will set
    // `this._pendingStateQueue` without triggering a re-render.
    if (this._pendingStateQueue) {
        inst.state = this._processPendingState(inst.props, inst.context);
    }
}
```

正确. 但当`state`被重新计算时, 我们调用`render`. 我们在自己的组件里就是这样指定的! 所以，再来看一下我们的代码吧.

下一件要做的事是创建一个React组件实例。呃... 什么, 又来一次? 我们已经见过`this._instantiateReactComponent`(5) 调用, 对吧? 但那一次我们是为`ExampleApplication` 组件实例化`ReactCompositeComponent`.现在，我们将为建立在元素上的子节点（我们从`render`方法得到的）创建VDOM 实例. 对于我们的例子来说, 渲染方法返回 `div`, 所以VDOM表现层就是`ReactDOMComponent`. 当实例创建后, 我们再次调用`ReactReconciler.mountComponent`,但这次是作为`internalInstance`, 我们传入一个新建的`ReactDOMComponent`实例.

然后，调用实例的`mountComponent`方法…

### Alright, we’ve finished *Part 3*.

Let’s recap how we got here. Let's look at the scheme one more time, then remove redundant less important pieces, and it becomes this:

[![](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/3/part-3-A.svg)](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/3/part-3-A.svg)

<em>3.1 Part 3 simplified (clickable)</em>

And we should probably fix spaces and alignment as well:

[![](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/3/part-3-B.svg)](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/3/part-3-B.svg)

<em>3.2 Part 3 simplified & refactored (clickable)</em>

Nice. In fact, that’s all that happens here. So, we can take the essential value from *Part 3* and use it for the final `mounting` scheme:

[![](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/3/part-3-C.svg)](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/3/part-3-C.svg)

<em>3.3 Part 3 essential value (clickable)</em>

And then we're done!


[To the next page: Part 4 >>](./Part-4.md)

[<< To the previous page: Part 2](./Part-2.md)


[Home](../../README.md)
