- [Vue 基础](#vue-%e5%9f%ba%e7%a1%80)
  - [安装](#%e5%ae%89%e8%a3%85)
  - [Vue实例](#vue%e5%ae%9e%e4%be%8b)
  - [组件](#%e7%bb%84%e4%bb%b6)
  - [可复用性&组合](#%e5%8f%af%e5%a4%8d%e7%94%a8%e6%80%a7%e7%bb%84%e5%90%88)
  - [工具](#%e5%b7%a5%e5%85%b7)
  - [规模化](#%e8%a7%84%e6%a8%a1%e5%8c%96)
  - [深入响应式原理](#%e6%b7%b1%e5%85%a5%e5%93%8d%e5%ba%94%e5%bc%8f%e5%8e%9f%e7%90%86)
  - [对比其它框架](#%e5%af%b9%e6%af%94%e5%85%b6%e5%ae%83%e6%a1%86%e6%9e%b6)
  - [API](#api)
- [Vue源码](#vue%e6%ba%90%e7%a0%81)
  - [数据变化侦听](#%e6%95%b0%e6%8d%ae%e5%8f%98%e5%8c%96%e4%be%a6%e5%90%ac)
  - [Virutal DOM](#virutal-dom)
  - [模板编译](#%e6%a8%a1%e6%9d%bf%e7%bc%96%e8%af%91)
  - [生命周期](#%e7%94%9f%e5%91%bd%e5%91%a8%e6%9c%9f)
  - [computed](#computed)
- [Vue 周边](#vue-%e5%91%a8%e8%be%b9)
  - [vue-loader](#vue-loader)
  - [Vue CLI](#vue-cli)
  - [Vuex](#vuex)
  - [vue-router](#vue-router)
  - [SSR](#ssr)

# Vue 基础

## 安装

(1) 构建文件规范

- `UMD`：通过`<script>`标签可以直接使用在浏览器中，如`vue.js`;
- `CommonJS`: 配合老的打包工具`Browserify`、`webpack1`, 如`vue.common.js`；
- ES Module(基于构建工具)： 现代打包工具，`webapck2+`，`Rollup`，如`vue.esm.js`；
- ES Module(基于浏览器)：用于现代浏览器，通过`<script type="module">`,如`vue.esm.browser.js`

(2) `Vue CLI 3`, 如何配置不同构建版本

​    在 `vue.config.js`中配置节点`runtimeCompiler`,  默认为false, 即默认不需要编译模板，构建大小少`10kb`左右。

------

## Vue实例

**创建实例**

```javascript
var vm = new Vue({
    // 选项
    el: string | Element,
    template: '',
    render: h => h(App),
    data: {},
    props: {},
    computed: {},
    methods: {},
    watch: {}
})
```

其中render的完成函数为：

```javascript
function (createElement) {
	return createElement(app)
}
```

**生命周期**

- new Vue()
- 初始化事件&生命周期
- beforeCreate
- 初始化注入和校验
- created
- 是否指定el,template,渲染render函数
- beforeMount
- 创建 `vm.$el`,并替换`el`
- mounted
- beforeUpdate
- 虚拟DOM重新渲染并应用更新
- updated
- beforeDestroy
- 解除绑定、销毁子组件及事件监听器
- destroyed

**指令**

（1）动态参数

```javascript
<a v-bind:[attributeName]="url"></a>
```

（2）修饰符

- .prevent

**计算属性**

（1）计算属性vs方法

- 计算属性是基于响应式依赖进行缓存的

（2）计算属性只有setter

（3）计算属性vs侦听器

- 当需要在数据变化时执行异步或者开销较大的操作时，需要侦听器

**渲染**

- 不要在v-for中使用计算属性
- 数组索引更新，对象更新用 `Vue.set()`

**事件修饰符**

- passive： 不会阻止默认行为
- once: 只会触发一次
- self: 自身时触发
- exact: 精确修饰符

**表单修饰符**

- lazy： 在change时更新
- number： 数值类型
- trim： 去除首位空白符

## 组件

**组件的复用**

- data必须是一个函数，因此每个实例都是独立的拷贝对象

（1）全局注册

```javascript
Vue.component('my-button', {
    // ... options ...
})
```

（2）局部注册

```javascript
import ComponentA from './Component.vue'

export default {
  components: {
  	ComponentA
  },
  // ...
}
```

（3）在组件上使用v-model

```vue
<!-- 自定义input -->
<template>
	<input :value="value" @input="$emit('input', $event.target.value)">
</template>
<script>
export default {
  props:['value']
}
</script>

// 使用
<custom-input v-model="searchText"></custom-input>

```

 

```vue
<!-- 自定义checkbox -->
<template>
    <input type="checkbox" :checked="checked" @change="$emit('change', $event.target.checked)"
</template>
<script>
export default {
  model: {
      prop: 'checked',
      event: 'change'
  }
  props:['checked']
}
</script>

// 使用
<my-checkbox v-model="value"></my-checkbox>
```

(4) 将原生事件绑定到组件

$listeners

（5）sync修饰符，双向绑定。

**插槽**

`v-slot` 指令自 Vue 2.6.0 起被引入，提供更好的支持 `slot` 和 `slot-scope` attribute 的 API 替代方案

**动态组件**

<keep-alive> 包裹，实现缓存

**异步组件**

require（）

**处理边界情况**

- $root 根实例
- $parent 父组件
- $refs 子组件 （非响应式）
- 依赖注入 provide, inject
- 强制更新 $forceUpdate

## 可复用性&组合

**混入**

```javascript
var mixin = {
  data: function () {
    return {
	  message: 'hello',
      foo: 'abc'
    }
  }
}

new Vue({
  mixins: [mixin]
  data: function() {
 	return {
  	  message: 'goodbye',
      bar: 'def'
	}
  },
  created: function () {
      console.log(this.$data)
      // { message: 'goodbye', foo: 'abc', bar: 'def' }
  }
})
```

**选项合并**

- 数据对象递归合并，组件数据优先；
- 同名钩子函数合并为数组，且混入对象较早调用；
- 对象合并，键名冲突时，取组件对象的键值对
- 自定义合并策略，`Vue.config.optionMergeStrategies`

**自定义指令**

定义： 对普通DOM元素进行底层操作

```javascript
// 自动聚焦指令
Vue.directives('focus', {
    inserted: function (el) {
        el.focus()
    }
})

<input v-focus>
```

**渲染函数 & JSX**

```javascript
// 渲染函数
Vue.component('heading', {
	render: function (h) {
		return h('h' + this.level, this.$slots.default)
	},
	props: {
		level: { type: Number, required: true }
	}
})
```



JSX, [Babel 插件](https://github.com/vuejs/jsx)，用于在 Vue 中使用 JSX 语法

```Vue
import AnchoreHeading from './AnchoreHeading.vue'
new Vue({
	el: '#demo',
	render: function (h) {
		return (
			<AnchoredHeading level={1}>
                <span>Hello World!</span>
			</AnchoredHeading>
		)
	}
})
```

**插件**

**定义**： 通常用来为Vue添加全局功能, 一般有下面几种方法：

- 添加全局方法或者属性
- 添加全局资源：指令、过滤器、过渡等
- 通过全局混入添加一些组件选项
- 添加Vue实例方法，通过把他们添加到`Vue.prototype` 上实现
- 一个库，提供自己的API，同时提供上面提到的一个或者多个功能，如 `vue-router`

**使用插件**: 通过全局方法`Vue.use()`

**开发插件**

```javascript
MyPlugin.install = function (Vue, options) {
    // 1. 添加全局方法或属性
    Vue.myGlobalMethod = function () {
        // 逻辑...
    }
    
    // 2. 注入组件选项
    Vue.mixin({
	 created: function () {
       // 逻辑...    
     }
    })
}
```

**过滤器**

**定义：** 过滤器可以用在两个地方：**双花括号插值和 `v-bind` 表达式** 。过滤器应该被添加在 JavaScript 表达式的尾部。

```Vue
<!-- 在双括号中 -->
{{ message | capittalize }}
```

```javascript
// 全局定义过滤器
Vue.filter('capitalize', function (value) {
    if (!value) return ''
    value = value.toString()
    return value.charAt(0).toUpperCase() + value.slice(1)
})

new Vue({
    // ...
})
```



## 工具

**单文件组件**

优点：

- 完整的语法高亮
- CommonJS模块
- 组件作用域的CSS
- 配合现代化构建工具，提高生产力

**单元测试**

[Vue CLI](https://cli.vuejs.org/zh/) 拥有开箱即用的通过 [Jest](https://github.com/facebook/jest) 或 [Mocha](https://mochajs.org/) 进行单元测试的内置选项。我们还有官方的 [Vue Test Utils](https://vue-test-utils.vuejs.org/zh/) 提供更多详细的指引和自定义设置。

**TypeScript**

[Vue CLI](https://cli.vuejs.org/) 提供了内建的 TypeScript 工具支持。

**[推荐配置](https://cn.vuejs.org/v2/guide/typescript.html#推荐配置)**

```
// tsconfig.json
{
  "compilerOptions": {
    // 与 Vue 的浏览器支持保持一致
    "target": "es5",
    // 这可以对 `this` 上的数据属性进行更严格的推断
    "strict": true,
    // 如果使用 webpack 2+ 或 rollup，可以利用 tree-shake:
    "module": "es2015",
    "moduleResolution": "node"
  }
}
```

## 规模化

- 路由 vue-router
- 状态管理 vuex
- 服务端渲染 Nuxt
- 安全，自动转义+HTTP 安全漏洞（后台）



## 深入响应式原理

（1）原理：Vue将遍历data对象所有的属性，并使用`Object.definePropery`把这些属性全部转为getter/setter. 它们让Vue能够追踪依赖，在属性被访问和修改时通知变更。

（2）检测变化时注意事项

- Vue无法检测到对象属性的添加或者删除
- 动态添加根级别的响应式属性：`Vue.set(object, propertyName, value)`
- 动态添加多个属性： `Object.assign({}, obj, otherObj)`

（3）异步更新队列

- 只要侦听到数据变化，Vue将开启一个队列，并缓冲在同一事件循环中发生的数据变更。
- 同一个watcher被触发多次，只会被推入一次。
- 下一个事件循环中更新DOM, `Vue.nextTick()`
- 异步实现：`Promise.then`、`MutationObserver` 和 `setImmediate`，如果执行环境不支持，则会采用 `setTimeout(fn, 0)` 代替。

```javascript
methods: {
    updateMessage: async function () {
		this.message = 'updated'
         console.log(this.$el.textContent)  // => no update
         await this.$nextTick()
         console.log(this.$el.textContent) // updated
    }
}
```



## 对比其它框架

1. Vue vs React

   - 共同点： 使用Virtual Dom、响应式和组件化的视图组件、注意力集中在核心库，其它交给相关的库，如路由、状态管理等。

   - HTML:  JSX vs Template 

   - CSS: CSS-in-JS vs 作用域内的css

   - 规模： 第三方库都支持，状态管理，路由等； vue有脚手架

   - 原生渲染： React-Native vs Weex

     

2. Vue vs angular 1

   - 复杂性， vue更简单
   - 数据绑定： 双向绑定 vs 单向数据流
   - 指令与组件： Vue中指令只封装DOM操作
   - 运行时性能： Vue不用脏检查，更容易优化

   

3. Vue vs angular 2

   - TypeScript： Angular支持更好
   - 性能都快，体积Vue小一些
   - 学习曲线： Vue容易一些

   

4. Vue vs other

   - Vue性能，响应式比较好，组件化等。

## API

**全局配置**

`Vue.config` 是一个对象，包含 Vue 的全局配置。可以在启动应用之前修改下列属性：

1. silent, 取消Vue所有的日志与警告。
2. errorHandler, 错误追踪；
3. productionTip, 阻止vue在启动时的生成提示；

**全局API**

1. `Vue.extend(options)`

   使用基础 Vue 构造器，创建一个“子类”。参数是一个包含组件选项的对象.

   ```javascript
   import MessageBox from './MessageBox.vue'
   var MBConstor = Vue.extend({
       messageBox
   }) 
   new MBConstor().$amount('#div')
   ```

   

2. `Vue.nextTick([callback, context])`

   下次 DOM 更新循环结束之后执行延迟回调

   ```javascript
   Vue.nextTick()
   	.then(() => { 
       	// 更新了 
   	})
   ```

   

3. `Vue.set(target, perpertyName, value)`

   响应式对象中添加属性，并确保该属性是响应式的

4. `Vue.directive(id, [definition])`

   注册或获取全局指令，

   ```javascript
   // 注册
   Vue.directive('my-directive', {
     bind: function () {},
     inserted: function () {},
     update: function () {},
     componentUpdated: function () {},
     unbind: function () {}
   })
   ```

   

5. `Vue.filter(id, [definition])`

   注册或获取全局过滤器

6. `Vue.component(id, [definition])`

   注册或获取全局组件。注册还会自动使用给定的`id`设置组件的名称

   ```javascript
   // 注册组件，传入一个扩展过的构造器
   Vue.component('my-component', Vue.extend({ /* ... */ }))
   
   // 注册组件，传入一个选项对象 (自动调用 Vue.extend)
   Vue.component('my-component', { /* ... */ })
   
   // 获取注册的组件 (始终返回构造器)
   var MyComponent = Vue.component('my-component')
   ```

   

7. `Vue.use(plugin)`

   安装 Vue.js 插件。如果插件是一个对象，必须提供 `install` 方法。如果插件是一个函数，它会被作为 install 方法。install 方法调用时，会将 Vue 作为参数传入。

   该方法需要在调用 `new Vue()` 之前被调用。

   当 install 方法被同一个插件多次调用，插件将只会被安装一次。

8. `Vue.mixin(mixin)`

   全局注册一个混入，影响注册之后所有创建的每个 Vue 实例。插件作者可以使用混入，向组件注入自定义的行为

9. `Vue.compile(template)`

   将一个模板字符串编译成 render 函数。**只在完整版时可用**。

10. `Vue.opservable(object)`

    让一个对象可响应。Vue 内部会用它来处理 `data` 函数返回的对象。

    ```javascript
    const state = Vue.observable({ count: 0 })
    
    const Demo = {
      render(h) {
        return h('button', {
          on: { click: () => { state.count++ }}
        }, `count is: ${state.count}`)
      }
    }
    ```

**选项/数据**

1. data

   - 组件内只接受函数，因为组件可能被用来创建多个实例。

2. props

   可以是数组或者对象，对象可以配置高级选项。

3. computed

   计算属性将被混入到 Vue 实例中。所有 getter 和 setter 的 this 上下文自动地绑定为 Vue 实例。

4. watch

   一个对象，键是需要观察的表达式，值是对应回调函数。Vue 实例将会在实例化时调用 `$watch()`，遍历 watch 对象的每一个属性。

5. methods

   **不应该使用箭头函数来定义 method 函数**

   箭头函数绑定了父级作用域的上下文，所以 `this` 将不会按照期望指向 Vue 实例

   

**选项/DOM**

1. el

   - 提供一个在页面上已存在的 DOM 元素作为 Vue 实例的挂载目标。可以是 CSS 选择器，也可以是一个 HTMLElement 实例。

   - 在实例挂载之后，元素可以用 `vm.$el` 访问。

   - 如果在实例化时存在这个选项，实例将立即进入编译过程，否则，需要显式调用 `vm.$mount()` 手动开启编译。

2. template

   一个字符串模板作为 Vue 实例的标识使用。模板将会 **替换** 挂载的元素。挂载元素的内容都将被忽略，除非模板的内容有分发插槽。

3. render

   字符串模板的代替方案，允许你发挥 JavaScript 最大的编程能力。该渲染函数接收一个 `createElement` 方法作为第一个参数用来创建 `VNode`

**选项/生命周期**

1. created

   在实例创建完成后被立即调用。在这一步，实例已完成以下的配置：数据观测 (data observer)，属性和方法的运算，watch/event 事件回调。然而，挂载阶段还没开始，`$el` 属性目前尚不可用.

2. mounted

   实例被挂载后调用，这时 `el` 被新创建的 `vm.$el` 替换了。 如果根实例挂载到了一个文档内的元素上，当`mounted`被调用时`vm.$el`也在文档内。

   注意 `mounted` **不会**保证所有的子组件也都一起被挂载。如果你希望等到整个视图都渲染完毕，可以在 `mounted` 内部使用 [vm.$nextTick](https://cn.vuejs.org/v2/api/index.html#vm-nextTick)：

3. updated

   由于数据更改导致的虚拟 DOM 重新渲染和打补丁，在这之后会调用该钩子。

4. destroyed

   实例销毁后调用。该钩子被调用后，对应 Vue 实例的所有指令都被解绑，所有的事件监听器被移除，所有的子实例也都被销毁。

5. xx

**选项/资源**

1. directives
2. filters
3. components

**选项/组合**

1. mixins

   `mixins` 选项接收一个混入对象的数组。这些混入对象可以像正常的实例对象一样包含实例选项，这些选项将会被合并到最终的选项中

2. extends

   允许声明扩展另一个组件(可以是一个简单的选项对象或构造函数)，而无需使用 `Vue.extend`。这主要是为了便于扩展单文件组件。

3. provide/inject

**选项/其它**

1. name

   允许组件模板递归地调用自身,只有作为组件选项时起作用

2. model

   允许一个自定义组件在使用 `v-model` 时定制 prop 和 event

**实例属性**

1. vm.$data

2. vm.$props

3. vm.$el

4. vm.$options

   用于当前 Vue 实例的初始化选项。需要在选项中包含自定义属性

5. vm.$slots

6. vm.refs

   一个对象，持有注册过 [`ref` 特性](https://cn.vuejs.org/v2/api/index.html#ref) 的所有 DOM 元素和组件实例。

7. vm.$listeners

**实例方法/事件**

- vm.$watch

- vm.$set 

  这是全局 `Vue.set` 的**别名**。

- vm.$delete

**实例方法/生命周期**

1. vm.$mount

   手动地挂载一个未挂载的实例

2. vm.$forceUpdate()

   迫使 Vue 实例重新渲染。注意它仅仅影响实例本身和插入插槽内容的子组件，而不是所有子组件。

3. vm.$nextTick()

**指令，特殊特性**

1. v-if

   当和 `v-if` 一起使用时，`v-for` 的优先级比 `v-if` 更高

2. v-slot

3. key

   的特殊属性主要用在 Vue 的虚拟 DOM 算法，在新旧 nodes 对比时辨识 VNodes。如果不使用 key，Vue 会使用一种最大限度减少动态元素并且尽可能的尝试就地修改/复用相同类型元素的算法。而使用 key 时，它会基于 key 的变化重新排列元素顺序，并且会移除 key 不存在的元素。

**内置组件**

1. component

   渲染一个“元组件”为动态组件。依 `is` 的值，来决定哪个组件被渲染。

2. transition

3. transition-group

4. keep-alive

   包裹动态组件时，会缓存不活动的组件实例，而不是销毁它们

5. slot

# Vue源码

##  数据变化侦听

## Virutal DOM

## 模板编译

## 生命周期

## computed

- vue初始化时，会遍历每一个computed中的属性，创建一个侦听watcher；
- 这个watcher中有个关键字段 dirty，表示是否需要计算，默认为true；
- 获取计算属性时，如果dirty为true，会重新计算；否则直接返回当前值；
- 当计算属性依赖的响应式的变量更新时，会通知计算属性watcher，改变dirty为true；

# Vue 周边

## vue-loader

1. 功能： 把.vue文件，解析编译js对象，通过render函数，生成vdom.
2. 实现原理：
   - 通过vue源码中的函数parseComponent(), 分割.vue文件至三部分；
   - template交给vue源码中parseHtml()去解析；
   - style部分交给webpack 中内置的postcss loader解析；
   - script部分交给babel-loader解析；

## Vue CLI

## Vuex

1. 功能：集中式状态管理，多用于多个组件依赖某个属性
2. 特点： 响应式，可回溯
3. 原理：
   - vue.use(), vuex内实现了install方法；
   - 混入beforeCreate勾子，initVuex(), 并且挂载到vm.$store;
   - 添加state到响应式

## vue-router

1. 功能

   - 嵌套路由、模块化路由配置；
   - 自定义视图过渡效果；
   - HTML5的历史模式或hash模式，ie 9中自动降级；
   - 支持路由勾子函数、路由元信息、路由懒加载等；

2. History模式

   - 利用 `history.pushState` API 完成URL跳转
   - 优点：页面地址显示正常，没有hash
   - 缺点： 需要后端支持，如nginx `location / { try_files $url $uri/ /index.html; }`

3. 路由懒加载

   - 原理： Vue的异步组件+webpack的代码分割功能；
   - 实现： `const Foo = () =>import('./Foo.vue')`;

4. 路由实现原理

   - hash模式： Hash模式通过锚点值的改变，根据不同的值，渲染指定DOM位置的不同数据。
   - history模式： 利用 history.pushState API 来完成 URL 跳转而无须重新加载页面。
   - abstract模式：使用一个不依赖于浏览器的浏览历史虚拟管理后端。

5. 源码主要功能：

   - VueRouter原型上提供install方法，供Vue.use 使用；
   - 监听路由变化；
   - 实现`router-view` 和 `router-link`全局组件；
   - 解析路由配置，根据规则找到匹配组件，然后在`router-view`中渲染；
   - 实现路由变量是响应式`Vue.prototype.$route`

   ```javascript
   VueRouter.install = function (_vue) {
   	Vue = _vue;
       Vue.mixin({
           beforeCreate() {
   			if (this.$options.router) {
                   Vue.prototype.$route = this.$options.router;
                   this.$options.router.init();
               }
           }
       })
   }
   ```
## SSR

1. 什么是SSR

   在服务端渲染好HTML所有内容的方式叫做SSR。

2. 为什么要用SSR

   - 更好的SSO，因为搜索引擎爬虫工具可以查看完全渲染好的页面。
   - 首屏加载更快（内容到达时间），无需等待所有js下载完并执行。

3. SSR缺点

   - 开发条件有限制，浏览器特定的代码，只能在某些生命周期函数中使用，如第三方库需要特殊处理。
   - 构建，部署要求更多；非静态资源，需要处于 Node.js server 运行环境
   - 更多的服务器端负载；（缓存策略）

4. 服务器端渲染(SSR) VS 预渲染 （Prerendering）

   - 预渲染更简单，前端依然是完全静态的站点；
   - 预渲染插件： prerender-spa-plugin



# Vue3

## 特点

1. 增加了composition-api，为了解决组件逻辑复用复杂问题；

   核心：把options API 换成 Composition API；灵感来源Reack Hook

2. 




# 经典面试题
1. 为什么说Vue的响应式体系是精确到组件级别的更新？

   ```vue
   <template>
     <div>
     	{{ msg }}
       <ChildComponent></ChildComponent>
     </div>
   </template>
   ```

   **现象**：更新this.msg，会触发当前组件的更新，视图会重新渲染；但是子组件不会重新渲染；

   **原理**：每个组件只收集了当前数据的依赖，子组件的更新只会通过更新props和slot去实现；

   - props更新：子组件初始化时，对props做了响应式监听；
   - slot更新：v2.5 父组件会主动强制更新子组件； v2.6 子组件收集了slot的依赖；

   详解：https://mp.weixin.qq.com/s/wX4bNk9buoSQD2dgT_YOjg

2. xx