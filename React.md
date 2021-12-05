## React

# 三大核心属性

1. state
2. props
3. refs与事件处理

## 1.state

```javascript
# 1.类组件中的state

class App extends React.Component {
  constructor() {
    super()
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

