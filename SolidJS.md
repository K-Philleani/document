# SolidJS

## 1.SolidJS是什么

一个声明式、高效且灵活用于构建用户界面的 JavaScript 库

## 2.为什么使用SolidJS

1. 接近原生，性能好，速度快

   ![screenshot-20211213-162917.png](https://s2.loli.net/2021/12/13/SpWZKNleu6TfgdX.png)

2. 包容量小，包含除路由外所有核心内容

   ![screenshot-20211213-163215.png](https://s2.loli.net/2021/12/13/KUkVMJRvzGfsAtn.png)

3. 语法简单灵活，使用JSX(不使用VDOM)，类似React Hooks语法

![screenshot-20211213-163449.png](https://s2.loli.net/2021/12/13/EpLMvofDqlzuPsj.png)

## 3.入门

我们可以通过solidjs命令获取基础脚手架模板

```shell
# 1.直接创建
> npx degit solidjs/templates/js my-app
> cd my-app
> npm i # or yarn or pnpm
> npm run dev # or yarn or pnpm

# 2.使用degit创建
> npm i -g degit
> degit solidjs/templates/js my-app
> cd my-app
> npm i # or yarn or pnpm
> npm run dev # or yarn or pnpm

# 3.使用TypeScript, 也可使用degit
> npx degit solidjs/templates/ts my-app
> cd my-app
> npm i # or yarn or pnpm
> npm run dev # or yarn or pnpm
```

## 3.1 入口和组件

- 组件应为大驼峰写法，这里与React的组件相同，并且可以接受props对象并返回JSX元素， 组件第一个参数接收 props 对象，最终返回真实的 DOM 节点，所有JSX节点都是真是DOM,SolidJS没有虚拟DOM
- solidjs的jsx与React的jsx有一点最明显的不同，solidjs默认而且以后也不会使用类组件，所以不存在class关键字的冲突，solidjs类选择器为class而不是react的className
- solidJS使用render函数进行渲染，接受两个参数，第一个参数为一个函数， 第二个为挂载的容器，并返回一个销毁的方法

```javascript
import { render } from 'solid-js/web'
const App = () => {
    return (
    	<div class="title">Hello, SolidJS<div>
    )
}

render(() => <App />, document.getElementById('root'))
```

## 3.2 Props

- Solid 允许你在组件上定义 property，将数据传递给子组件

```javascript
import { createSignal } from 'solid-js'
const Parent = () => {
    const [greeting, setGreeting] = createSignal("Hello")
    return (
    	<div>
        	<Label greeting="greeting">
        		<div>Jhon</div>
        	</Label>
        </div>
    )
}

const Label = (props) => {
    <>
        <div>{ props.greeting }</div>
    	{ props.children }
    </>
}
```

- 与其他框架不同，你不能在组件的 `props` 上使用对象解构。这是因为 `props` 对象在底层依赖对象的 getter 来惰性获取值。使用对象解构破坏了 props 的响应性。

```javascript
// 这里 `props.name` 会按照你的期望更新
const MyComponent = props => <div>{props.name}</div>;

// 错误方式
// 这里，`props.name` 不会更新（即不是响应式的），因为它被解构为 `name`
const MyComponent = ({ name }) => <div>{name}</div>;
```

- 虽然 props 对象在使用时看起来像一个普通对象（Typescript 用户会注意到它的类型像普通对象），但实际上它是响应式的--使用Proxy实现响应式，解构会使值失去Proxy特性