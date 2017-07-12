## 简介

### 图, 初探


[![](../images/intro/all-page-stack-reconciler-25-scale.jpg)](../images/intro/all-page-stack-reconciler.svg)

<em>Intro.0 All scheme (clickable)</em>

所以... 看一下. 花点时间. 总体上看起来复杂些, 但事实上, 它只描述了两个过程: 挂载(mount) 和 更新(update). 我忽略卸载(unmount)，简化结构， 因为它看上去就像“逆挂载” . 而且, **这并不是百分之百** 代码, 而大部分是关于架构的. 总的来说, 大概有60%的代码, but the other 40% would bring little visual value. 所以，简而言之，我忽略了那部分.

乍一看, 你可能注意到在图里有很多颜色. 每一个逻辑单元 (图上的各种形状) 使用其父模块的颜色标注出来. 例如, 从`模块B`调用`方法A`时，会使用`模块B`的颜色. 下面就是对于在图中每个文件路径的图例.

[![](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/7c2372e1/stack/images/intro/modules-src-path.svg)](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/7c2372e1/stack/images/intro/modules-src-path.svg)

<em>Intro.1 Modules colors (clickable)</em>

我们来把 **模块之间的相互依赖**放到图里试试.

[![](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/7c2372e1/stack/images/intro/files-scheme.svg)](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/7c2372e1/stack/images/intro/files-scheme.svg)

<em>Intro.2 Modules dependencies (clickable)</em>

可能如你所知, React **支持很多环境**. 
- 手机 (**ReactNative**)
- 浏览器 (**ReactDOM**)
- 服务器端渲染(Server Rendering)
- **ReactART** (用来画矢量图)
- 等等.

因此, 其文件的数量要远大于上图. 下图比上图多了平台相关的信息.

[![](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/7c2372e1/stack/images/intro/modules-per-platform-scheme.svg)](https://rawgit.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/7c2372e1/stack/images/intro/modules-per-platform-scheme.svg)

<em>Intro.3 Platform dependencies (clickable)</em>

如你所见, 一些条目重复了. 这意味着一些功能在不同平台的实现. 我们来看一下ReactEventListener. 显然, 它的实现在不同平台是不一样的! 从技术上来说, 这些依赖于平台的模块或多或少和当前逻辑流程相关, 事实上, 有很多很多注入器. 因为其使用方法是标准组合模式的一部分, 我选择忽略这部分. 当然,还是为了简化教程.

让我们来学习**React DOM**在**普通浏览器**里的逻辑流程吧. 这是最常见的环境 而且完全涉及到React架构的理念. 很好，开始吧!


### Code sample

什么是学习一个框架或者库源代码的最好的方式? 没错, 那就是阅读和调试代码. 好了, 我们来调试 **两个方法**: **ReactDOM.render** 和 **component.setState**, 这些涉及到mount 和 update. 我们来看看下面的代码. 我们需要什么? 可能只是几个简单渲染组件, 这样方便调试.

```javascript
class ChildCmp extends React.Component {
    render() {
        return <div> {this.props.childMessage} </div>
    }
}

class ExampleApplication extends React.Component {
    constructor(props) {
        super(props);
        this.state = {message: 'no message'};
    }

    componentWillMount() {
        //...
    }

    componentDidMount() {
        /* setTimeout(()=> {
            this.setState({ message: 'timeout state message' });
        }, 1000); */
    }

    shouldComponentUpdate(nextProps, nextState, nextContext) {
        return true;
    }

    componentDidUpdate(prevProps, prevState, prevContext) {
        //...
    }

    componentWillReceiveProps(nextProps) {
        //...
    }

    componentWillUnmount() {
        //...
    }

    onClickHandler() {
        /* this.setState({ message: 'click state message' }); */
    }

    render() {
        return <div>
            <button onClick={this.onClickHandler.bind(this)}> set state button </button>
            <ChildCmp childMessage={this.state.message} />
            And some text as well!
        </div>
    }
}

ReactDOM.render(
    <ExampleApplication hello={'world'} />,
    document.getElementById('container'),
    function() {}
);
```

现在，我们准备开始吧. 来看图的第一部分. 一个接一个地, 我们完整地研究它.

[To the next page: Part 0 >>](./Part-0.md)


[Home](../../README.md)