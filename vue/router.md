
# 目录
- [简介](#简介)
- [基本使用流程](#基本使用流程)
- [动态路由 (路由传参)](#动态路由)
- [路由嵌套](#路由嵌套)
- [编程式导航](#编程式导航)
- [命名路由](#命名路由)
- [命名视图](#命名视图)
- [重定向和别名](#重定向和别名)
- [导航守卫](#导航守卫)



# 简介
## vue-router的优点
  ### 01.Vue在开发时对路由支持的不足。
  ### 02.vue的单页面应用是基于路由和组件的，路由用于设定访问路径，可以将路径和组件映射起来。
  ### 03.在vue-router单页面应用中，路径之间的切换，实际上是组件的切换。
  ### 04.vue-router可以视为WebApp的链接路径管理系统，路由可视为SPA（单页应用）的路径管理器。
  ### 05.传统的页面应用，是用一些超链接来实现页面切换和跳转的，而Vue做的大多是单页应用，只有一个主页面index.html，<a></a>标签是不起作用的。


# 基本使用流程

## 1. 安装：npm install vue-router

## 2. 创建路由组件
```js
import Vue from 'vue' 
import Router from 'vue-router' //引入vue-router

Vue.use(Router)
// const originalPush = Router.prototype.push
// Router.prototype = function push(location) {
//     return originalPush.call(this, location).catch(err => err)
// }

const routes = [ // 准备路由组件
    {
        path: '/Login',
        name: 'login',
        component: () => import('../views/Login.vue')
    },
    {
        path: '/home',
        name: 'home',
        component: () => import('../views/Home.vue')
    },
    {
        path: '/main',
        name: 'main',
        component: () => import('../views/Main.vue')
    },
    {
        path: '/test',
        name: 'test',
        component: () => import('../views/Test.vue')
    }
]

const router = new Router({ routes }) //创建路由对象

export default router // 抛出
```
## 3. 向Vue实例中，注入VueRouter实例
```js
// import Vue from 'vue'
// import App from './App.vue'
// import Antd from 'ant-design-vue'
// import axios from 'axios'
import router from "./router/lazy"
// Vue.config.productionTip = false

// Vue.prototype.$axios = axios
// Vue.use(Antd)

new Vue({
  render: h => h(App),
  router,
}).$mount('#app')

```
## 4.router-link制作导航
```js
  <router-link to="/home">首页</router-link>
  <router-link to="/test">测试页</router-link>
  <router-link to="/login">登录页</router-link>
  <router-link to="/main">主页</router-link>
    //1.router-link 是一个组件，它默认会被渲染成一个带有链接的a标签，通过to属性指定链接地址。
    // 注意：被选中的router-link将自动添加一个class属性值.router-link-active。


    // 2.<router-link to="/">[text]<router-link/>
    // + to：导航路径，要填写的是你在router/index.js文件里配置的
    // + path值，如果要导航到默认首页，只需要写成 to="/" ，
    // [text] ：就是我们要显示给用户的导航名称。

  <router-view></router-view>
    // router-view 用于渲染匹配到的组件。
```



# 动态路由
  ## 1. 设置形参
  ```js
      {
        path:'/test/:id',
        component:Test
      }
  ```
  ## 2. 传递实参
  ```js
  <router-link to="/test/007">测试组件</router-link>
  ```
  ## 3. 在目标组件中，提取参数，并使用
  ```js
  let testData = this.$route.params;  //提取方式
  ```
  ## 4. 对应关系
  | 模式 | 匹配路径 | $route.params|
  | --- | --- | --- |
  | /test/:id  | /test/123 | { id: 123 } |
  | /test/:id/mode/:lay_out  | /test/123/mode/up_down | { id: 123, lay_out: up_down } |

# 路由嵌套

实际项目通常由多层嵌套的组件组合而成。同样地，URL 中各段动态路径也按某种结构对应嵌套的各层组件，例如：
```js
/user/foo/profile                     /user/foo/posts
+------------------+                  +-----------------+
| User             |                  | User            |
| +--------------+ |                  | +-------------+ |
| | Profile      | |  +------------>  | | Posts       | |
| |              | |                  | |             | |
| +--------------+ |                  | +-------------+ |
+------------------+                  +-----------------+
```
借助 vue-router，使用嵌套路由配置，就可以很简单地表达这种关系。

如果<router-view> 是最顶层的出口，渲染最高级路由匹配到的组件。同样地，一个被渲染组件同样可以包含自己的嵌套 <router-view>, 渲染与之匹配到的子组件。
```js

<div id="app">
  <router-view></router-view> // 这里的是最顶层的出口
</div>
const User = {
  template: '<div>User {{ $route.params.id }}</div>'
}

const router = new VueRouter({
  routes: [{ path: '/user/:id', component: User }]
})

// 在 User 组件的模板添加一个 <router-view>
// 渲染与之匹配到的子组件。
const User = {
  template: `
    <div class="user">
      <h2>User {{ $route.params.id }}</h2>
      <router-view></router-view>
    </div>
  `
}
```
要在嵌套的出口中渲染组件，需要在 VueRouter 的参数中使用 children 配置：

```js
const router = new VueRouter({
  routes: [
    {
      path: '/user/:id',
      component: User,
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
要注意，以 / 开头的嵌套路径会被当作根路径。 这让你充分的使用嵌套组件而无须设置嵌套的路径。

你会发现，children 配置就是像 routes 配置一样的路由配置数组，所以呢，你可以嵌套多层路由。

此时，基于上面的配置，当你访问 /user/foo 时，User 的出口是不会渲染任何东西，这是因为没有匹配到合适的子路由。如果你想要渲染点什么，可以提供一个 空的 子路由：
```js
const router = new VueRouter({
  routes: [
    {
      path: '/user/:id',
      component: User,
      children: [
        // 当 /user/:id 匹配成功，
        // UserHome 会被渲染在 User 的 <router-view> 中
        { path: '', component: UserHome }

        // ...其他子路由
      ]
    }
  ]
})
```

# 编程式导航
除了使用 <router-link> 创建 a 标签来定义导航链接，我们还可以借助 router 的实例方法，通过编写代码来实现。

## 1.router.push()
在 Vue 实例内部，你可以通过 $router 访问路由实例。因此你可以调用 this.$router.push。


想要导航到不同的 URL，则使用 router.push 方法。这个方法会向 history 栈添加一个新的记录，所以，当用户点击浏览器后退按钮时，则回到之前的 URL。

当你点击 <router-link> 时，这个方法会在内部调用，所以说，点击 <router-link :to="..."> 等同于调用 router.push(...)。

  | 声明式 | 编程式 |
  | --- | --- |
  | `<router-link :to="...">`  | router.push(...) |

**注意：如果提供了 path，params 会被忽略，query 则不会。取而代之的是提供路由的 name 或手写完整的带有参数的 path：**
```js
const userId = '123'
router.push({ name: 'user', params: { userId }}) // -> /user/123
router.push({ path: `/user/${userId}` }) // -> /user/123
router.push({ path: 'user', query: { userId }}) // -> /user?userId=123
// 这里的 params 不生效
router.push({ path: '/user', params: { userId }}) // -> /user
```	
## 2.router.replace()
跟 router.push 很像，唯一的不同就是，它不会向 history 添加新记录，而是跟它的方法名一样 —— 替换掉当前的 history 记录。

  | 声明式 | 编程式 |
  | --- | --- |
  | `<router-link :to="..." replace>`  | router.replace(...) |

## 3.router.go(n)
这个方法的参数是一个整数，意思是在 history 记录中向前或者后退多少步，类似 window.history.go(n)。
```js

// 在浏览器记录中前进一步，等同于 history.forward()
router.go(1)

// 后退一步记录，等同于 history.back()
router.go(-1)

// 前进 3 步记录
router.go(3)

// 如果 history 记录不够用，那就默默地失败呗
router.go(-100)
router.go(100)
```
## 4.操作 History
你也许注意到 router.push、 router.replace 和 router.go 跟 window.history.pushState、 window.history.replaceState 和 window.history.go (opens new window)好像， 实际上它们确实是效仿 window.history API 的。

因此，如果你已经熟悉 Browser History APIs (opens new window)，那么在 Vue Router 中操作 history 就是超级简单的。

还有值得提及的，Vue Router 的导航方法 (push、 replace、 go) 在各类路由模式 (history、 hash 和 abstract) 下表现一致

# 命名路由
1. 为路由对象设置name
```
{
  path:'/test',
  name:'test',
  component:Test
},
```

2. 可以使用name切换路由

声明式
```js
<router-link :to="{ name: 'test', params: { testId: 123 }}">test007</router-link>
```
编程式
```js
  this.$router.push({
    name:test,
    params:{  //刷新后会丢失
      id:3333
    },
    query:{  //刷新后依旧可以保留
      num:100
    }
  })
```

# 命名视图
## 1. 介绍
下图页面想要点击aside切换页面，只有content区域更新                       
```js
+-----------------------------+                
| nav1                        |                
|----------- +--------------- |                 
|    aside1  | content        |          
|            |                |                 
|    aside2  |                |                 
|----------- +----------------|
```
这个时候命名视图就派上用场了。你可以在界面中拥有多个单独命名的视图，而不是只有一个单独的出口。如果 router-view 没有设置名字，那么默认为 default。

## 2. 路由对象的多组件配置
```js
{
  path:'/',
  components:{
    'nav':Nav,
    'aside':Aside,
    default:Content
  }
}
```

## 3. 使用多个命名的router-view渲染视图
```js
<router-view class="nav" name="nav"></router-view>
<router-view class="aside" name="aside"></router-view>
<router-view class=" content"></router-view>
```

## 4. 完整示例
```js
<div id="app">
  // 顶部导航栏
  <router-view name="nav">sdfgasg</router-view> 
  // 侧边导航栏
  <router-view name="aside"></router-view>
  // 不带名字渲染的就是default里面的东西
  <router-view></router-view>   
</div>

```
```js
  // 公共组件
  let Nav = {
    template:'<div class="nav">顶部导航栏</div>'
  }
  let Aside = {
    template:`<div class="aside">
              <ul>
                <li>
                  <router-link to="/">产品首页</router-link>
                </li>
                <li>
                  <router-link to="/detail">产品详情</router-link>
                </li>
                <li>
                  <router-link to="/config">产品配置</router-link>
                </li>
              </ul>
            </div>`
  }

  // 业务组件
  let Home = {
    template:'<div class="home">产品首页组件</div>'
  }
  let Detail = {
    template:'<div class="detail">产品详情组件</div>'
  }
  let Config = {
    template:'<div class="config">产品配置组件</div>'
  }
  let router = new VueRouter({
    routes:[
      {
        path:'/',
        components:{
          'nav':Nav,
          'aside':Aside,
          default:Home
        }
      },
      {
        path:'/detail',
        components:{
          'nav':Nav,
          'aside':Aside,
          default:Detail
        }
      },
      {
        path:'/config',
        components:{
          'nav':Nav,
          'aside':Aside,
          default:Config
        }
      }
    ]
  })
  new Vue({
    el:'#app',   //占地盘
    router,
    data:{      // 小金库
      'msg':'Hello'
    }
  })
```

![图列](./img/example.png)

# 重定向和别名

## “重定向”的意思是，当用户访问 /a时，URL 将会被替换成 /b，然后匹配路由为 /b，它有三种方式

### 第一种 path重定向
```js
const router = new VueRouter({
  routes: [
    { path: '/source', redirect: '/target' }
  ]
})
```

### 第二钟 name重定向

```js
const router = new VueRouter({
  routes: [
    { path: '/source', redirect: { name: 'target' }}
  ]
})
```
### 第三种 动态返回重定向目标：

```js
const router = new VueRouter({
  routes: [
    { path: '/source', redirect: to => {
      // 方法接收 目标路由 作为参数
      // return 重定向的 字符串路径/路径对象
    }}
  ]
})
```

## 别名
/a 的别名是 /b，意味着，当用户访问 /b 时，URL 会保持为 /b，但是路由匹配则为 /a，就像用户访问 /a 一样。

```js
const router = new VueRouter({
  routes: [
    { path: '/a', component: A, alias: '/b' }
  ]
})


// https://xxx/a 和 https://xxx/b 访问的地址一致
```

# 导航守卫
>导航守卫主要用来通过跳转或取消的方式守卫导航。有多种机会植入路由导航过程中：全局的, 单个路由独享的, 或者组件级的。

## 全局前置守卫
>router.beforeEach 当一个导航触发时，全局前置守卫按照创建顺序调用。守卫是异步解析执行，此时导航在所有守卫 resolve 完之前一直处于 等待中。

```js
const router = new VueRouter({ ... })

router.beforeEach((to, from, next) => {
  if (to.name !== 'Login' && !isAuthenticated) next({ name: 'Login' })
  else next()
})
```

每个守卫方法接收三个参数：

+ to: Route: 即将要进入的目标 路由对象

+ from: Route: 当前导航正要离开的路由

+ next: Function: 一定要调用该方法来 resolve 这个钩子。执行效果依赖 next 方法的调用参数。 
  +  next(): 进行管道中的下一个钩子。如果全部钩子执行完了，则导航的状态就是 confirmed (确认的)。

  + next(false): 中断当前的导航。如果浏览器的 URL 改变了 (可能是用户手动或者浏览器后退按钮)，那么 URL 地址会重置到 from 路由对应的地址。

  + next('/') 或者 next({ path: '/' }): 跳转到一个不同的地址。当前的导航被中断，然后进行一个新的导航。

  + next(error): (2.4.0+) 如果传入 next 的参数是一个 Error 实例，则导航会被终止且该错误会被传递给 router.onError() 注册过的回调。

## 全局后置钩子
>你也可以注册全局后置钩子，然而和守卫不同的是，这些钩子不会接受 next 函数也不会改变导航本身：
```js
router.afterEach((to, from) => {
  // ...
})
```
## 路由独享的守卫
>在路由配置上直接定义 beforeEnter 守卫：

```js
const router = new VueRouter({
  routes: [
    {
      path: '/foo',
      component: Foo,
      beforeEnter: (to, from, next) => {
        // 这些守卫与全局前置守卫的方法参数是一样的。
      }
    }
  ]
})
```

## 组件内的守卫
>在路由组件内直接定义路由导航守卫：

+ beforeRouteEnter
+ beforeRouteUpdate (2.2 新增)
+ beforeRouteLeave

```js
const Foo = {
  template: `...`,
  beforeRouteEnter(to, from, next) {
    // 在渲染该组件的对应路由被 confirm 前调用
    // 不！能！获取组件实例 `this`
    // 因为当守卫执行前，组件实例还没被创建
  },
  beforeRouteUpdate(to, from, next) {
    // 在当前路由改变，但是该组件被复用时调用
    // 举例来说，对于一个带有动态参数的路径 /foo/:id，在 /foo/1 和 /foo/2 之间跳转的时候，
    // 由于会渲染同样的 Foo 组件，因此组件实例会被复用。而这个钩子就会在这个情况下被调用。
    // 可以访问组件实例 `this`
  },
  beforeRouteLeave(to, from, next) {
    // 导航离开该组件的对应路由时调用
    // 可以访问组件实例 `this`
  }
}
```
beforeRouteEnter 守卫 不能 访问 this，因为守卫在导航确认前被调用，因此即将登场的新组件还没被创建。

不过，你可以通过传一个回调给 next来访问组件实例。在导航被确认的时候执行回调，并且把组件实例作为回调方法的参数。

```js
beforeRouteEnter (to, from, next) {
  next(vm => {
    // 通过 `vm` 访问组件实例
  })
}
```
注意 beforeRouteEnter 是支持给 next 传递回调的唯一守卫。对于 beforeRouteUpdate 和 beforeRouteLeave 来说，this 已经可用了，所以不支持传递回调，因为没有必要了。

```js
beforeRouteUpdate (to, from, next) {
  // just use `this`
  this.name = to.params.name
  next()
}
```
这个离开守卫通常用来禁止用户在还未保存修改前突然离开。该导航可以通过 next(false) 来取消。

```js
beforeRouteLeave (to, from, next) {
  const answer = window.confirm('Do you really want to leave? you have unsaved changes!')
  if (answer) {
    next()
  } else {
    next(false)
  }
}
```
## 完整的导航解析流程
1.导航被触发。

2.在失活的组件里调用 beforeRouteLeave 守卫。

3.调用全局的 beforeEach 守卫。  

4.在重用的组件里调用 beforeRouteUpdate 守卫 (2.2+)。  

5.在路由配置里调用 beforeEnter。

6.解析异步路由组件。

7.在被激活的组件里调用 beforeRouteEnter。

8.调用全局的 beforeResolve 守卫 (2.5+)。

9.导航被确认。

10.调用全局的 afterEach 钩子。

11.触发 DOM 更新。

12.调用 beforeRouteEnter 守卫中传给 next 的回调函数，创建好的组件实例会作为回调函数的参数传入。