## 为什么需要React Hooks?

作者：[Tyler McGinnis](https://twitter.com/tylermcginnis) @2019-07-30  翻译&校验：[freedom](https://github.com/yylifen)

<iframe width="864" height="486" src="https://yylifen.github.io/sundries-trans/js/why-react-hooks/assets/why-react-hooks.mp4" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

### 前言

想学到新东西的时候，你首先要问自己两个问题-

* 1.为什么会存在这个东西？

* 2.这个东西能解决什么问题？

如果你从来没有为这两个问题找到一个满意的答案，那在深入研究细节时，你就建立不了足够扎实的基础。这些问题在[React Hooks](https://tylermcginnis.com/courses/react-hooks/)上特别有趣。发布Hooks时，React已经是JavaScript生态系统中[最受欢迎的前端框架](https://insights.stackoverflow.com/survey/2019)。尽管人们对此已经赞不绝口，React团队仍然认为有必要开发和发布Hooks。在各种媒体和博客文章中宣传Hooks的原因是要解决两个问题：(1)**为什么**在React已经广受好评的时候，React团队还是决定花这么多宝贵的资源来开发和发布Hooks，它解决了什么问题；(2)它能带来什么**好处**。为了更好地理解这两个问题的答案，首先我们需要更深入地了解之前是如何编写React应用程序的。

### createClass

如果你很早就开始使用React，那么你一定记得API`React.createClass`。这是我们最初创建React组件的方式。用于描述组件的所有信息都将作为对象传递给`createClass`。

```JavaScript
const ReposGrid = React.createClass({
  getInitialState () {
    return {
      repos: [],
      loading: true
    }
  },
  componentDidMount () {
    this.updateRepos(this.props.id)
  },
  componentDidUpdate (prevProps) {
    if (prevProps.id !== this.props.id) {
      this.updateRepos(this.props.id)
    }
  },
  updateRepos (id) {
    this.setState({ loading: true })

    fetchRepos(id)
      .then((repos) => this.setState({
        repos,
        loading: false
      }))
  },
  render() {
    const { loading, repos } = this.state

    if (loading === true) {
      return <Loading />
    }

    return (
      <ul>
        {repos.map(({ name, handle, stars, url }) => (
          <li key={name}>
            <ul>
              <li><a href={url}>{name}</a></li>
              <li>@{handle}</li>
              <li>{stars} stars</li>
            </ul>
          </li>
        ))}
      </ul>
    )
  }
})
```
[代码在线编辑](https://codesandbox.io/s/why-react-hooks-createclass-iw6s5)

`createClass`是一种创建React组件的简单且有效的方法。最初使用API`createClass`的原因是，当时JavaScript没有内置的类系统。当然，这个局面最终改变了。在ES6中，JavaScript引入了class关键字，并使用它提供了在JavaScript中创建类的原生方式。这使得React处境尴尬。要么继续使用`createClass`，拒绝追随JavaScript的发展，要么服从ECMAScript标准，接受类。历史证明，他们选择了后者。

### React.Component

> 我们认为我这不是在设计类系统。我们只是想统一使用惯用的JavaScript方式创建类。- [React在v0.13.0版本中发布](https://reactjs.org/blog/2015/01/27/react-v0.13.0-beta-1.html)

React v0.13.0引入了API`React.Component`，它允许你从现在开始用原生JavaScript类创建React组件。这是一个壮举，因为它更好地支持ECMAScript标准。

```JavaScript
class ReposGrid extends React.Component {
  constructor (props) {
    super(props)

    this.state = {
      repos: [],
      loading: true
    }

    this.updateRepos = this.updateRepos.bind(this)
  }
  componentDidMount () {
    this.updateRepos(this.props.id)
  }
  componentDidUpdate (prevProps) {
    if (prevProps.id !== this.props.id) {
      this.updateRepos(this.props.id)
    }
  }
  updateRepos (id) {
    this.setState({ loading: true })

    fetchRepos(id)
      .then((repos) => this.setState({
        repos,
        loading: false
      }))
  }
  render() {
    if (this.state.loading === true) {
      return <Loading />
    }

    return (
      <ul>
        {this.state.repos.map(({ name, handle, stars, url }) => (
          <li key={name}>
            <ul>
              <li><a href={url}>{name}</a></li>
              <li>@{handle}</li>
              <li>{stars} stars</li>
            </ul>
          </li>
        ))}
      </ul>
    )
  }
}
```
[代码在线编辑](https://codesandbox.io/s/why-react-hooks-reactcomponent-xdhg5)

虽然朝着正确的方向迈出了明确的一步，但`React.Component`并不是没有事先权衡过利弊。

#### 构造函数`constructor`

使用Class组件，可以将`constructor`函数方法中组件的状态初始化为实例(`this`)上的`state`属性。但是，根据ECMAScript规范，如果你要扩展一个子类(在本例中是`React.Component`)，你必须先调用`super`才能使用它。具体来说，在使用React时，你还必须记住将`props`传递给`super`。

```JavaScript
constructor (props) {
  super(props) // 🤮

  ...
}
```

#### 自动绑定

当使用`createClass`时，React将自动地将所有方法绑定到组件的实例，如下所示。对于`React.Component`就不是这样的。很快，[各地的React开发者](https://stackoverflow.com/questions/32317154/react-uncaught-typeerror-cannot-read-property-setstate-of-undefined)都意识到他们不知道[`this`关键字](https://tylermcginnis.com/this-keyword-call-apply-bind-javascript/)是如何工作的。与其使用“只起作用”的方法调用，还不如在类的构造函数(`constructor`)中记住`.bind`方法。如果没有，就会出现流行的“无法读取未定义的属性集状态”错误。

```JavaScript
constructor (props) {
  ...

  this.updateRepos = this.updateRepos.bind(this) // 😭
}
```

我知道你现在在想什么。首先，这些问题是相当简单的。虽然调用`super(props)`并记得使用`bind`绑定你的方法有点烦人，但是这里也没错。其次，与JavaScript类的设计方式相比，这些甚至是一个与React无关的问题。这两种方式都是有效的。然而，我们作为开发者。即使是最简单的问题，你每天如果要处理20次，也会成为一个讨厌的问题。幸运的是，在从`createClass`切换到`React.Component`之后不久，有人提出了[Class Field](https://tylermcginnis.com/javascript-private-and-public-class-fields/)的提案。

#### Class Field

Class Field允许你将实例属性作为类上的属性直接添加，而不必使用构造函数`constructor`。这对我们来说意味着，有了Class Field，我们之前讨论过的两个“肤浅的”问题都会得到解决。我们不再需要使用构造函数`constructor`来设置组件的初始状态，也不再需要在构造函数中`constructor`使用`.bind`绑定，因为我们可以使用箭头函数。

```JavaScript
class ReposGrid extends React.Component {
  state = {
    repos: [],
    loading: true
  }
  componentDidMount () {
    this.updateRepos(this.props.id)
  }
  componentDidUpdate (prevProps) {
    if (prevProps.id !== this.props.id) {
      this.updateRepos(this.props.id)
    }
  }
  updateRepos = (id) => {
    this.setState({ loading: true })

    fetchRepos(id)
      .then((repos) => this.setState({
        repos,
        loading: false
      }))
  }
  render() {
    const { loading, repos } = this.state

    if (loading === true) {
      return <Loading />
    }

    return (
      <ul>
        {repos.map(({ name, handle, stars, url }) => (
          <li key={name}>
            <ul>
              <li><a href={url}>{name}</a></li>
              <li>@{handle}</li>
              <li>{stars} stars</li>
            </ul>
          </li>
        ))}
      </ul>
    )
  }
}
```
[代码在线编辑](https://codesandbox.io/s/why-react-hooks-class-fields-9tbis)

所以现在我们没事了吗？很遗憾还不是。从`createClass`到`React.Component`带来了一些利弊的权衡，正如我们所看到的，Class Field负责处理了这些问题。不幸的是，在我们所见的所有之前的版本中，仍然存在一些更深刻(但较少讨论)的问题。

React的整体想法是，通过将应用程序拆分为不同的组件（然后可以组合在一起）来更好地管理应用程序的复杂性。这种组件模型使React如此优雅。这是React的初衷。但问题并不在组件模型中，而是在于如何实现组件模型。

### 重复逻辑

从历史上看，我们构建的React组件一直被耦合到组件的生命周期中。这一鸿沟自然迫使我们在组件中撒下相关的逻辑。我们可以在我们一直使用的`ReposGrid`示例中清楚地看到这一点。为了保持`repos`与任何`pros.id`同步，我们需要三个独立的方法(`componentDidMount`、`ComponentDidUpdate`和`updateRepos`)来完成相同的任务。

```JavaScript
  componentDidMount () {
    this.updateRepos(this.props.id)
  }
  componentDidUpdate (prevProps) {
    if (prevProps.id !== this.props.id) {
      this.updateRepos(this.props.id)
    }
  }
  updateRepos = (id) => {
    this.setState({ loading: true })

    fetchRepos(id)
      .then((repos) => this.setState({
        repos,
        loading: false
      }))
  }
```

为了解决这个问题，我们需要一个全新的模式来处理React组件中的副作用。

```JavaScript
view = fn(state)
```

实际上，构建应用程序不仅仅是UI层。需要组合和复用非可视逻辑并不少见。但是，将UI与组件结合起来可能会很困难。从历史上看，React并没有给出一个很好的答案。

继续我们的例子，假设我们需要创建另一个也需要`repos`状态的组件。现在，这种状态和处理它的逻辑存在于`ReposGrid`组件中。我们该怎么处理这件事？最简单的方法是复制获取和处理repos的所有逻辑，并将其粘贴到新组件中。很诱人，但是不够好。一种更明智的方法是创建一个[高阶组件](https://tylermcginnis.com/react-higher-order-components/)，该组件封装所有共享逻辑，并将`loading`和`repos`作为props传递给任何需要它的组件。

```JavaScript
function withRepos (Component) {
  return class WithRepos extends React.Component {
    state = {
      repos: [],
      loading: true
    }
    componentDidMount () {
      this.updateRepos(this.props.id)
    }
    componentDidUpdate (prevProps) {
      if (prevProps.id !== this.props.id) {
        this.updateRepos(this.props.id)
      }
    }
    updateRepos = (id) => {
      this.setState({ loading: true })

      fetchRepos(id)
        .then((repos) => this.setState({
          repos,
          loading: false
        }))
    }
    render () {
      return (
        <Component
          {...this.props}
          {...this.state}
        />
      )
    }
  }
}
```

现在，每当我们的应用程序中的任何组件需要`repos`(或`loading`)时，我们都可以将它封装在我们的`withRepos`高阶组件(HOC)中。

```JavaScript
// ReposGrid.js
function ReposGrid ({ loading, repos }) {
  ...
}

export default withRepos(ReposGrid)
```

```JavaScript
// Profile.js
function Profile ({ loading, repos }) {
  ...
}

export default withRepos(Profile)
```
[代码在线编辑](https://codesandbox.io/s/why-react-hooks-withrepos-okjsj)

这是可行的，之前(与[渲染Props](https://tylermcginnis.com/react-render-props/)一起)一直是共享非可视逻辑的推荐解决方案。然而，这两种模式还是都有一些缺点。

首先，如果你对它们不熟悉(即使你对它们不熟悉)，你就会比较懵。对于我们的`withRepos`HOC，我们有一个函数，它将最终呈现的组件作为第一个参数，但返回一个新的类组件，这是逻辑的关键所在。过程挺复杂！

其次，如果我们有一个以上的高阶组件正在使用。你可以想象得到，它很快就失控了。

```JavaScript
export default withHover(
  withTheme(
    withAuth(
      withRepos(Profile)
    )
  )
)
```

比上面的情况更糟糕的是最终渲染的是什么。高阶组件HOC(和类似的模式)迫使你重构和封装组件。最终会导致“封装地狱”，然后再次使它更难维护。

```JavaScript
<WithHover>
  <WithTheme hovering={false}>
    <WithAuth hovering={false} theme='dark'>
      <WithRepos hovering={false} theme='dark' authed={true}>
        <Profile 
          id='JavaScript'
          loading={true} 
          repos={[]}
          authed={true}
          theme='dark'
          hovering={false}
        />
      </WithRepos>
    </WithAuth>
  <WithTheme>
</WithHover>
```

### 现在的状况

我们现在的局面是这样的：

* React很受欢迎。

* React组件使用类创建，因为这是当时最有意义的方式。

* 使用super(props)是很烦人的。

* 没人知道“this”是怎么回事。

* 好了冷静点。我假设你知道“this”是怎么回事，但这对某些人来说是不必要的障碍。

* 通过生命周期方法组织组件，迫使我们将相关的逻辑分散到组件中。

* React对于共享非可视逻辑没有很好的原始支持。

现在我们需要一个新的组件API来解决所有的这些问题，同时保持简单、可组合、灵活和可扩展。这是一项相当艰巨的任务，但React团队却以某种方式完成了这个艰巨的任务。

### React Hooks

自从React的v0.14.0版本发布以来，我们有两种方法来创建组件：类或者函数。区别在于，如果组件具有状态或需要使用生命周期方法，则必须使用类。否则，如果它只是接受props并渲染一些UI，我们可以直接使用一个函数搞定。

如果不使用这种方式呢？如果我们总是使用函数代替使用类，那该怎么办呢？

> 有时候，优雅的实现只是一个函数，不是一种方法，不是类，不是框架，只是个功能。
> - John Carmack. Oculus VR CTO

当然，我们需要找到一种方法来让功能组件拥有状态和生命周期方法的能力，但是如果我们这样做了，我们会看到什么好处呢？

好吧，我们不再需要调用`super(props)`，我们不再需要担心显式`bind`绑定我们的方法或`this`关键字，我们也不再使用Class Field。从本质上讲，我们之前讨论过的所有“肤浅的”问题都会消失。

```JavaScript
(ノಥ,_｣ಥ)ノ彡 React.Component 🗑

function ヾ(Ő‿Ő✿)
```

现在，更困难的问题是：

* State

* 生命周期函数

* 共享非可视逻辑

#### State

由于我们不再使用类或`this`，我们需要一种新的方法来添加和管理组件内部的状态。从React的v16.8.0版本开始，React通过`useState`方法给我们提供了这种新方法。

> `useState`是你在本课程中看到的许多“Hooks”中的第一个。让这篇文章的其余部分作为一个简单的介绍。我们将在以后的章节中深入研究`useState`以及其他Hooks。

`useState`接受一个参数，即状态的初始值。它返回的是一个数组，其中第一个是状态块，第二个是更新该状态的函数。

```JavaScript
const loadingTuple = React.useState(true)
const loading = loadingTuple[0]
const setLoading = loadingTuple[1]

...

loading // true
setLoading(false)
loading // false
```

正如你所看到的，单独抓取数组中的每一项并不是最好的开发者体验。这只是为了演示`useState`是如何返回数组的。通常，你将使用Array析构来获取一行中的值。

```JavaScript
// const loadingTuple = React.useState(true)
// const loading = loadingTuple[0]
// const setLoading = loadingTuple[1]

const [ loading, setLoading ] = React.useState(true) // 👌
```

现在，让我们用新发现的`useState`Hook知识更新`ReposGrid`组件。

```JavaScript
function ReposGrid ({ id }) {
  const [ repos, setRepos ] = React.useState([])
  const [ loading, setLoading ] = React.useState(true)

  if (loading === true) {
    return <Loading />
  }

  return (
    <ul>
      {repos.map(({ name, handle, stars, url }) => (
        <li key={name}>
          <ul>
            <li><a href={url}>{name}</a></li>
            <li>@{handle}</li>
            <li>{stars} stars</li>
          </ul>
        </li>
      ))}
    </ul>
  )
}
```
[代码在线编辑](https://codesandbox.io/s/why-react-hooks-usestate-ddhc4)


* State✅

* 生命周期函数

* 共享非可视逻辑

#### 生命周期函数

有件事可能会让你伤心(或快乐？)。当使用React Hooks时，我希望你将你所知道的关于传统的React生命周期方法以及这种思维方式的所有东西都拿出来，然后忘记它。我们已经看到了考虑组件生命周期的问题-“这种[生命周期]划分自然迫使我们在组件中写下相关的逻辑。”相反，考虑一下**同步**。

想想你曾经使用过生命周期事件的任何时候。不管是设置组件的初始状态、获取数据、更新DOM等等，最终目标总是同步。通常在有React的地方(组件状态)，同步响应域之外的东西(API请求、DOM等)，反之亦然。

当我们考虑同步而不是生命周期事件时，它允许我们将相关的逻辑块组合在一起。为了做到这一点，React给了我们另一个叫做`useEffect`的Hooks。

定义Hooks，`useEffect`允许你在函数组件中执行副作用。它有两个参数，一个函数和一个可选数组。函数定义要运行的副作用，(可选的)数组定义何时“重新同步”(或重新运行)效果。

```JavaScript
React.useEffect(() => {
  document.title = `Hello, ${username}`
}, [username])
```

在上面的代码中，只要`username`发生变化，传递给`useEffect`的函数就会运行。因此，将文档的标题与`Hello，${username}`所有相关的进行解析。

现在，我们如何使用代码中的`useEffect`Hook来将`repos`与API`fetchRepos`请求同步？

```JavaScript
function ReposGrid ({ id }) {
  const [ repos, setRepos ] = React.useState([])
  const [ loading, setLoading ] = React.useState(true)

  React.useEffect(() => {
    setLoading(true)

    fetchRepos(id)
      .then((repos) => {
        setRepos(repos)
        setLoading(false)
      })
  }, [id])

  if (loading === true) {
    return <Loading />
  }

  return (
    <ul>
      {repos.map(({ name, handle, stars, url }) => (
        <li key={name}>
          <ul>
            <li><a href={url}>{name}</a></li>
            <li>@{handle}</li>
            <li>{stars} stars</li>
          </ul>
        </li>
      ))}
    </ul>
  )
}
```
[代码在线编辑](https://codesandbox.io/s/why-react-hooks-useeffect-0m35p)

很狡猾对吧？我们已经成功地摆脱了`React.Component`、`constructor`、`super`、`this`，更重要的是，我们不再在整个组件中分散(和复制)我们的效果逻辑。

* State✅

* 生命周期函数✅

* 共享非可视逻辑

#### 共享非可视逻辑

前面我们提到，React没有很好地解决共享非可视逻辑的原因是因为“将UI耦合到组件上”。这会导致创建组件的模式过于复杂，如[高阶组件](https://tylermcginnis.com/react-higher-order-components/)或[渲染props](https://tylermcginnis.com/react-render-props/)。正如你现在可能猜到的那样，Hooks也有了答案。不过，可能不是你想的那样。没有内置的Hook来共享非可视逻辑，相反，你可以创建自己的自定义Hook，这些Hook与任何UI都是解耦的。

我们可以通过创建自己的定制`useRepos`Hook来看到这一点。这个Hook将接受我们想要获取的Repos的`id`，并且(坚持使用类似的API)将返回一个数组，第一个是`loading`状态，第二个是`repos`状态。

```JavaScript
function useRepos (id) {
  const [ repos, setRepos ] = React.useState([])
  const [ loading, setLoading ] = React.useState(true)

  React.useEffect(() => {
    setLoading(true)

    fetchRepos(id)
      .then((repos) => {
        setRepos(repos)
        setLoading(false)
      })
  }, [id])

  return [ loading, repos ]
}
```
开心的是，我们需要获取`repos`相关的任何逻辑都可以在这个自定义钩子中抽象出来。现在，不管我们在哪个组件中，即使它是非可视逻辑，每当我们需要有关`repos`的数据时，我们都可以使用`useRepos`自定义Hook。

```JavaScript
function ReposGrid ({ id }) {
  const [ loading, repos ] = useRepos(id)

  ...
}
```

```JavaScript
function Profile ({ user }) {
  const [ loading, repos ] = useRepos(user.id)

  ...
}
```
[代码在线编辑](https://codesandbox.io/s/why-react-hooks-custom-hooks-t4rqv)

* State✅

* 生命周期函数✅

* 共享非可视逻辑✅

Hooks的营销理念是，你可以在功能组件中使用state。事实上，Hooks不止这些。它们是关于改进代码复用、组合和更好的缺省值的。我们还有很多关于Hooks的内容，但是现在你已经知道了它们存在的原因，我们就有了一个扎实的基础。