## Part 14

[![](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/14/part-14.svg)](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/14/part-14.svg)

<em>14.0 Part 14 (clickable)</em>

### 最后一部分!

这个方法协调影响子节点内容的各种属性. 有几种可能的场景, 但从技术上来说只有两种. 子节点仍然是‘复杂类型（complex）’(就是说, 它们是React组件，并且，React应该递归几次它们的图层直到内容那个层面上), 或者, 子节点是简单类型, 字符串或数字(内容).

开关是一种`nextProps.children`(1)的类型, 对于我们的例子来说, `ExampleApplication` 组件有三个子节点: `button`, `ChildCmp` and `text string`.

第一次迭代`ExampleApplication children`. 显然, 子节点不是‘内容’, 所以当做复杂类型. 我们把所有子节点一个个地按照处理其父节点的方式来处理. 另外, 限制`shouldUpdateReactComponent`(2)的验证可能会引发疑问, 看起来验证是为了检查是否更新, 但事实是, 它检查更新、删除和创建 (我们跳过细枝末节). 之后, 比较旧的和当前的子节点, 如果一些子节点被删除了, 我们卸载组件并删除.

[![](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/14/children-update.svg)](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/14/children-update.svg)

<em>14.1 Children update (clickable)</em>

第二次迭代, 处理`button`, 这就简单了, 因为按钮的`children` 就是‘文本’, 因为按钮只有个标题‘set state button’. 然后检查原来的按钮的文本是否相同, 好, 文本没变, 那么我们不需要更新`button`了? 有道理. 所以, ‘VirtualDOM things’在运行中. 现在听起来不是那么抽象了, 如果有必要，React维护DOM内部，处理真正的DOM. 
你应该想到了, 然后检查`ChildCmp`和其子节点直到最底层(content).它的内容变了, 当`click`时调用`setState`，`this.props.message`被'click state message'更新了.

```javascript
//...
onClickHandler() {
	this.setState({ message: 'click state message' });
}

render() {
    return <div>
		<button onClick={this.onClickHandler.bind(this)}>set state button</button>
		<ChildCmp childMessage={this.state.message} />
//...

```

我们将会更新元素的内容, 事实上是替换. 那么到底什么被更新了呢? So, 配置对象将被解析和配置的行为将被应用. 本例应该是这样的:

```javascript
{
  afterNode: null,
  content: "click state message",
  fromIndex: null,
  fromNode: null,
  toIndex: null,
  type: "TEXT_CONTENT"
}
```
它几乎是空的, 只有文本更新的例子太简单了. 你看, 有很多属性, 它可能很复杂.

看方法代码, 加深印象.

```javascript
//src\renderers\dom\client\utils\DOMChildrenOperations.js#172
processUpdates: function(parentNode, updates) {
    for (var k = 0; k < updates.length; k++) {
      var update = updates[k];

      switch (update.type) {
        case 'INSERT_MARKUP':
          insertLazyTreeChildAt(
            parentNode,
            update.content,
            getNodeAfter(parentNode, update.afterNode)
          );
          break;
        case 'MOVE_EXISTING':
          moveChild(
            parentNode,
            update.fromNode,
            getNodeAfter(parentNode, update.afterNode)
          );
          break;
        case 'SET_MARKUP':
          setInnerHTML(
            parentNode,
            update.content
          );
          break;
        case 'TEXT_CONTENT':
          setTextContent(
            parentNode,
            update.content
          );
          break;
        case 'REMOVE_NODE':
          removeChild(parentNode, update.fromNode);
          break;
      }
    }
  }
```

我们的例子是‘TEXT_CONTENT’，而且是最后一步, 调用`setTextContent` (3) 修改HTML节点内容( 真实的HTML, 通过DOM).

内容变了，页面也重新渲染了. 少了点什么? 一切都准备好了, 所以组件的钩子`componentDidUpdate`将会被调用. 被延期的? 没错, 通过事务封装器. 脏组件会通过`ReactUpdatesFlushTransaction`封装, 其中之一包含 了`this.callbackQueue.notifyAll()`, 所以它会调用`componentDidUpdate`!

看起来完全结束了.

### Alright, we’ve finished *Part 14*.

Let’s recap how we got here. Let's look at the scheme one more time, then remove redundant less important pieces, and it becomes this:

[![](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/14/part-14-A.svg)](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/14/part-14-A.svg)

<em>14.2 Part 14 simplified (clickable)</em>

And we should probably fix spaces and alignment as well:

[![](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/14/part-14-B.svg)](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/14/part-14-B.svg)

<em>14.3 Part 14 simplified & refactored (clickable)</em>

Nice. In fact, that’s all that happens here. So, we can take the essential value from *Part 14* and use it for the final `updating` scheme:

[![](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/14/part-14-C.svg)](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/14/part-14-C.svg)

<em>14.4 Part 14 essential value (clickable)</em>

And then we're done! In fact, we're done with updating. Let's see it below!

[![](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/14/updating-parts-C.svg)](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/14/updating-parts-C.svg)

<em>14.5 Updating (clickable)</em>

[<< To the previous page: Part 13](./Part-13.md)


[Home](../../README.md)
