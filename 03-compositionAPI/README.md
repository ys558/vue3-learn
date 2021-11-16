## 函数式编程 Composition API

### 由于是函数式编程`this` 可全局省略

先和react hooks对比其相似性:

```js
// React
function Component(props) {
  const [a, setA] = useState(0)
  return <a onClick={() => setA(x => x + 1)}>{a}</a>
}
```

```js
// Vue 3 composition API
function Component(props) {
  const a = ref(0)
  const setA = () => a.value++
  return <a onClick={setA}>{a}</a>
}
```

虽然有所累死，但React编程范式更接近于纯函数，Vue 则采取了 Reactivity 的模式。

### `setup` 中的参数

依次为:

- props
- ctx


其中props不是普通参数, 是一个代理 Proxy, 所以使用props里面的东西不能直接在括号里直接解构, 应为: props.XXX

第二个参数, ctx 为普通参数, 可以直接解构, 如下:

```js
setup(props, {attrs, slots, emit}){

}
```