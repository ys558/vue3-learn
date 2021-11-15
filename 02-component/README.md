# 组件常用属性及方法，蓝色标题含有Vue3的新特性

## <font color="#5FC7DF">Vue2 和 Vue3 组件化在api上的区别</font>
具体范例见09.Component.html

- vue2 中创建组建的方法：直接Vue自带的静态方法
  ```js
  Vue.component({})
  ```  

- vue3 中创建组件的方法：需要实例化后用 `.component()` 方法：   
  - 全局注册：
  ```js
  const app = Vue.createApp({
    /* ... */
  })
  app.component('component-name', xxComp)
  ```

  - 局部注册
  ```js
  const ComponentA = {
    /* ... */
  }
  const app = Vue.createApp({
    components: {
      'component-a': ComponentA,
    } 
  })
  ```

## props 数据传递
具体范例见09.Component.html

```js
// 数组形式列出的 prop
props: ['title', 'likes', 'isPublished', 'commentIds', 'author']

// 对象形式列出 prop  
props: {
  title: {
    type: String, // 类型
    required: true, // 必填项
  },
  // 简化的写作以下这些形式：
  likes: Number, 
  isPublished: Boolean,
  commentIds: Array,
  author: Object,
  callback: Function,
  contactsPromise: Promise // 或任何其他构造函数
}
```

传值

```html
  <!-- 静态值 -->
  <comp title="1" ></comp>
  <!-- 动态值 -->
  <comp2 
    :title="foo"
    :bar="[1,2,3]" 
    :obj="{bar: '23'}" 
    v-bind="{id:1, xx: 'xx'}">
  </comp2>

  <!-- 其中传入一个对象所有properties -->
  <blog-post v-bind="{
    id: 1,
    title: 'My Journey with Vue'
    }"></blog-post>
  <!--等价于-->
  <blog-post v-bind:id="post.id" v-bind:title="post.title"></blog-post>
```

## <font color="#5FC7DF">组件中的emits自定义事件，可向外部传参</font>
完整范例见11.emits2.html

1. 在父组件声明子组件名字，如下：
```js
<new-fruit-add></new-fruit-add>

components: {
  'new-fruit-add': NewFruitAdd
},
```

2. 子组件中的写法:
```html
<!-- new-fruit-add 子组件 -->
<script type="text/x-template" id="new-fruit-add">
  <input type="text" v-model="newFruit" @keydown.enter="addNewFruit" />
  <button @click="addNewFruit">新增水果</button>
</script>
<script>
  const NewFruitAdd = {
    // 1. 指定模板名字：
    template: '#new-fruit-add',
    data() {
      return {
        newFruit: ''
      }
    },
    emits: ['add'],
    methods: {
      addNewFruit() {
        // 2. 将子组件的数据派发出去$emits, 第一参数为函数名，第二个参数为输入的水果名：
        this.$emit('add', this.newFruit),
        this.course = ''
      }
    }
  }
</script>
```

3. 父组件处理接收到的参数@add, 并命名为自己的名字addNew并在methods中操作, 即`@add="addNew"`
  ```js
  <new-fruit-add @add="addNew"></new-fruit-add>

  methods: {
    addNew(fruit) {
      if (fruit !== '') this.fruits.push(fruit)
    }
  }
  ```

## <font color="#5FC7DF">v-model</font>
具体范例见12.v-model.html

v-model 是一个语法糖
```html
<custom-input v-model="searchText"></custom-input>

<!--等价于--> 
<custom-input
  :model-value="searchText"
  @update:model-value="searchText = $event"
></custom-input>
```

子组件
```js
props: {
  // 1. 作为属性值,用驼峰法命名, 而传递过程中, 会转换为kebab-case写法: new-fruit
  newFruit: {
    type: String,
    required: true,
  },
  // 2. 将派发的事件写在emits属性里
  emits: ['update:new-fruit', 'add']
},
```

经过这一番改造, 使得该组件变得更加通用了, 将外界传递进来的值进行监控, 可以在父组件操作子组件值


## slot 插槽

### 匿名插槽
```html
<!-- 使用 todo-button 组件时传递内容 -->
<todo-button>
  button label
</todo-button>

<!-- todo-button 组件模板中使用slot作为内容出口 --> 
<button class="btn-primary">
  <slot></slot>
</button>

<!-- 未来渲染的 HTML --> 
<button class="btn-primary">
  button label
</button>
```
### 具名插槽

如果一个页面插槽插槽有多个, 那么需要把每个插槽都命名以示区分, 这时需要用到具名插槽

```html
<!-- 展示 -->
<div class="container">
  <header>
    <slot name="header"></slot>
  </header>
  <main>
    <slot></slot>
  </main>
  <footer>
    <slot name="footer"></slot>
  </footer>
</div>

<!-- 实际内容 -->
<layout>
  <!-- 这里 v-slot:header 对应上面的 name="header"-->
  <template v-slot:header>
    <h1>Here might be a page title</h1>
  </template>
  <template v-slot:default>
    <p>A paragraph for the main content.</p>
    <p>And another one.</p>
  </template>
  <template v-slot:footer>
    <p>Here's some contact info</p>
  </template>
</layout>
```

### 作用域插槽

有时插槽内容需要访问子组件中才有的数据，可使用作用域插槽实现。

```js
const Child = {
  template: '<div><slot :foo="foo"></slot></div>',
  data() {
    return {
      foo: 'bar'
} }
}
const Parent = {
  template: `
    <Child>
      <template v-slot:default="slotProps">
        abc
        {{slotProps.foo}}
        efg
      </template>
    </Child> `,
  components: { Child }
}
```