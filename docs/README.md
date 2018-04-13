import wepyx, { connect } from 'wepyx'

### `wepyx.init(options)`

Arguments

* extraMiddlewares(Array): 额外的中间件

### `wepyx.model(options)`

Arguments

* `namespace(String)`: 命名空间
* `state(Object)`: model 的数据结构
* `mutations(Object)`: 数据的修改[copy-on-write](https://en.wikipedia.org/wiki/Copy-on-write)
  * `handleActionName(String)-reducer(Function)[state, payload]`

reducer 内部使用 [immer](https://github.com/mweststrate/immer) 进行包装，可以[直接对 state 进行赋值](https://github.com/tolerance-go/wepyx/blob/fa32121d88142b80d003ca2875b53dabb8d26622/__test__/index.test.js#L19)，支持深度拷贝，[如果直接返回新值会替换原来的 state](https://github.com/tolerance-go/wepyx/blob/fa32121d88142b80d003ca2875b53dabb8d26622/__test__/index.test.js#L220)
自动生成同名的 actionCreator，默认为 [payload => payload](https://github.com/tolerance-go/wepyx/blob/fa32121d88142b80d003ca2875b53dabb8d26622/src/index.js#L72)

* `actions(Object)`: 事件生成器
  * `actionName(String)-actionCreator(Function)[() => object|function[{ take, dispatcher, state, getState, eventBus }]]`

action 生成器，如果和 namespace 下的 mutation 属性同名，将会覆盖自动生成的 actionCreator。

take 返回一个 promise 对象，可以对 EventBus 上的事件任何事件进行监听。

state 是当前 model 的数据，getState 动态获得 rootState

* `setups(Object|Function)`: 启动器，所有函数在 launch 之后会调用
  * `key(String)-set(Function)[({ dispatcher, take, eventBus }) => void]`

### `wepyx.start()`

启动程序，最后调用

### `wepyx.eventBus`

这是一个内部实现需要的 事件中心，take 就是基于它实现的。所有的 redux action 都会自动派发响应事件名，store 变化之后，会派发 `${actionName}:after` 事件

`eventBus.listen(type, cb, [scope]) => unlisten(Function)`: 开启监听

`eventBus.emit(type, payload)`: 派发事件

`eventBus.take(type) => chained(Promise)`: 监听一次事件，事件发生之后监听会被自动移除

### `connect`

基于 [`wepy-redux`](https://github.com/Tencent/wepy/tree/2.0.x/packages/wepy-redux#wepy-%E5%92%8C-redux-%E7%BB%93%E5%90%88%E7%9A%84%E8%BF%9E%E6%8E%A5%E5%99%A8)，另外融入了 dispatcher，可以在 connect 过后的组件内部，使用 [`this.dispatcher`](https://github.com/tolerance-go/wepyx/blob/fa32121d88142b80d003ca2875b53dabb8d26622/examples/src/components/counter.wpy#L80)，去除了第二个参数。