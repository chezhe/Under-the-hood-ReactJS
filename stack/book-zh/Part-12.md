## Part 12

[![](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/12/part-12.svg)](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/12/part-12.svg)

<em>12.0 Part 12 (clickable)</em>

### If components actually should update..

这只是更新的开始, 也就是说该调用 `componentWillUpdate` 钩子(1). 然后, 重新渲染组件并把`componentDidUpdate`方法入列 (延缓这个调用, 它应该在更新的最后才调用).
重新渲染是怎么回事? 事实上, 在这里要做的就是调用`render` 方法 并据此更新DOM. 所以, 第一步, 从实例(`ExampleApplication`)内部调用`render`(2) 并保存渲染结果(方法返回的React元素). 然后, 和前一次的元素比较, 看DOM是否该更新.

它是React的杀手锏之一, 它避免了多余的DOM更新, 使React运行流畅.
根据`shouldUpdateReactComponent`(3)方法的注释:
> ‘determines if the existing instance should be updated as opposed to being destroyed or replaced by a new instance’.

粗略地说, 这个方法检查元素是否该被完全地替换, 旧元素应该先执行`unmounted`, 然后新元素 (从`render`得到)应该被挂载和标记, 从`mount`方法获取, 应该取代当前元素, 或者, 如果被部分更新的话. 完全替换元素的最主要原因是，当新元素是空的(根据`render`逻辑被删除)它的类型不同, 例如原来是`div`，但现在是别的标签. 看一下源码.

```javascript
///src/renderers/shared/shared/shouldUpdateReactComponent.js#25

function shouldUpdateReactComponent(prevElement, nextElement) {
    var prevEmpty = prevElement === null || prevElement === false;
    var nextEmpty = nextElement === null || nextElement === false;
    if (prevEmpty || nextEmpty) {
        return prevEmpty === nextEmpty;
    }

    var prevType = typeof prevElement;
    var nextType = typeof nextElement;
    if (prevType === 'string' || prevType === 'number') {
        return (nextType === 'string' || nextType === 'number');
    } else {
        return (
            nextType === 'object' &&
            prevElement.type === nextElement.type &&
            prevElement.key === nextElement.key
        );
    }
}
```

在我们的例子里，`ExampleApplication` 仅仅更新的`state`熟悉 不会这样影响`render`， 来看看第二个场景, `update`.

### Alright, we’ve finished *Part 12*.

Let’s recap how we got here. Let's look at the scheme one more time, then remove redundant less important pieces, and it becomes this:

[![](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/12/part-12-A.svg)](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/12/part-12-A.svg)

<em>12.1 Part 12 simplified (clickable)</em>

And we should probably fix spaces and alignment as well:

[![](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/12/part-12-B.svg)](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/12/part-12-B.svg)

<em>12.2 Part 12 simplified & refactored (clickable)</em>

Nice. In fact, that’s all that happens here. So, we can take the essential value from *Part 12* and use it for the final `updating` scheme:

[![](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/12/part-12-C.svg)](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/12/part-12-C.svg)

<em>12.3 Part 12 essential value (clickable)</em>

And then we're done!


[To the next page: Part 13 >>](./Part-13.md)

[<< To the previous page: Part 11](./Part-11.md)


[Home](../../README.md)
