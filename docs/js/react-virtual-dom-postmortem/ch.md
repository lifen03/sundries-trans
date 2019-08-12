## React虚拟DOM原理追踪(动画教程)

> 使用React来构建UI的你需要知道它实际上是如何工作的。

作者：[JavaScript Teacher](https://twitter.com/js_tut) @2019-07-31 [原文地址](https://medium.com/@js_tut/react-animated-tutorial-7a46fa3c2b96)
> 翻译：[林毅](https://www.yuque.com/gzlinyi)   校验：[freedom](https://github.com/yylifen)

### 首次启动一个React <App />

React会自动将App类挂载到根目录的应用程序容器

![Mounting.gif](https://yylifen.github.io/sundries-trans/js/react-virtual-dom-postmortem/assets/1_NBBZYfjeVBfHa1gPnMZJzA.gif)

> 第一次挂载<App />

现在，你的应用程序已挂载到DOM，让我们进一步来研究它的原理。

### 虚拟DOM & Diffing (“Reconciliation”) 算法

> 要构建React应用程序，你可能不需要知道<Really />内部花里胡哨的东西。但是...我还是决定创建一个可视化的教程，让你更好掌握和理解React是如何工作的。

**DIFF**ing算法是寻找两个虚拟DOM之间的差异。稍等，有两个虚拟DOM吗？我认为只有一个，React将之前的虚拟DOM和新生成的虚拟DOM进行比较。只有在检测有更改时，它才会最终更新到浏览器的DOM:

![Diffing.gif](https://yylifen.github.io/sundries-trans/js/react-virtual-dom-postmortem/assets/1_BAleNGsko42ArMZKbsjPRA.gif)

> React的Diffing算法通过抽象动画表示。如果发现这两棵虚拟DOM树是不同的，那么将最新的一个与浏览器中的实际DOM进行协调。

简单说明一下这里发生了什么

使用**POST**数据调用**API.tweet()**，其中包含单击事件上的消息。**有效数据**将在`(Event)=>{…}`中发送和接收回调。如果**返回的有效数据**导致**props**发生变化，DOM树将被**DIFF**算法更新。如果这两个虚拟DOM树是不同的，那么最近的一个会被发送到浏览器。

然后这个新的虚拟DOM就变成了旧的，我们在等待新的DOM到来。

### React组件

React组件不过是一个JavaScript对象。React以二叉树的形式表示整个UI结构的方式创建自己的虚拟DOM。它将虚拟DOM树保存到内存中，多次添加、更新和删除各种组件，最终在实际浏览器中被渲染出来，浏览器也就更新了DOM和UI。

不要将`render()`用于呈现用户界面以外的任何内容。如果需要更新状态或者属性，那必须使用React内置的生命周期函数。

### `render()`应该始终是一个纯函数

`render()`函数的作用是：更新虚拟DOM组件。如果这个新的虚拟DOM树和之前渲染的虚拟DOM树不同，那react也会更新浏览器中实际的DOM。你不需要直接在任何地方更新浏览器的DOM - 特别是不能从`render()`函数中更新。

![image.png](https://yylifen.github.io/sundries-trans/js/react-virtual-dom-postmortem/assets/1_ixymleyewAuYn8WsPOV7Ng.png)

> 不要使用以某种方式直接更新DOM，会污染`render()`函数。

你最好不要在`render()`函数中进行http请求、jQuery、Ajax或fetch调用来改变**state**（即使使用**setstate**），因为它应该是纯的。渲染函数始终作为最后一步执行，只是为了所有虚拟DOM更新后更新UI的。

### 生命周期事件
当组件首次挂载到DOM时，React会创建**componentWillMount**事件。在浏览器的DOM中实际挂载（第一次被渲染）虚拟组件之后，将执行另一个名为**componentDidMount**的生命周期事件。

在整个应用程序的生命周期中，你需要在这些生命周期事件方法中编写组件的大部分逻辑。

### 下一个动画教程：Hooks？

我认为这是一个虚拟DOM和生命周期函数的原理分析教程。

许多开发者已经开始学习Hooks。根据React的文档，组件生命周期事件甚至被认为是不安全的，并且将来可能会过时。制作这些动画仍然很有趣， 我敢肯定，对于那些刚接触React的人或者对其发展历史感兴趣的人，至少会踩几次坑。

Hooks我也正在学习。希望有一天我会制作Hooks的动画教程！在此之前，保持联系！可以通过一下渠道关注我：

* @Twitter [https://twitter.com/js_tut](https://twitter.com/js_tut)
* @Instagram [https://www.instagram.com/javascriptteacher/](https://www.instagram.com/javascriptteacher/)
* @Facebook [https://www.facebook.com/javascriptteacher/](https://www.facebook.com/javascriptteacher/)
