# Vue-router

## 1.基本使用

### HTML

```html
<script src="https://unpkg.com/vue/dist/vue.js"></script>
<script src="https://unpkg.com/vue-router/dist/vue-router.js"></script>

<div id="app">
  <h1>Hello App!</h1>
  <p>
    <!-- 使用 router-link 组件来导航. -->
    <!-- 通过传入 `to` 属性指定链接. -->
    <!-- <router-link> 默认会被渲染成一个 `<a>` 标签 -->
    <router-link to="/foo">Go to Foo</router-link>
    <router-link to="/bar">Go to Bar</router-link>
  </p>
  <!-- 路由出口 -->
  <!-- 路由匹配到的组件将渲染在这里 -->
  <router-view></router-view>
</div>
```

### JavaScript

```javascript
// 0. 如果使用模块化机制编程，导入Vue和VueRouter，要调用 Vue.use(VueRouter)

// 1. 定义 (路由) 组件。
// 可以从其他文件 import 进来
const Foo = { template: '<div>foo</div>' }
const Bar = { template: '<div>bar</div>' }

// 2. 定义路由
// 每个路由应该映射一个组件。 其中"component" 可以是
// 通过 Vue.extend() 创建的组件构造器，
// 或者，只是一个组件配置对象。
// 我们晚点再讨论嵌套路由。
const routes = [
  { path: '/foo', component: Foo },
  { path: '/bar', component: Bar }
]

// 3. 创建 router 实例，然后传 `routes` 配置
// 你还可以传别的配置参数, 不过先这么简单着吧。
const router = new VueRouter({
  routes // (缩写) 相当于 routes: routes
})

// 4. 创建和挂载根实例。
// 记得要通过 router 配置参数注入路由，
// 从而让整个应用都有路由功能
const app = new Vue({
  router
}).$mount('#app')

// 现在，应用已经启动了！
```

## 2.动态路由匹配

### 2.1使用动态路径参数

```javascript
const User = {
    template: '<div>User</div>'
}

const router = new VueRouter({
  routes: [
    // 动态路径参数 以冒号开头
    { path: '/user/:id', component: User }
  ]
})
```

当匹配到参数时，参数值将被设置到`this.$route.params`

```javascript
const User = {
  template: '<div>User {{ $route.params.id }}</div>'
}
```

多段路径匹配

| 模式 | 匹配路径 | $route.params |
| ------ | ------ | ------ |
| /user/:username | /user/evan | `{username:'evan'}`
| /user/:username/post/:post_id | /user/evan/post/123 | `{username: 'evan', post_id: '123'}`

复用组件(**生命周期钩子不会被调用**)

简单watch `$route` 对象：

```javascript
const User = {
  template: '...',
  watch: {
    '$route' (to, from) {
      // 对路由变化作出响应...
    }
  }
}
```

导航守卫

```javascript
const User = {
  template: '...',
  beforeRouteUpdate (to, from, next) {
    // react to route changes...
    // don't forget to call next()
  }
}
```

### 2.2通配符捕获路由

匹配任意路径

```javascript
{
  // 会匹配所有路径
  path: '*'
}
{
  // 会匹配以 `/user-` 开头的任意路径
  path: '/user-*'
}
```

通配符匹配的参数，会被赋值到`pathMatch`

```javascript
// 给出一个路由 { path: '/user-*' }
this.$router.push('/user-admin')
this.$route.params.pathMatch // 'admin'
// 给出一个路由 { path: '*' }
this.$router.push('/non-existing')
this.$route.params.pathMatch // '/non-existing'
```

### 2.3高级路由匹配

使用[path-to-regexp](https://github.com/pillarjs/path-to-regexp)

### 2.4匹配优先级

先匹配到优先级最高

## 3.嵌套路由

实际场景

```bash
/user/foo/profile                     /user/foo/posts
+------------------+                  +-----------------+
| User             |                  | User            |
| +--------------+ |                  | +-------------+ |
| | Profile      | |  +------------>  | | Posts       | |
| |              | |                  | |             | |
| +--------------+ |                  | +-------------+ |
+------------------+                  +-----------------+
```

示例

```javascript
const User = {
  template: `
    <div class="user">
      <h2>User {{ $route.params.id }}</h2>
      <router-view></router-view>
    </div>
  `
}
```

在嵌套的出口渲染组件，需要使用`children`配置

```javascript
const router = new VueRouter({
  routes: [
    { path: '/user/:id', component: User,
      children: [
        {
          // 当 /user/:id/profile 匹配成功，
          // UserProfile 会被渲染在 User 的 <router-view> 中
          path: 'profile',
          component: UserProfile
        },
        {
          // 当 /user/:id/posts 匹配成功
          // UserPosts 会被渲染在 User 的 <router-view> 中
          path: 'posts',
          component: UserPosts
        }
      ]
    }
  ]
})
```

提供空的子路由

```javascript
const router = new VueRouter({
  routes: [
    {
      path: '/user/:id', component: User,
      children: [
        // 当 /user/:id 匹配成功，
        // UserHome 会被渲染在 User 的 <router-view> 中
        { path: '', component: UserHome },

        // ...其他子路由
      ]
    }
  ]
})
```

## 4.编程式导航

### `router.push(location, onComplete?, onAbort?)`

点击`<router-link :to="...">`等同于`router.push(...)`

| 声明式 | 编程式 |
| ------ | ------ |
| `<router-link :to="...">` | `router.push(...)` |

参数选择

```javascript
// 字符串
router.push('home')

// 对象
router.push({ path: 'home' })

// 命名的路由
router.push({ name: 'user', params: { userId: '123' }})

// 带查询参数，变成 /register?plan=private
router.push({ path: 'register', query: { plan: 'private' }})
```

注意点：提供了`path`，`params`的内容会被忽略

```javascript
const userId = '123'
router.push({ name: 'user', params: { userId }}) // -> /user/123
router.push({ path: `/user/${userId}` }) // -> /user/123
// 这里的 params 不生效
router.push({ path: '/user', params: { userId }}) // -> /user
```

### `router.replace(location, onComplete?, onAbort?)`

该方法不向`history`添加新记录，而是替换当前记录

| 声明式 | 编程式 |
| ------ | ------ |
| `<router-link :to="..." replace>` | `router.replace(...)` |

### `router.go(n)`

在`history`记录中向前或者后退多少步，类似于`window.history.go(n)`

## 5.命名路由

使用name

```javascript
const router = new VueRouter({
  routes: [
    {
      path: '/user/:userId',
      name: 'user',
      component: User
    }
  ]
})
```

链接到命名路由

```html
<router-link :to="{ name: 'user', params: { userId: 123 }}">User</router-link>
```

等同于`router.push()`

```javascript
router.push({ name: 'user', params: { userId: 123 }})
```

## 6.命名视图

使用场景：同级渲染多个视图，如：

```html
<router-view class="view one"></router-view>
<router-view class="view two" name="a"></router-view>
<router-view class="view three" name="b"></router-view>
```

多个视图配置多个组件

```javascript
const router = new VueRouter({
  routes: [
    {
      path: '/',
      components: {
        default: Foo,
        a: Bar,
        b: Baz
      }
    }
  ]
})
```

## 7.重定向和别名

### 重定向

普通重定向

```javascript
const router = new VueRouter({
  routes: [
    { path: '/a', redirect: '/b' }
  ]
})
```

命名路由的重定向

```javascript
const router = new VueRouter({
  routes: [
    { path: '/a', redirect: { name: 'foo' }}
  ]
})
```

带方法的动态重定向

```javascript
const router = new VueRouter({
  routes: [
    { path: '/a', redirect: to => {
      // 方法接收 目标路由 作为参数
      // return 重定向的 字符串路径/路径对象
    }}
  ]
})
```

### 别名

`/a`的别名是`/b`，意味着当用户访问`/b`时，URL保持为`/b`，但是路由依然匹配`/a`

```javascript
const router = new VueRouter({
  routes: [
    { path: '/a', component: A, alias: '/b' }
  ]
})
```

## 8.路由组件传参

在组件中使用`$route`会使之与其对应的路由形成高度耦合，从而使组件只能在某些特定URL上使用，限制了灵活性

通过`props`解耦

```javascript
const User = {
  props: ['id'],
  template: '<div>User {{ id }}</div>'
}
const router = new VueRouter({
  routes: [
    { path: '/user/:id', component: User, props: true },

    // 对于包含命名视图的路由，你必须分别为每个命名视图添加 `props` 选项：
    {
      path: '/user/:id',
      components: { default: User, sidebar: Sidebar },
      props: { default: true, sidebar: false }
    }
  ]
})
```

### 布尔模式

如果`props`设置为`true`，`route.params`将会被设置为组件属性

### 对象模式

如果`props`是一个对象，它会被按照原样设置为组件属性，当`props`是静态的时候有用

### 函数模式

可以通过创建一个函数返回props，这样可以将参数转换成为另一种类型，将静态值与基于路由的值相结合等等

```javascript
const router = new VueRouter({
  routes: [
    { path: '/search', component: SearchUser, props: (route) => ({ query: route.query.q }) }
  ]
})
```

## 9.HTML5 History 模式

`vue-router`默认采用hash模式，使用URL的hash来模拟一个完整的URL

另一种路由的**history模式**，充分利用`history.pushState`来完成URL跳转无需重新加载页面

```javascript
const router = new VueRouter({
  mode: 'history',
  routes: [...]
})
```

> 此模式需要后端支持，URL无任何匹配时返回`index.html`
