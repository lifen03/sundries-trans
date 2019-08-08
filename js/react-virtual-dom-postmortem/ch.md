# React Virtual DOM原理追踪

> 使用React来构建UI并且需要知道它实际上是如何工作的。

<a name="wh1Ab"></a>
## 首次启动一个React <App />
React会自动将App类挂载到根目录的应用程序容器

![Mounting.gif](https://cdn.nlark.com/yuque/0/2019/gif/244679/1565182695858-69f8baf7-0eb1-4ec7-b532-aa2fa7180076.gif#align=left&display=inline&height=576&name=Mounting.gif&originHeight=576&originWidth=1332&size=697319&status=done&width=1332)<br />第一次挂载<App /><br />
<br />现在您的应用程序已安装到DOM，让我们深入了解。<br />

<a name="KoABC"></a>
## Virtual DOM & Diffing (“Reconciliation”) 算法
> 要构建React应用程序，您可能不需要知道<Really />内部的花里胡哨。但是...我决定创建一个动画教程，让你更好掌握和理解React是怎样运行。


DIFFing算法是寻找两个virtual DOM 之间的差异。等待。两个virtual DOM?我以为只有一个，好吧，React将之前的virtual DOM和新生成的virtual DOM进行比较。只有在检测有更改时，它才会最终更新到浏览器的DOM:

![Diffing.gif](https://cdn.nlark.com/yuque/0/2019/gif/244679/1565182707532-f9714976-e7b0-4d9e-9fcb-60f9d5ac61d7.gif#align=left&display=inline&height=660&name=Diffing.gif&originHeight=660&originWidth=1332&size=342739&status=done&width=1332)<br />React的Diffing算法的抽象动画。<br />如果发现两个virtual DOM树不同，则最新的树与浏览器中的实际DOM进行协调。

简单说明一下：<br />调用api.tweet()时使用包含单击事件消息的post数据。<br />有效负载将在（event）=>…回调中发送和接收。<br />如果返回的有效负载数据会导致props更改，则树会有所不同。<br />如果两个virtual DOM树都不同，则最新的一个将发送到浏览器。

然后这个新的虚拟DOM就变成了旧的，我们在等待新的DOM的到来。

<a name="izovF"></a>
## React 组件
React组件它是一个JavaScript对象。React创建自己的virtual DOM，它以二叉树的形式表示整个UI结构。它将virtual DOM树保存到内存中，多次添加、更新和删除各种组件，最终在实际浏览器中呈现，浏览器更新了DOM和UI。

不要将render（）用于呈现用户界面以外的任何内容。如果需要更改状态或属性，则必须使用React的内置生命周期方法。

<a name="Ee017"></a>
## render() 应始终保持纯函数
函数的作用是：更新virtual DOM组件。如果这个新的virtual DOM树与以前呈现的virtual DOM树不同，那么react也将更新实际浏览器的DOM。在任何地方直接更新浏览器的DOM都不是你的工作 - 特别是不能从render()函数中更新。

![image.png](https://cdn.nlark.com/yuque/0/2019/png/244679/1565170060587-7766ad64-f3fc-4745-9415-569edb4743a1.png#align=left&display=inline&height=200&name=image.png&originHeight=400&originWidth=1376&size=13062&status=done&width=688)<br />不要使用以某种方式直接更新DOM的函数污染render（）方法。

您不应该更改状态（即使使用setstate），在render（）中进行http请求，jQuery，Ajax或fetch调用，因为它应该是纯的。渲染函数始终作为最后一步执行，只是为了更新UI，假设所有virtual DOM更新都已执行。

<a name="TR4NN"></a>
## 生命周期事件
当组件首次挂载到DOM时，React会创建componentWillMount事件。 在实际浏览器的DOM中实际挂载（第一次呈现）虚拟组件之后，将执行另一个名为componentDidMount的生命周期事件。

在整个应用程序的生命周期中，您需要在这些生命周期事件方法中编写组件的大部分逻辑。

<a name="4IPR4"></a>
## 下一个动画钩子？
我认为本教程是一个Virtual DOM和生命周期后期分析。

许多开发人员已经转向钩子。 根据React docs，组件生命周期事件甚至被认为是不安全的，并且将来可能会过时。 制作这些动画仍然很有趣， 我敢肯定，对于那些刚接触React的人或者对其发展历史感兴趣的人，至少会踩几次坑。

钩子是我仍在学习的东西。 希望有一天我会制作动画钩子教程！ 在此之前，保持联系！
