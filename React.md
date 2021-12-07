#	（一）组件三大核心属性

1. state
2. props
3. refs与事件处理

- **我们强烈建议你不要创建自己的组件基类。** 在 React 组件中，代码重用的主要方式是组合而不是继承

## 1.state

```javascript
# 1.类组件构造函数中的state

class App extends React.Component {
  constructor() {
    super() // this的操作必须放在super后面
    this.state = {
      flag: true
    }
  }
  render() {
    return (
      <div>{ this.state.flag ? '显示' : '隐藏' }</div>
    )
  }
}
```

```javascript
# 2.通过向类添加属性实现state

class App extends React.Component {
  state = {
    flag: true
  }
  render() {
    return (
      <div>
        <div>{ this.state.flag ? '显示' : '隐藏' }</div>
      </div>
    )
  }
}
```



## 2.事件绑定&&更改State

```javascript
# 1.基本方式绑定 && 使用bind绑定this
# 由于ES6中的class默认使用严格模式，所以类中事件的this为undefined,需要为事件绑定实例
# 类组件中状态必须通过setState修改，并且修改是一种合并，并非替换

class App extends React.Component {
  constructor() {
    super()
    this.state = {
      flag: true
    }
    this.change = this.change.bind(this)
  }
  change() {
    this.setState({flag: !this.state.flag})
  }
  render() {
    return (
      <div>
        <div>{ this.state.flag ? '显示' : '隐藏' }</div>
        <button onClick={this.change}>click</button>
      </div>
    )
  }
}

```

```javascript
# 2.通过赋值箭头函数绑定this

class App extends React.Component {
  constructor() {
    super()
    this.state = {
      flag: true
    }
  }
  change = () =>  {
    this.setState({flag: !this.state.flag})
  }
  render() {
    return (
      <div>
        <div>{ this.state.flag ? '显示' : '隐藏' }</div>
        <button onClick={this.change}>click</button>
      </div>
    )
  }
}
```



## 3.Props

```javascript
# 1.props基本使用, props是只读的

<Cat name="生巧" age="3" sex="fale" />

class Cat extends React.Component {
  render() {
    const { name, age, sex } = this.props
    return (
      <div>
        <div>name: { name }</div>
        <div>age: { age }</div>
        <div>sex: { sex }</div>
      </div>
    )
  }
}
```

```javascript
# 2.批量props

const p = { name: "生巧", age: "3", sex: "fale" }
<Cat {...p} />
// {...p} => ...p 并非是ES6的对象解构, ES6对象不能直接解构，应该在一个对象内解构
// {...p}是React的优化写法,babel和react会进行处理，仅仅适用于标签上

class Cat extends React.Component {
  render() {
    const { name, age, sex } = this.props
    return (
      <div>
        <div>name: { name }</div>
        <div>age: { age }</div>
        <div>sex: { sex }</div>
      </div>
    )
  }
}

```

```javascript
# 3.函数式组件使用props
# 函数式组件默认无法使用state和refs

function Cat(props) {
  const { name, age, sex } = props
  return (
      <div>
        <div>name: { name }</div>
        <div>age: { age }</div>
        <div>sex: { sex }</div>
      </div>
    )
}
```



## 4.对Props进行限制&&默认值

```javascript
# 1.PropTypes对Props进行约束
# 依赖React.propTypes， React15.5开始改为独立的库prop-types

import PropTypes from 'prop-types'

class Cat extends React.Component {
  render() {
    const { name, age, sex } = this.props
    return (
      <div>
        <div>name: { name }</div>
        <div>age: { age + 1}</div>
        <div>sex: { sex }</div>
      </div>
    )
  }
}

Cat.propTypes = {
  name: PropTypes.string,
  age: PropTypes.number,
  sex: PropTypes.string
}

Cat.defaultProps = {
  name: 'tom',
  age: 10,
  sex: 'fale'
}
```

```javascript
# 优化写法

import PropTypes from 'prop-types'

class Cat extends React.Component {
  static propTypes = {
    name: PropTypes.string.isRequired,
    age: PropTypes.number,
    sex: PropTypes.string
  }
  static defaultProps = {
    name: 'tom',
    age: 10,
    sex: 'fale'
  }
  render() {
    const { name, age, sex } = this.props
    return (
      <div>
        <div>name: { name }</div>
        <div>age: { age + 1 }</div>
        <div>sex: { sex }</div>
      </div>
    )
  }
}

```

```javascript
# 3.函数式组件对props进行限制

function Cat(props) {
  const { name, age, sex } = props
  return (
      <div>
        <div>name: { name }</div>
        <div>age: { age }</div>
        <div>sex: { sex }</div>
      </div>
    )
}
Cat.propTypes = {
  name: PropTypes.string,
  age: PropTypes.number,
  sex: PropTypes.string
}

Cat.defaultProps = {
  name: 'tom',
  age: 10,
  sex: 'fale'
}
```

## 5.Refs

- 管理焦点，文本选择或媒体播放。
- 触发强制动画。
- 集成第三方 DOM 库。

```javascript
# 1.字符串ref（已废除,存在效率问题）

class App extends React.Component {
  focus() {
    this.refs.refInput.focus()
  }
  get() {
    console.log(this.refs.refInput.value)
  }
  render() {
    return (
      <div>
        <input ref="refInput" type="text" placeholder="获取焦点" />
        <button onClick={() => this.focus()}>获取焦点</button>
        <button onClick={() => this.get()}>获取数据</button>
      </div>
    )
  }
}
```

```javascript
# 2.回调形式

class App extends React.Component {
  focus() {
    this.refInput.focus()
  }
  get() {
    console.log(this.refInput.value)
  }
  render() {
    return (
      <div>
        <input ref={ (element) => this.refInput = element } type="text" placeholder="获取焦点" />
        <button onClick={() => this.focus()}>获取焦点</button>
        <button onClick={() => this.get()}>获取数据</button>
      </div>
    )
  }
}
```

- 如果 `ref` 回调函数是以内联函数的方式定义的，在更新过程中它会被执行两次，第一次传入参数 `null`，然后第二次会传入参数 DOM 元素。这是因为在每次渲染时会创建一个新的函数实例，所以 React 清空旧的 ref 并且设置新的。通过将 ref 的回调函数定义成 class 的绑定函数的方式可以避免上述问题，但是大多数情况下它是无关紧要的。

```javascript
# 3.通过React.CreateRef()创建refs
# React.CreateRef调用后返回一个容器，可以存储被ref标识的节点，一个容器存放一个ref节点

class App extends React.Component {
  constructor() {
    super()
    this.refInput = React.createRef()
  }
  focus() {
    this.refInput.current.focus()
  }
  get() {
    console.log(this.refInput.current.value)
  }
  render() {
    return (
      <div>
        <input ref={ this.refInput } type="text" placeholder="获取焦点" />
        <button onClick={() => this.focus()}>获取焦点</button>
        <button onClick={() => this.get()}>获取数据</button>
      </div>
    )
  }
}
```

## 6.非受控组件

```javascript
# 在大多数情况下，推荐使用 受控组件 来处理表单数据。在一个受控组件中，表单数据是由 React 组件来管理的。
# 另一种替代方案是使用非受控组件，这时表单数据将交由 DOM 节点来处理。

class App extends React.Component {
  submit(event) {
    event.preventDefault()
    console.log('username:', this.username.value, 'password:', this.password.value)
  }
  render() {
    return (
      <div>
        <form action="#" onSubmit={(event) => this.submit(event)}>
          <input ref={e => this.username = e} type="text" name="username" />
          <input ref={e => this.password = e} type="password" name="password" />
          <button>提交</button>
        </form>
      </div>
    )
  }
}
```



## 7.受控组件

```javascript

class App extends React.Component {
  state = {
    username: '',
    password: ''
  }
  changeUsername(e) {
    this.setState({ username: e.target.value })
  }
  changePassword(e) {
    this.setState({ password: e.target.value })
  }
  submit(event) {
    event.preventDefault()
    console.log('username:', this.state.username, 'password:', this.state.password)
  }
  render() {
    return (
      <div>
        <form action="#" onSubmit={(event) => this.submit(event)}>
          <label htmlFor="username">username</label>
          <input onChange={(e) => this.changeUsername(e)} type="text" name="username" />
          <label htmlFor="password">password</label>
          <input onChange={(e) => this.changePassword(e)} type="password" name="password" />
          <button>提交</button>
        </form>
      </div>
    )
  }
}
```



- 高阶函数：满足以下两点中的一点
  1. 接受的参数是一个函数，
  2. 调用的返回值是一个函数
- 函数的柯里化： 通过函数调用继续返回函数的方式，实现多次接受参数最后统一处理的函数编码形式
  - setTimeout,promise, js数组api...

#	（二）组件生命周期

## 1.旧版本

## ![img](https://cdn.nlark.com/yuque/0/2021/png/271392/1638797530242-92ddc687-7199-4456-9c71-306b6e789cb6.png) 



```javascript
# 1.挂载
# 2.更新 setState forupdate

class App extends React.Component {
  // 构造函数
  constructor(props) {
    super(props)
    console.log('constructor') // => 1, 1次
    this.state = {
      count: 0
    }
  }
  // 组件将要挂载
  componentWillMount() { // 这个生命周期已经rename
    console.log('componentWillMount') // => 2， 1次
  }
  // 组件已经挂载
  componentDidMount() {
    console.log('componentDidMount') // => 4， 1次
  }
  
  // 控制组件更新
  // 不单独声明，则默认返回true,返回值必须为布尔值， 返回true则更新，继续其他生命周期，返回false则停止
  shouldComponentUpdate() {
    console.log('shouldComponentUpdate')
    return true
  }
  
  // 组件将要更新
  componentWillUpdate() { // 这个生命周期已经rename
    console.log('componentWillUpdate')
  }
  
  // 组件已经更新
  componentDidUpdate() {
    console.log('componentDidUpdate')
  }
  
  // 组件将被移除
  componentWillUnmount() {
    console.log('componentWillUnmount') // => 5. 1次
  }
  // 组件渲染
  render() {
    console.log('render') // => 3， 1+n次
    return (
      <div>
        React生命周期--{this.state.count}
        <button onClick={() => this.change()}>click</button>
        <button onClick={() => this.remove()}>remove</button>
	    <button onClick={() => this.force()}>force</button>
      </div>
    )
  }
  // 更新状态
  change() {
    let count = this.state.count
    this.setState({ count: count + 1}) // React不支持自增(++)自减(--)
  }
  // 卸载组件
  remove() {
    ReactDOM.unmountComponentAtNode(document.getElementById('root')) // ReactDOM 主动卸载组件
  }
  // 强制更新
  force() {
    this.forceUpdate()
  }
}
```

```javascript
# 父组件更新触发子组件

class Child extends React.Component {
  // 组件将要接收新的props
  componentWillReceiveProps() { // 这个生命周期已经rename
    console.log('Child, componentWillReceiveProps')
  }
  
  // 控制组件更新
   shouldComponentUpdate() {
    console.log('shouldComponentUpdate')
    return true
  }
 
  // 组件将要更新
  componentWillUpdate() { // 这个生命周期已经rename
    console.log('componentWillUpdate')
  }
  
  // 组件已经更新
  componentDidUpdate() {
    console.log('componentDidUpdate')
  }
  
  render() {
    console.log('render')
    return (
      <div>child {this.props.count}</div>
    )
  }
}
class App extends React.Component {
  state = {
    count: 0
  }
  change() {
    let count = this.state.count
    this.setState({ count: count + 1 })
  }
  render() {
    return (
      <div>
        <Child />
        <div>father</div>
        <div>{this.state.count}</div>
        <button onClick={() => this.change()}>change</button>
      </div>
    )
  }
}

```

## 2.更名的声明周期

```javascript
# 1.挂载
# 2.更新 setState forupdate

class App extends React.Component {
  // 构造器
  constructor(props) {
    console.log('constructor')
    super(props)
    this.state = {
      count: 0
    }
  }
  // 组件将要挂载
  UNSAFE_componentWillMount() {
    console.log('UNSAFE_componentWillMount')
  }
  // 组件已经挂载
  componentDidMount() {
    console.log('componentDidMount')
  }
  // 组件将要卸载
  componentWillUnmount() {
    console.log('componentWillUnmount')
  }
  // 控制组件更新
  shouldComponentUpdate() {
    console.log('shouldComponentUpdate')
    return false
  }
  // 组件将要更新
  UNSAFE_componentWillUpdate() { 
    console.log('UNSAFE_componentWillUpdate')
  }
  // 组件已经更新
  componentDidUpdate() {
    console.log('componentDidUpdate')
  }
  // 组件渲染
  render () {
    console.log('render')
    return (
      <div>
      React声明周期
      <div>{this.state.count}</div>
      <button onClick={() => this.change()}>change</button>
      <button onClick={() => this.remove()}>remove</button>
      <button onClick={() => this.force()}>force</button>
      </div>
    )
  }
  change() {
    const count = this.state.count
    this.setState({ count: count + 1 })
  }
  remove() {
    ReactDOM.unmountComponentAtNode(document.getElementById('root'))
  }
  force() {
    this.forceUpdate()
  }
}

```



```javascript
# 父组件更新触发子组件

class Child extends React.Component {
  // 组件将要接收新的props
  UNSAFE_componentWillReceiveProps() {
    console.log('UNSAFE_componentWillReceiveProps')
  }
  render() {
    return (
      <div>
        <div>Child</div>
        <div>{this.props.count}</div>
      </div>
    )
  }
}

class App extends React.Component {
  state = {
    count: 0
  }
  render() {
    return (
      <div>
        <Child count={this.state.count} />
        <div>Father</div>
        <div>{this.state.count}</div>
        <button onClick={() => this.change()}>change</button>
      </div>
    )
  }
  change() {
    const count = this.state.count
    this.setState({ count: count + 1 })
  }
}
```



## 2.新版本

![img](https://cdn.nlark.com/yuque/0/2021/png/271392/1638798215485-187b5b83-9697-4d31-94c7-48a8a7212730.png )



```javascript
# 新加入的生命周期：getDerivedStateFromProps(极少使用)

<App count={200} />
    
class App extends React.Component {
  // 构造器
  constructor(props) {
    console.log('constructor')
    super(props)
    this.state = {
      count: 0
    }
  }
  componentDidMount() {
    console.log('componentDidMount')
  }
  // getDerivedStateFromProps 会在调用 render 方法之前调用，并且在初始挂载及后续更新时都会被调用。
  // 它应返回一个对象来更新 state，如果返回 null 则不更新任何内容
  // 此方法适用于罕见的用例，即 state 的值在任何时候都取决于 props。
  // 例如，实现 <Transition> 组件可能很方便，该组件会比较当前组件与下一组件，以决定针对哪些组件进行转场动画。
  // 派生状态会导致代码冗余，并使组件难以维护
  static getDerivedStateFromProps(props) {
    console.log('getDerivedStateFormProps', props)
    return {
      count: props.count
    }
  }

  // 组件渲染
  render () {
    console.log('render')
    return (
      <div>
      React声明周期
      <div>{this.state.count}</div>
      <button onClick={() => this.change()}>change</button>
      <button onClick={() => this.remove()}>remove</button>
      <button onClick={() => this.force()}>force</button>
      </div>
    )
  }
  change() {
    const count = this.state.count
    this.setState({ count: count + 1 })
  }
  remove() {
    ReactDOM.unmountComponentAtNode(document.getElementById('root'))
  }
  force() {
    this.forceUpdate()
  }
}
```



```javascript
# 新加入的生命周期：getSnapshotBeforeUpdate(极少使用)

class App extends React.Component {
  // 构造器
  constructor(props) {
    console.log('constructor')
    super(props)
    this.state = {
      count: 0
    }
  }
  // getSnapshotBeforeUpdate() 在最近一次渲染输出（提交到 DOM 节点）之前调用。它使得组件能在发生更改之前从 DOM 中捕获一些信息（例如，滚动位置）。
  // 此生命周期方法的任何返回值将作为参数传递给 componentDidUpdate()。
  // componentDidUpdate必须存在
  // 更新前获取快照
  getSnapshotBeforeUpdate() {
    console.log('getSnapshotBeforeUpdate')
    return 'getSnapshotBeforeUpdate'
  }
  componentDidUpdate(prevProps, prevState, snapshot) {
    console.log('componentDidUpdate', prevProps, prevState, snapshot)
  }

  // 组件渲染
  render () {
    console.log('render')
    return (
      <div>
      React声明周期
      <div>{this.state.count}</div>
      <button onClick={() => this.change()}>change</button>
      </div>
    )
  }
  change() {
    const count = this.state.count
    this.setState({ count: count + 1 })
  }
}
```



