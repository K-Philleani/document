# Redux

## 1.什么是Redux

> Redux 是 JavaScript 应用的状态容器，提供可预测的状态管理


- JavaScript开发的应用程序，状态变得难以管理，包括：
	1. 需要管理的状态越来越多，越来越复杂
	2. 服务器返回的数据，缓存数据，用户操作的数据，UI状态的数据等等
- 管理不断变化的状态是十分困难的：
	1. 状态之间存在相互依赖，状态同样也受视图的影响
	2. 对于状态的改变原因和方式难以追踪
- React负责视图帮助解决DOM的渲染过程，但是state依然需要我们自己管理
	1. 通信可以通过props,Context等方式
- Redux就是一个帮助管理state的容器

## 2.工作流程

![redux原理图](https://s2.loli.net/2021/12/29/sCOwjS9xgP7AifU.png)

## 3. redux的三个核心概念

### 3.1 action 

- action 就是一个普通 JavaScript 对象,用来描述更新的类型和内容
- 包括两个属性：
	1. type: 标识属性，值是一个字符串，用于标识需要使用的action，唯一值，必传属性
	2. data: 数据属性, 值可以使任意数据类型，用来传递数据给action操作，可选属性
-   例子：

```JavaScript
{ type: 'CHNAGE_COUNT', data: { count: 1 } }

```


- Redux要求通过action来更新数据
	1. 所有数据的变化必须通过dispatch派发action来更新

### 3.2 reducer

- reducer就是一个纯函数，负责将state和action联系在一起
- reducer就是将传入的state和action结合起来生成新的state
## 4.redux的三大原则
- 单一数据源
	1. 整个应用程序的state被存储在一颗object tree中，并且这个object tree值存储在一个store中
	2. redux并没有强制不能创建多个store，但是多个store不利于数据的维护
	3. 单一的数据源可以让整个程序的state变得方便维护，追踪，修改
- State是只读的
	1. 唯一修改state的方式一定是触发action，不要试图通过其他方式修改state
	2. 这样可以确保View或网络请求都不能直接修改state，只能通过action来描述如何修改state
	3. 这样可以保证所有的修改都被集中处理，并且按照严格的顺序来执行，所以可以不用担心race condition(竞态)的问题
- 使用纯函数来执行修改
	1. 通过reducer将旧的state和action联系在一起，并且返回一个新的state
	2. 随着应用程序的复杂程度，我们可以将reducer拆分成多个小的reducer，分别操作不同的state 部分
	3. 所有的reducer都应该是纯函数，不能产生任何副作用

## 5.redux的核心API



