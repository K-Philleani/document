

# React



## 三大核心属性

1. state
2. props
3. refs与事件处理

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
