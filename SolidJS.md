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

### 3.1 入口和组件

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

### 3.2 Props

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

### 3.3 Signal

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



### 3.4 Effect

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

### 3.5 Memo

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

### 4.1 Show

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



### 4.2 For

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

### 4.3 Index

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



### 4.4 Switch/Match

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



### 4.5 Dynamic

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



### 4.6 Portal

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



### 4.7 ErrorBoundary

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

### 4.8 Suspense

- `<Suspense>` 是一个跟踪其下所有读取资源并显示回退占位符状态的组件，直到它们被解析。`Suspense` 与 `Show` 的不同之处在于它是非阻塞的，即使当前不在 DOM 中，两个分支也可以同时存在

```javascript
<Suspense fallback={<div>Loading...</div>}>
  <AsyncComponent />
</Suspense>
```



## 5.生命周期

- Solid 中只有少量的生命周期，因为一切的存活销毁都由响应系统控制。响应系统是同步创建和更新的，因此唯一的调度就是将逻辑写到更新结束的 Effect 中

### 5.1 onMount

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



### 5.2 onCleanup

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



### 5.3  onError

- 注册一个错误处理函数，它会在子作用域抛出错误时执行。但是最近的范围的错误处理函数才会执行。重新抛出可以向上触发



## 6.绑定

### 6.1 事件

- Solid 中的事件属性都会以 `on` 为前缀
- 常见的 UI 事件（冒泡和组合）会自动委托给文档元素。为了提高委托性能，Solid 支持数组语法调用事件处理程序，我们使用数据作为第二个参数所以无需创建额外的闭包
- 所有 `on` 绑定都不区分大小写，这意味着事件名称需要小写。如果你有需要支持其他大小写或不使用事件委托的情况，可以使用 `on:` 命名空间来匹配冒号后面的事件处理程序

```javascript
# 1.基础写法
import { render } from "solid-js/web"
import { createSignal } from "solid-js"

function App() {
  const [pos, setPos] = createSignal({x: 0, y: 0})

  function handleMouseMove(event) {
    setPos({
      x: event.clientX,
      y: event.clientY
    })
	}

  return (
    <div onMouseMove={handleMouseMove}>
    The mouse position is {pos().x} x {pos().y}
    </div>
  )
}

render(() => <App />, document.getElementById('app'))
```

```javascript
# 2. 数组语法调用

import { render } from "solid-js/web"
import { createSignal } from "solid-js"

function App() {
  function handleMouseMove(data) {
    console.log("data", data)
	}

  return (
    <div onMouseMove={[handleMouseMove, "data"]}></div>
  );
}

render(() => <App />, document.getElementById('app'))
```



### 6.2 样式

- Solid 中的 style 属性接受样式字符串或对象
- Solid 通过调用 `style.setProperty` 的封装来进行样式设置。这意味着键需要采用破折号的形式，如 `background-color` 而不是 `backgroundColor`。 但这意味着我们可以设置 CSS 变量
- `classList` 接受一个对象，其中键是类名，值是一个布尔表达式。当为 true 时应用该 class，当为 false 时该 class 被移除。
- `classlist`相当于Vue中的动态class接受了一个对象

```javascript
<div style={{ "--my-custom-color": themeColor() }} />

<div
  style={{
    color: `rgb(${num()}, 180, ${num()})`,
    "font-weight": 800,
    "font-size": `${num()}px`,
  }}
>
  Some Text
</div>


<button
  classList={{ selected: current() === "foo" }}
  onClick={() => setCurrent("foo")}
>
  foo
</button>

```



### 6.3 Ref

- 可以通过ref获取到DOM元素的引用
- Refs 也可以采用回调函数的形式
- ref可以通过转发暴露给父组件

```javascript
# 基本用法
let myDiv;
<div ref={myDiv}>My Element</div>;

# 回调形式
<div ref={el => /* 处理 el... */}>My Element</div>

# 转发
<canvas ref={props.ref} width="256" height="256" /> // 子组件
  
<Canvas ref={canvas} // 父组件
```



### 6.4 指令

- 通过 `use:` 命名空间支持自定义指令。但这只是 `ref` 一个有用的语法糖，类似于原生的绑定，并且可以在同一个元素上有多个绑定而不会发生冲突。这可以让我们更好地利用可重用 DOM 元素行为
- 自定义指令只是形式为 `(element, valueAccesor)` 的函数，其中 `valueAccessor` 是一个获取绑定值的函数。只要函数是在作用域中导入的，你就可以通过 `use:` 使用它

```javascript
# click-outsie.js
export default function clickOutside(el, accessor) {
  // implement here
  const onClick = (e) => !el.contains(e.target) && accessor()?.()
  document.body.addEventListener("click", onClick)
  onCleanup(() => document.body.removeEventListener("click", onClick))
}

# indeex.js
import { render } from "solid-js/web"
import { createSignal, Show } from "solid-js"
import clickOutside from "./click-outside"

function App() {
  const [show, setShow] = createSignal(false)

  return (
    <Show
      when={show()}
      fallback={<button onClick={(e) => setShow(true)}>Open Modal</button>}
    >
      <div class="modal" use:clickOutside={() => setShow(false)}>
        Some Modal
      </div>
    </Show>
  );
}

render(() => <App />, document.getElementById("app"))


```



## 7.Props

- Props 对象是只读的，并且含有封装为对象 getter 的响应式属性
- `mergeProps`: 响应式对象的合并 `merge` 方法。用于为组件设置默认 props 以防调用者不提供这些属性值,或者克隆包含响应式的属性的 props 对象
- `splitProps`: `splitProps` 是解构的替代品。`splitProps` 在保持响应性的同时通过键来拆分响应式对象,接收一个 props 对象以及一个 props 对象的键数组。返回一个数组，数组第一个元素是与入参键数组对应的对象。数组中的最后一个元素会是一个未指定的键名的 props 对象，，类似于剩余参数

```javascript
// 设置默认 props
props = mergeProps({ name: "Smith" }, props);

// 克隆 props
newProps = mergeProps(props);

// 合并 props
props = mergeProps(props, otherProps);

// 分离 propos
const [local, others] = splitProps(props, ["children"]);
<>
  <Child {...others} />
  <div>{local.children}<div>
</>
  
```

```javascript
# 父组件
<Greeting greeting="Hello" />
  
# 子组件 
const merged = mergeProps({ greeting: "Hi", name: "John" }, props);
return <h3>{merged.greeting} {merged.name}</h3>
```

```javascript
# 扩展，类似与React的批量props
<Info {...pkg} />

```

```javascript
# props.children: 接受组件标签内层的JSX元素
function ColoredList(props) {
  return <>{props.children}</>
}
 
# 如果有一个动态列表，我们直接与 props.children 交互，不仅会多次创建节点
# children（Api）: 这是一个工具函数解决层级嵌套的响应性并返回一个 Memo。除了直接传递值给 JSX 这种情况之外，推荐使用 props.children 的方法
const list = children(() => props.children);
// 用 list 做点什么
createEffect(() => list())

```



## 8.Store 状态存储

### 8.1 创建Store

- Store 通过`createStore`创建一个 Signal 树作为代理，允许独立跟踪嵌套数据结构中的各个值，常用于嵌套的数据结构, 例如对象数组等

- Store 代理对象仅跟踪访问的属性。并在访问 Store 时递归地生成嵌套数据上的嵌套 Store 对象，但是它只包装数组和普通对象，类不包装，所以像 `Date`、`HTMLElement`、`RegExp`、`Map`、`Set` 之类的东西都不是响应式粒度的。此外，如果不访问对象上的属性，则无法跟踪顶级状态对象
- 如果不访问对象上的属性，则无法跟踪顶级状态对象。因此它不适用于迭代对象，因为添加新键或索引无法触发更新。因此，将数组放在键上，而不是尝试使用状态对象本身

```javascript
const [state, setState] = createStore(initialValue)

// 读取值
state.someValue

// 设置值
setState({ merge: "thisValue" })

setState("path", "to", "value", newValue)

// 将列表作为状态对象的键
const [state, setState] = createStore({ list: [] })

// 访问 state 对象上的 `list` 属性
<For each={state.list}>{item => /*...*/}</For>
```

### 8.2 Getter

- Store 对象支持使用 getter 来存储计算值

```javascript
# 1.直接使用
const [state, setState] = createStore({
  user: {
    firstName: "John",
    lastName: "Smith",
    get fullName() {
      return `${this.firstName} ${this.lastName}`;
    },
  },
})
```

```javascript
# 2.使用memo

let fullName
const [state, setState] = createStore({
  user: {
    firstName: "John",
    lastName: "Smith",
    get fullName() {
      return fullName();
    },
  },
})
fullName = createMemo(() => `${state.user.firstName} ${state.user.lastName}`)
```





### 8.3 更新Store

- 更改状态可以采用传递先前状态并返回新状态或值的函数的形式。对象总是浅合并的。将值设置为 `undefined` 以将属性从 Store 中删除

```javascript
const [state, setState] = createStore({
  firstName: "John",
  lastName: "Miller",
});

setState({ firstName: "Johnny", middleName: "Lee" });
// ({ firstName: 'Johnny', middleName: 'Lee', lastName: 'Miller' })

setState((state) => ({ preferredName: state.firstName, lastName: "Milner" }));
// ({ firstName: 'Johnny', preferredName: 'Johnny', middleName: 'Lee', lastName: 'Milner' })

```

- setState 支持的路径包括键数组、对象范围和过滤器函数。
- setState 还支持嵌套设置，你可以在其中指明要修改的路径。在嵌套的情况下，要更新的状态可能是非对象值。对象仍然合并，但其他值（包括数组）将会被替换。

```javascript

const [state, setState] = createStore({
  counter: 2,
  list: [
    { id: 23, title: 'Birds' }
    { id: 27, title: 'Fish' }
  ]
});

setState('counter', c => c + 1)
setState('list', l => [...l, {id: 43, title: 'Marsupials'}])
setState('list', 2, 'read', true)

// {
//   counter: 3,
//   list: [
//     { id: 23, title: 'Birds' }
//     { id: 27, title: 'Fish' }
//     { id: 43, title: 'Marsupials', read: true }
//   ]
// }
```

- 路径可以是字符串键、键数组、迭代对象（{from、to、by}）或过滤器函数

```javascript

const [state, setState] = createStore({
  todos: [
    { task: 'Finish work', completed: false }
    { task: 'Go grocery shopping', completed: false }
    { task: 'Make dinner', completed: false }
  ]
});

setState('todos', [0, 2], 'completed', true);
// {
//   todos: [
//     { task: 'Finish work', completed: true }
//     { task: 'Go grocery shopping', completed: false }
//     { task: 'Make dinner', completed: true }
//   ]
// }

setState('todos', { from: 0, to: 1 }, 'completed', c => !c);
// {
//   todos: [
//     { task: 'Finish work', completed: false }
//     { task: 'Go grocery shopping', completed: true }
//     { task: 'Make dinner', completed: true }
//   ]
// }

setState('todos', todo => todo.completed, 'task', t => t + '!')
// {
//   todos: [
//     { task: 'Finish work', completed: false }
//     { task: 'Go grocery shopping!', completed: true }
//     { task: 'Make dinner!', completed: true }
//   ]
// }

setState('todos', {}, todo => ({ marked: true, completed: !todo.completed }))
// {
//   todos: [
//     { task: 'Finish work', completed: true, marked: true }
//     { task: 'Go grocery shopping!', completed: false, marked: true }
//     { task: 'Make dinner!', completed: false, marked: true }
//   ]
// }

```



- Immer 启发了 Solid 的 Store 对象的 `produce` API，它允许本地修改状态

```javascript
setState(
  produce((s) => {
    s.user.name = "Frank";
    s.list.push("Pencil Crayon");
  })
);
```

- `reconcile` 用于处理来自 store 或巨大 API 响应这些不可变数据

```javascript
// 订阅一个 observable
const unsubscribe = store.subscribe(({ todos }) => (
  setState('todos', reconcile(todos)));
);
onCleanup(() => unsubscribe());
```

- `createMutable` 创建一个新的可变 Store 代理对象。Store 仅在值更改时触发更新。跟踪是通过拦截属性访问来完成的，并通过代理自动跟踪深度嵌套数据。
- `createMutable` 用于集成外部系统或作为与 MobX/Vue 的兼容层会很有用

```javascript
const state = createMutable(initialValue);

// 读取值
state.someValue;

// 设置值
state.someValue = 5;

state.list.push(anotherValue);
```



## 9. Context

### 9.1 使用Context上下文

- Context 提供了一种依赖注入的形式。它可以用来避免需要通过中间组件将数据作为 props 传递的情况
- 该函数创建了一个新的上下文对象，可以通过 `useContext` 来使用，并提供 `Provider` 控制流。当在层次结构的上方找不到 `Provider` 时，将使用默认上下文

```javascript
# Store

import { createSignal, createContext, useContext } from 'solid-js'

const CounterContext = createContext()

export function CounterProvider(props) {
  console.log(props)
  const [count, setCount] = createSignal(props.count || 0)
  const store = [
    count,
    {
      increment() {
        setCount(c => c + 1)
      },
      decrement() {
        setCount(c => c - 1)
      }
    }
  ]
  return (
    <CounterContext.Provider value={store}>{props.children}</CounterContext.Provider>
  )
}

export function useCounter() {
  return useContext(CounterContext)
}

```

```javascript
# Nested.js

import { useCounter } from './store/counter'

export default function Nested() {
  const [count, { increment, decrement }] = useCounter()
  return (
    <>
      <div>{count()}</div>
      <button onClick={increment}>+</button>
      <button onClick={decrement}>-</button>
    </>
  )
}

```

```javascript
# test.js

import { useCounter } from './store/counter'

export default function Test() {
  const [count, { increment, decrement }] = useCounter()

  return (
    <>
      <div>{count()}</div>
      <button onClick={increment}>add</button>
      <button onClick={decrement}>sub</button>
    </>
  )
}


```



```javascript
# index.js

import { render } from 'solid-js/web'
import { CounterProvider } from './store/counter'
import Nested from './nest'
import Test from './text'

function App() {
  return <>
    <h1>Welcome to Counter App</h1>
    <Nested></Nested>
    <Test></Test>
  </>
}

render(() => (
  <CounterProvider count={100}>
    <App/>
  </CounterProvider>
), document.getElementById('root'))

```



### 9.2 不使用Context

```javascript

import { createSignal } from 'solid-js';

export default createSignal(0);

// 别的地方的代码
import counter from './counter';
const [count, setCount] = counter;
```



## 10.响应式

### 10.1 批量更新

- Solid 的 `batch` 工具函数允许将多个更改推入队列，然后在通知观察者之前同时使用它们。在批处理中更新的信号值直到批处理完成才会提交

```javascript
import { render } from "solid-js/web"
import { createSignal, batch } from "solid-js"

const App = () => {
  const [firstName, setFirstName] = createSignal("John");
  const [lastName, setLastName] = createSignal("Smith");
  const fullName = () => {
    console.log("Running FullName")
    return `${firstName()} ${lastName()}`
  } 
  const updateNames = () => {
    console.log("Button Clicked")
    batch(() => {
      setFirstName(firstName() + "n")
      setLastName(lastName() + "!")
    })
  }
  
  return <button onClick={updateNames}>My name is {fullName()}</button>
};

render(App, document.getElementById("app"))


```



### 10.2 Untrack 

- 有时希望 Signal 读取行为不被跟踪，即使在响应式上下文中也是如此 Solid 提供了 `untrack` 工具函数来避免包装计算跟踪任何读取行为
- 假设我们不想在 `b` 更改时输出日志。我们可以通过将 Effect 更改为以下内容来取消跟踪 `b` Signal

```javascript
import { render } from "solid-js/web";
import { createSignal, createEffect, untrack } from "solid-js";

const App = () => {
  const [a, setA] = createSignal(1);
  const [b, setB] = createSignal(1);

  createEffect(() => {
    console.log(a(), untrack(b));
  });

  return <>
    <button onClick={() => setA(a() + 1)}>Increment A</button>
    <button onClick={() => setB(b() + 1)}>Increment B</button>
  </>
};

render(App, document.getElementById("app"));

```



### 10.3 On

- Solid 提供一个 `on` 工具函数，可以为我们的计算设置显式依赖。这主要用来更明确地简洁地声明跟踪哪些信号。然而，它也允许计算不立即执行而只在第一次更改时运行。可以使用`defer` 选项启用此功能
- createEffect默认跟踪所用被读取的响应式数据，通过on可以指定跟踪的对象

```javascript

import { render } from "solid-js/web"
import { createSignal, createEffect, on } from "solid-js"

const App = () => {
  const [a, setA] = createSignal(1)
  const [b, setB] = createSignal(1)

  createEffect(on(a, (a) => {
    console.log(a, b())
  }, { defer: true }))
  
  return <>
    <button onClick={() => setA(a() + 1)}>Increment A</button>
    <button onClick={() => setB(b() + 1)}>Increment B</button>
  </>
}

render(App, document.getElementById("app"))

```



## 11.异步

### 11.1 懒加载组件

- Solid 的 `lazy` 方法允许包装组件的动态导入来实现延迟加载。然后输出一个可以在 JSX 模板中正常使用的组件，它会在第一次渲染时在内部动态加载底层导入的代码，此时会暂停渲染分支直到代码可用

```javascript
# 一般引入
import Greeting from "./greeting"

# lazy引入
const Greeting = lazy(() => import("./greeting"))

```

```javascript
# 延迟模拟
import { render } from "solid-js/web";
import { lazy } from "solid-js";

const Greeting = lazy(async () => {
  // 模拟延迟
  await new Promise((r) => setTimeout(r, 1000));
  return import("./greeting");
})

function App() {
  return (
    <>
      <h1>Welcome</h1>
      <Greeting name="Jake" />
    </>
  );
}

render(() => <App />, document.getElementById("app"));
```



### 11.2 资源

- Resource 是专门设计用于处理异步加载的特殊 Signal
- 生成的 Resource Signal，还包含响应式 `loading` 和 `error` 属性，可以根据当前状态轻松控制我们的视图
- createResource 返回的第二个值包含一个 `mutate` 方法，用于直接更新内部 Signal ，另外还有一个 `refetch` 方法，即使源没有改变，也可以用它来重新加载当前查询请求

```javascript
import { createSignal, createResource } from "solid-js"
import { render } from "solid-js/web"

const fetchUser = async (id) =>
  (await fetch(`https://swapi.dev/api/people/${id}/`)).json();

const App = () => {
  const [userId, setUserId] = createSignal()
  const [user, { mutate, refetch }] = createResource(userId, fetchUser)

  return (
    <>
      <input
        type="number"
        min="1"
        placeholder="Enter Numeric Id"
        onInput={(e) => setUserId(e.currentTarget.value)}
      />
      <span>{user.loading && "Loading..."}</span>
      <div>
        <pre>{JSON.stringify(user(), null, 2)}</pre>
      </div>
    </>
  )
}

render(App, document.getElementById("app"))
```



### 11.2 Suspense

- Solid 还提供了一种机制来协调多个异步事件的显示。Suspense 作为一个边界，可以在这些异步事件未完成时显示回退占位而不是部分加载的内容
- 触发 `Suspense` 的是异步派生值的读取。不是异步获取行为本身。如果在 `Suspense` 边界下未读取资源 Signal（包括 `lazy` 组件），`Suspense` 将不会挂起

```javascript
# index.js
import { render } from "solid-js/web"
import { lazy, Suspense } from "solid-js"

const Greeting = lazy(async () => {
  // simulate delay
  await new Promise(r => setTimeout(r, 1000))
  return import("./greeting")
})

function App() {
  return (
    <>
      <h1>Welcome</h1>
      <Suspense fallback={<p>Loading...</p>}>
        <Greeting name="Jake" />
      </Suspense>
    </>
  )
}

render(() => <App />, document.getElementById("app"))

# greeting.js
export default function Greeting(props) {
  return <h3>Hi, {props.name}</h3>
}


```

### 11.3 Suspense List (实验)

- ### `SuspenseList` 可以协调多个并行的 `Suspense` 和 `SuspenseList` 组件。它控制显示内容的顺序以减少布局抖动，并且可以通过选项控制折叠或隐藏回退状态


```javascript
<SuspenseList revealOrder="forwards" tail="collapsed">
  <ProfileDetails user={resource.user} />
  <Suspense fallback={<h2>Loading posts...</h2>}>
    <ProfileTimeline posts={resource.posts} />
  </Suspense>
  <Suspense fallback={<h2>Loading fun facts...</h2>}>
    <ProfileTrivia trivia={resource.trivia} />
  </Suspense>
</SuspenseList>
```

### 11.4 Transition

- 用于在所有异步处理完成后在延迟提交事务中批量异步更新。这与 Suspense 有所关联，并且仅跟踪在 Suspense 边界下读取的资源

```javascript
const [isPending, start] = useTransition();

// 检查是否在 transition 中
isPending();

// 包裹在 transition 中
start(() => setSignal(newValue), () => /* transition 完成 */)
```

