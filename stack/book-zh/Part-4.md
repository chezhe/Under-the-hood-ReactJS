## Part 4

[![](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/4/part-4.svg)](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/4/part-4.svg)

<em>4.0 Part 4 (clickable)</em>

### Child mounting

很神奇, 对不对? 我们继续探讨 `mount` 方法.

所以, 如果 `_tag` 包含一个‘complex’标签 (1), 如 video, form, textarea, 等等., 它就需要额外的封装. 它为每个媒体事件添加监听器, 如 `audio`标签的‘音量变化’, 或者仅仅封装`select`, `textarea`, 标签的原生行为.
有很多这样的元素封装器, 例如 `ReactDOMSelect` 和 `ReactDOMTextarea` (在 src\renderers\dom\client\wrappers\ 文件夹下). 我们的例子只有简单的 `div`,没有额外的加工.

### 验证Props

下一步要调用的验证方法只是为了保证传递的`props`是正确的（有效的）, 否则就会抛出异常. 例如, 如果`props.dangerouslySetInnerHTML`被设置了 (通常我们这么做是为了以字符串的形式插入HTML) 和 对象的`__html`键缺少了, 下面的异常就会抛出:

> `props.dangerouslySetInnerHTML` must be in the form `{__html: ...}`.  Please visit https://fb.me/react-invariant-dangerously-set-inner-html for more information.

### Create HTML element

那么, 真正的HTML元素是由`document.createElement`创建的(3), 也就是为我们实例化HTML `div`. 在我们和虚拟显示层工作之前和之后, 你才第一次看到它.


### Alright, we’ve finished *Part 4*.

复习以下. Let's look at the scheme one more time, then remove redundant less important pieces, and it becomes this:

[![](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/4/part-4-A.svg)](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/4/part-4-A.svg)

<em>4.1 Part 4 simplified (clickable)</em>

And we should probably fix spaces and alignment as well:

[![](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/4/part-4-B.svg)](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/4/part-4-B.svg)

<em>4.2 Part 4 simplified & refactored (clickable)</em>

Nice. In fact, that’s all that happens here. So, we can take the essential value from *Part 4* and use it for the final `mounting` scheme:

[![](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/4/part-4-C.svg)](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/4/part-4-C.svg)

<em>4.3 Part 4 essential value (clickable)</em>

And then we're done!


[To the next page: Part 5 >>](./Part-5.md)

[<< To the previous page: Part 3](./Part-3.md)


[Home](../../README.md)
