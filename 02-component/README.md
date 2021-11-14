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

## <font color="#5FC7DF">组件中的emits自定义事件，组件向外部传参</font>


