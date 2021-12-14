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

## 3.3 Signal

- Signal是最核心的响应式基本要素, 要创建 Signal，你可以从 `solid-js` 导入 `createSignal`
- 传递给 createSignal 调用的参数是初始值，createSignal 返回一个带有 2 个函数的数组，一个是 getter，一个是 setter
- 第一个返回值是一个 getter 而不是值本身，需要通过函数调用访问值

```javascript
import { createSignal } from "solid-js"

function Counter() {
  const [count, setCount] = createSignal(0)
  return (
  	<div>
    	<div>Count: 0</div>
    	<button onClick={() => setCount((c) => c + 1)}>add</button>
    </div>
  )
}
```



## 3.4Effect

- effect为观察者，通过createEffect创建，接收一个函数，并监视其执行情况
- `createEffect` 会自动订阅在执行期间读取的所有 Signal，并在这些 Signal 值之一发生变化时重新运行该函数
- `createEffect` 创建的 Effect 会在渲染完成后运行，主要用于调度渲染后与 DOM 交互的更新

```javascript
import { createSignal, createEffect } from 'solid-js'
import { render } from 'solid-js/web';

export default function App() {
  const [count, setCount] = createSignal(0)
  createEffect(() => {
    console.log('The count is now', count())
  })
  return (
    <>
      <div>{count()}</div>
      <button onClick={() => setCount(c => c + 1)}>add</button>
    </>
  )
}

render(() => <Counter />, document.getElementById('app'))
```

## 3.5 Memo

- 我们可以使用 Memo缓存值以减少重复工作
- Memo（一种特殊的 primitive）来存储和访问最后一个缓存的值，而无需重新进行求值，直到它们的依赖关系发生变化才重新求值

```javascript
# 1. 没有使用memo
# 每一次调用fib，都会重新执行一次
import { render } from 'solid-js/web'
import { createSignal } from 'solid-js'

function fibonacci(num) {
  if (num <= 1) return 1
  return fibonacci(num - 1) + fibonacci(num - 2)
}

function Counter() {
  const [count, setCount] = createSignal(10)
  const fib = () => {
      console.log('值发生了改变')
      return fibonacci(count())
  }

  return (
    <>
      <button onClick={() => setCount(count() + 1)}>Count: {count()}</button>
      <div>1. {fib()} {fib()} {fib()} {fib()} {fib()}</div>
      <div>2. {fib()} {fib()} {fib()} {fib()} {fib()}</div>
      <div>3. {fib()} {fib()} {fib()} {fib()} {fib()}</div>
      <div>4. {fib()} {fib()} {fib()} {fib()} {fib()}</div>
      <div>5. {fib()} {fib()} {fib()} {fib()} {fib()}</div>
      <div>6. {fib()} {fib()} {fib()} {fib()} {fib()}</div>
      <div>7. {fib()} {fib()} {fib()} {fib()} {fib()}</div>
      <div>8. {fib()} {fib()} {fib()} {fib()} {fib()}</div>
      <div>9. {fib()} {fib()} {fib()} {fib()} {fib()}</div>
      <div>10. {fib()} {fib()} {fib()} {fib()} {fib()}</div>
    </>
  )
}

render(() => <Counter />, document.getElementById('app'))

```



![screenshot-20211214-105603.png](https://s2.loli.net/2021/12/14/txD9olCmgTcfsZW.png)

```javascript
# 2.使用memo
# meme处理之后，每一次调用fib，只要值没有变化不会重新执行
import { render } from 'solid-js/web'
import { createSignal, createMemo } from 'solid-js'

function fibonacci(num) {
  if (num <= 1) return 1

  return fibonacci(num - 1) + fibonacci(num - 2)
}

function Counter() {
  const [count, setCount] = createSignal(10);
  const fib = createMemo(() => {
  console.log("值发生了改变");
  return fibonacci(count());
})

  return (
    <>
      <button onClick={() => setCount(count() + 1)}>Count: {count()}</button>
      <div>1. {fib()} {fib()} {fib()} {fib()} {fib()}</div>
      <div>2. {fib()} {fib()} {fib()} {fib()} {fib()}</div>
      <div>3. {fib()} {fib()} {fib()} {fib()} {fib()}</div>
      <div>4. {fib()} {fib()} {fib()} {fib()} {fib()}</div>
      <div>5. {fib()} {fib()} {fib()} {fib()} {fib()}</div>
      <div>6. {fib()} {fib()} {fib()} {fib()} {fib()}</div>
      <div>7. {fib()} {fib()} {fib()} {fib()} {fib()}</div>
      <div>8. {fib()} {fib()} {fib()} {fib()} {fib()}</div>
      <div>9. {fib()} {fib()} {fib()} {fib()} {fib()}</div>
      <div>10. {fib()} {fib()} {fib()} {fib()} {fib()}</div>
    </>
  )
}

render(() => <Counter />, document.getElementById('app'))


```



![screenshot-20211214-105905.png](https://s2.loli.net/2021/12/14/yTEQalPDqw7bFC8.png)

## 4.流程控制

- solidjs没有虚拟 DOM，使用诸如 `Array.prototype.map` 之类的东西会在每次更新时很浪费地重新创建所有 DOM 节点。相反，Reactive 库使用模板工具是很常见
- solidjs 模板有： Show, For, Switch/Match, Index, Dynamic, Portal, ErrorBoundary

## 4.1 Show

```javascript
# 1. 使用三元组 (a ? b : c) 和布尔表达式 (a && b)可以作为基本的条件控制。然而，使用 <Show> 组件通常更简洁
# fallback 属性充当 else，在传递给 when 的条件不为 true 时显示

import { render } from 'solid-js/web'
import { createSignal, Show } from 'solid-js'

function App() {
  const [loggedIn, setLoggedIn] = createSignal(false)
  const toggle = () => setLoggedIn(!loggedIn())
  
  return (
    <>
      <Show
        when={loggedIn()}
        fallback={() => <button onClick={toggle}>Log in</button>}
        >
        <button onClick={toggle}>Log out</button>
        </Show>
    </>
  )
}

render(() => <App />, document.getElementById('app'))
```



## 4.2 For

- `<For>` 组件是遍历任何非原始值数组的最佳方式,通过引用自动键引用，以便在数据更新时对其进行优化以更新或移动行而不是重新创建它们
- `index` 是一个 Signal，因此它可以在移动行时独立更新
- 数据项不是 Signal，因为更改将意味着新引用并会导致创建新行。进行嵌套更新的方法是创建嵌套 Signal 或使用 Solid 的 Store 代理

```javascript
import { render } from 'solid-js/web'
import { createSignal, For } from 'solid-js'

function App() {
  const [cats, setCats] = createSignal([
		{ id: 'J---aiyznGQ', name: 'Keyboard Cat' },
		{ id: 'z_AbfPXTKms', name: 'Maru' },
		{ id: 'OUtn3pvWmpg', name: 'Henri The Existential Cat' }
	]);
  
  return (
    <For each={cats()}>
    	{(cat, i) => (
      	<li>
        	<a>{i() + 1}: {cat.name}</a>
        </li>
      )}
  	</For>
  )
}
render(() => <App />, document.getElementById('app'))


```

## 4.3 Index

- 在处理原始值或二维数组时，将值视为键可能会导致很多不必要的渲染
- Index 中数组索引是列表的实际键
- `<Index>` 与 `<For>` 具有相似的签名，除了数据项是 Signal 并且索引是固定的

```javascript

import { render } from 'solid-js/web'
import { createSignal, Index } from 'solid-js'

function App() {
  const [cats, setCats] = createSignal([1, 2, 3])
  
  return (
    <Index each={cats()}>
    {(cat, i) => (
      <div>
        <div>
          {i + 1}: {cat()}
        </div>
      </div>
    )}
  </Index>
  )
}

render(() => <App />, document.getElementById('app'))

```



## 4.4 Switch/Match

- Switch用于处理 2 个以上互斥结条件
- 尝试匹配每个条件，当第一个条件求值为真时停止渲染。如果所有这些都失败，它将渲染回退内容（fallback）

```javascript
import { render } from "solid-js/web"
import { createSignal, Switch, Match } from "solid-js"

function App() {
  const [x] = createSignal(7)

  return (
   <Switch fallback={<p>{x()} is between 5 and 10</p>}>
    <Match when={x() > 10}>
        <p>{x()} is greater than 10</p>
    </Match>
    <Match when={5 > x()}>
        <p>{x()} is less than 5</p>
    </Match>
    </Switch>
  )
}

render(() => <App />, document.getElementById("app"))

```



## 4.5 Dynamic

- `<Dynamic>` 标签处理根据数据渲染时很有用。`<Dynamic>` 可以让你将元素的字符串或组件函数传递给它，并使用提供的其余 props 来渲染组件

```javascript
import { render, Dynamic } from "solid-js/web"
import { createSignal, For } from "solid-js"

const RedThing = () => <strong style="color: red">Red Thing</strong>
const GreenThing = () => <strong style="color: green">Green Thing</strong>
const BlueThing = () => <strong style="color: blue">Blue Thing</strong>

const options = {
  red: RedThing,
  green: GreenThing,
  blue: BlueThing
}

function App() {
  const [selected, setSelected] = createSignal("red");

  return (
    <>
      <select value={selected()} onInput={e => setSelected(e.currentTarget.value)}>
        <For each={Object.keys(options)}>{
          color => <option value={color}>{color}</option>
        }</For>
      </select>
      <Dynamic component={options[selected()]} />
    </>
  )
}

render(() => <App />, document.getElementById("app"))
```



## 4.6 Portal

- 有时在应用程序的正常顺序之外插入元素是很有用。Z-index 有时不足以处理像模态框类浮动元素的渲染上下文
- Solid 提供一个 `<Portal>` 组件，`<Portal>` 的子内容将被插入到选择的位置。默认情况下，它的元素将在 `document.body` 下的 `<div>` 中呈现

```javascript
import { render, Portal } from "solid-js/web"

function App() {
  return (
    <div class="app-container">
      <p>Just some text inside a div that has a restricted size.</p>
      <Portal>
        <div class="popup">
            <h1>Popup</h1>
            <p>Some text you might need for something or other.</p>
        </div>
       </Portal>
    </div>
  );
}

render(() => <App />, document.getElementById("app"))
```



## 4.7 ErrorBoundary

- 错误边界(ErrorBoundary)是一个可以捕获子组件树任何位置产生的 JavaScript 错误，错误边界(ErrorBoundary) 会记录这些错误，并显示回退 UI 而非崩溃的组件树

```javascript

import { render } from "solid-js/web"
import { ErrorBoundary } from "solid-js"

const Broken = (props) => {
  throw new Error("Oh No")
  return <>Never Getting Here</>
}

function App() {
  return (
    <>
      <div>Before</div>
      <ErrorBoundary fallback={(err) => err}>
        <Broken />
      </ErrorBoundary>
      <div>After</div>
    </>
  )
}
render(() => <App />, document.getElementById("app"))
```

## 4.8 Suspense

- `<Suspense>` 是一个跟踪其下所有读取资源并显示回退占位符状态的组件，直到它们被解析。`Suspense` 与 `Show` 的不同之处在于它是非阻塞的，即使当前不在 DOM 中，两个分支也可以同时存在

```javascript
<Suspense fallback={<div>Loading...</div>}>
  <AsyncComponent />
</Suspense>
```



## 5.生命周期

- Solid 中只有少量的生命周期，因为一切的存活销毁都由响应系统控制。响应系统是同步创建和更新的，因此唯一的调度就是将逻辑写到更新结束的 Effect 中

## 5.1 onMount

- onMount是createEffect的一个非跟踪（从不重新运行）状态的别名
- 只是一个 Effect 调用，一旦所有初始渲染完成，它只会在组件中运行一次

```javascript
import { render } from "solid-js/web"
import { createSignal, onMount, For } from "solid-js"
import "./styles.css"

function App() {
  const [photos, setPhotos] = createSignal([])

  onMount(async () => {
    const res = await fetch(`https://jsonplaceholder.typicode.com/photos?_limit=20`)
    setPhotos(await res.json())
  });

  return <>
    <h1>Photo album</h1>

    <div class="photos">
      <For each={photos()} fallback={<p>Loading...</p>}>{ photo =>
        <figure>
          <img src={photo.thumbnailUrl} alt={photo.title} />
          <figcaption>{photo.title}</figcaption>
        </figure>
      }</For>
    </div>
  </>
}

render(() => <App />, document.getElementById('app'))
```



## 5.2 onCleanup

- 会在当前响应范围内执行销毁或重新计算时候被触发。可用于任何组件或 Effect

```javascript

import { render } from "solid-js/web"
import { createSignal, onCleanup } from "solid-js"

function Counter() {
  const [count, setCount] = createSignal(0)

  const timer = setInterval(() => setCount(count() + 1), 1000)
  onCleanup(() => clearInterval(timer))

  return <div>Count: {count()}</div>
}

render(() => <Counter />, document.getElementById('app'))
```



## 5.3  onError

- 注册一个错误处理函数，它会在子作用域抛出错误时执行。但是最近的范围的错误处理函数才会执行。重新抛出可以向上触发



## 6.绑定

- Solid 中的事件属性都会以 `on` 为前缀
- 常见的 UI 事件（冒泡和组合）会自动委托给文档元素。为了提高委托性能，Solid 支持数组语法调用事件处理程序，我们使用数据作为第二个参数所以无需创建额外的闭包
- 所有 `on` 绑定都不区分大小写，这意味着事件名称需要小写。如果你有需要支持其他大小写或不使用事件委托的情况，可以使用 `on:` 命名空间来匹配冒号后面的事件处理程序

```javascript
import { render } from "solid-js/web"
import { createSignal } from "solid-js"

function App() {
  const [pos, setPos] = createSignal({x: 0, y: 0});

  function handleMouseMove(event) {
    setPos({
      x: event.clientX,
      y: event.clientY
    });
	}

  return (
    <div onMouseMove={handleMouseMove}>
    The mouse position is {pos().x} x {pos().y}
    </div>
  );
}

render(() => <App />, document.getElementById('app'))
```

