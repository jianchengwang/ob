---
title: intro-to-vue
slug: intro-to-vue
author: [jianchengwang]
date: 2020-09-13
excerpt: "最近喵了下introducction to vue.js视频感觉还不错，下面主要根据视频作者Sarah Drasner的ppt简单做下笔记，当然如果要系统过一遍的话还是推荐看官方文档。"
draft: false
tags: [frontend]
---

最近喵了下 `introducction to vue.js` 视频感觉还不错，如果感兴趣，[请戳](https://frontendmasters.com/courses/vue/)。下面主要根据视频作者`Sarah Drasner` 的`ppt` 简单做下笔记，当然如果要系统过一遍的话还是推荐看官方文档。

## Directives & Data Rendering

很多指令官方文档都有很详细的介绍，这里不多赘述。

| Name                    | Shortcut | Purpose                                                      | Example                                                      |
| :---------------------- | -------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| `v-if,v-else-if,v-else` | none     | Conditional Rendering                                        | `<g v-if="condition==='A'" /><g v-else-if="condition==='B'" /><g v-else />` |
| `v-bind`                | `:`      | Bind attributes  dynamically or pass props                   | `<div :style="{color: red}"></div>`                          |
| `v-on`                  | `@`      | Attaches an event listener to the element                    | `<button @click=""></button>`                                |
| `v-model`               | none     | Creates two-way binding，like combian with`v-bind` and `v-on` | `<input v-model="message" />`                                |
| `v-pre`                 | none     | Skip compilling for raw content, can boost performance       | `<div v-pre>row content with not methods</div>`              |
| `v-show`                | none     | Will show or hide the `component/element` based on state, but will leave it int the `DOM` without unmounting(unlike `v-if`) | `<child v-show="showComponent"></child>`                     |
|                         |          |                                                              |                                                              |

## Methods, Computed, Watch

### methods

```js
 methods: {
     addComment() {
         this.comments.push(this.newComment)
         this.newComment = ''
     }
 }
```

### computed

`Computed` properties are calculations that will be` cached` and will only update when needed.Highly performant but use with understandin

```js
computed: {
  filteredFilms() {
    let filter = new RegExp(this.filterText, 'i')
    return this.ratingsInfo.filter(el => el.title.match(filter))
  }
}
```

### watch

`Reactive programming` is programming with asynchronous data streams. (响应式编程)

Vue.js, MobX or Ractive.js all use a variation of **getters/setters**. (对象原型链get,set方法)

Vue takes the object, walks through its properties and converts them to **getter/setters**

```js
new Vue({
  data: {
    counter: 0
  }
})
```

`Vue` cannot detect property addition or deletion so we create this object to keep track

---

1. Each component has a `watcher instance`.

2. The `properties` touched by the `watcher` during the `render` are registered as dependencies

3. When the `setter` is triggered, it lets the `watcher` know, and causes the component to re-render.

---

The `Vue instance` is the middleman between the `DOM` and `the business logic`

`Watch` updates the `DOM` only if it's required- performing calculations in JS is really performant but accessing the DOM is not. So we have a `Virtual DOM which` is like a copy, but parsed in JavaScript

It will only update the `DOM`for things that need to be changed, which is faster.

```js
watch {
    counter(value, oldValue) {
        console.log('The counter has changed')
    }
}
```

## Components

int the short, A collection of elements that are encapsulated into a group that can be accessed through one single element.(节点，功能的集合)

### template

Vue.js uses `HTML-based template syntax` to bind the Vue instance to the DOM, very useful for components.

`Templates` are optional, you can also [write render functions](https://vuejs.org/v2/guide/render-function.html) with optional `JSX` support.

### render function

```html
<h1>blogTitle</h1>
```

```js
render: function (createElement) {
  return createElement('h1', this.blogTitle)
}
```

用到的的时候喵下`createElement` 方法文档用法即可，

```js
// @returns {VNode}
createElement(
  // {String | Object | Function}
  // 一个 HTML 标签名、组件选项对象，或者
  // resolve 了上述任何一种的一个 async 函数。必填项。
  'div',

  // {Object}
  // 一个与模板中属性对应的数据对象。可选。
  {
    // (详情见下一节)
  },

  // {String | Array}
  // 子级虚拟节点 (VNodes)，由 `createElement()` 构建而成，
  // 也可以使用字符串来生成“文本虚拟节点”。可选。
  [
    '先写一些文字',
    createElement('h1', '一则头条'),
    createElement(MyComponent, {
      props: {
        someProp: 'foobar'
      }
    })
  ]
)
```

### data

Each `component instance`has its own `isolated scope`, so `data` must be a `function`.

```js
data() {
    return {
        counter: 0
    }
}
```

### props

Passing data down from the parent to the child.(父组件传参给子组件)

```js
props: [
    'prop1',
    'prop2'
]
```

or `props` have `type & validation `

```js
props: {
  text: [String, Number]
}

props: {
    text: {
        type: String,
        required: true,
        default: 'hello mr. magoo'
    }
}
```

`Objects and Arrays` need their `defaults` to be returned a `function`

```js
props: {
    text: {
      type: Object,
      default() {
        return { message: 'hello mr. magoo' }
      }
    }
}
```

use static value

```js
<child count="1"></child>
```

use the state of the parent, We use `v-bind`or` :` to dynamically bind props to data on the parent

```js
<child :count="count"></child>
```

### $emit

 The child is reporting it's activity to the parent by `$emit` (子组件传值给父组件)

```html
<child @myEvent="parentHandler"></child>
```

```js
methods: {
  fireEvent() {
    this.$emit('myEvent', eventValueOne, eventValueTwo);
  }
}
```

### slot

插槽，相对灵活，简单来说，就是定义一块`html模板`，由父组件决定是否显示，该怎么显示。

```html
<script type="text/x-template" id="childarea">
  <div class="child">
    <slot></slot>
    <p>It's a veritable slot machine!<br> 
    Ha ha aw</p>
  </div>
</script>
```

```html
<div id="app">
  <h2>We can use slots to populate content</h2>
  <app-child>
    <h3>This is slot number one</h3>
  </app-child>
  <app-child>
    <h3>This is slot number two</h3>
    <small>I can put more info in, too!</small>
  </app-child>
</div>
```

可以由默认插槽，匿名插槽

```html
<slot>default slot</slot>
```

当然也可以有多个插槽，需要命名

```html
<slot name="headerinfo"></slot>
<h1 slot="headerinfo">I will populate the headerinfo slot</h1>
```

也可以传数据，当然数据只能是子组件内部的数据，父组件提供样式。

```html
<slot name="up" :data="data"></slot>
```

### keep-alive

可以使被包含的组件保留状态，或避免重新渲染

```html
<keep-alive include="a">
  <component>
    <!-- name 为 a 的组件将被缓存！ -->
  </component>
</keep-alive>
```

```html
<keep-alive include="a">
  <component>
    <!-- 除了name 为 a 的组件将被缓存！ -->
  </component>
</keep-alive>
```

跟`router`结合，实现有的路由视图缓存，仅限单页面应用。

```js
// routes 配置
export default [
  {
    path: '/',
    name: 'home',
    component: Home,
    meta: {
      keepAlive: true // 需要被缓存
    }
  }, {
    path: '/:id',
    name: 'edit',
    component: Edit,
    meta: {
      keepAlive: false // 不需要被缓存
    }
  }
]
```

```html
<keep-alive>
    <router-view v-if="$route.meta.keepAlive">
        <!-- 这里是会被缓存的视图组件，比如 Home！ -->
    </router-view>
</keep-alive>

<router-view v-if="!$route.meta.keepAlive">
    <!-- 这里是不被缓存的视图组件，比如 Edit！ -->
</router-view>
```

## Filters, Mixins, Directive, & Plugin

### filters

The first thing to understand about filters is that they aren't replacements for methods, computed values, or watchers, **because filters don't transform the data, just the output that the user sees.**(过滤器不转换数据，只过滤输出)

Two ways to register a new filter:

```js
//global
Vue.filter('filterName', function(value,arg1,arg2) {
  return // thing to transform
});
 
//locally, like methods or computed
filters: {
  filterName(value,arg1,arg2) {
    return // thing to transform
  }
}

// use
:="data | filterA | filterB(arg1,arg2)"
```

### mixins

这是一种常见的情况:您有两个非常相似的组件，它们共享相同的基本功能，但是它们之间有足够多的不同之处，因此您面临一个十字路口:我要将这个组件分成两个不同的组件吗?或者我保留一个组件，但创建足够的变化与道具，我可以改变每一个。

`mixin`允许您封装一项功能，以便可以在整个应用程序的不同组件中使用它。

```js
//modal
const Modal = {
  template: '#modal',
  data() {
    return {
      isShowing: false
    }
  },
  methods: {
    toggleShow() {
      this.isShowing = !this.isShowing;
    }
  },
  components: {
    appChild: Child
  }
}

//tooltip
const Tooltip = {
  template: '#tooltip',
  data() {
    return {
      isShowing: false
    }
  },
  methods: {
    toggleShow() {
      this.isShowing = !this.isShowing;
    }
  },
  components: {
    appChild: Child
  }
}
```

可以看到冗余的代码片段可以封装起来，改写后如下，

```js
const toggle = {
  data() {
    return {
      isShowing: false
    }
  },
  methods: {
    toggleShow() {
      this.isShowing = !this.isShowing;
    }
  }
}

const Modal = {
  template: '#modal',
  mixins: [toggle],
  components: {
    appChild: Child
  }
};

const Tooltip = {
  template: '#tooltip',
  mixins: [toggle],
  components: {
    appChild: Child
  }
};
```

默认情况下，`mixin` 将首先应用，然后应用`component`，这样我们就可以根据需要覆盖它。

```js
//mixin
const hi = {
  mounted() {
    console.log('hello from mixin!')
  }
}

//vue instance or component
new Vue({
  el: '#app',
  mixins: [hi],
  mounted() {
    console.log('hello from Vue instance!')
  }
});

//Output in console
//> hello from mixin!
//> hello from Vue instance!
```

```js
//mixin
const hi = {
  methods: {
    sayHello: function() {
      console.log('hello from mixin!')
    }
  },
  mounted() {
    this.sayHello()
  }
}

//vue instance or component
new Vue({
  el: '#app',
  mixins: [hi],
  methods: {
    sayHello: function() {
      console.log('hello from Vue instance!')
    }
  },
  mounted() {
    this.sayHello()
  }
})

// Output in console
//> hello from Vue instance!
//> hello from Vue instance!
```

`Global Mixins` 会应用于每一个组件，不过一般很少用到

```js
Vue.mixin({
  mounted() {
    console.log('hello from mixin!')
  }
})

new Vue({
  ...
})
```

### directive

`v-if`， `v-bind` 这些都是内置的指令，当然我们可以根据自己需要自定义指令

```js
Vue.directive('tack', {
 bind(el, binding, vnode) {
    el.style.position = 'fixed'
  }
});
```

```html
<p v-tack>I will now be tacked onto the page</p>
```

当然指令也可以接收参数值

`v-example="string"`， 这将使用字符串作为表达式。

```html
<div v-if="stateExample">I will show up 
if stateExample is true</div>
```

```html
<p v-html="'<strong>this is an example of a string
 in some text</strong>'"></p>
```

`v-example:arg="value"`，这允许我们向指令传递一个参数。在下面的示例中，我们将绑定到一个类，并使用一个单独存储的对象对其进行样式设置。

```html
<div v-bind:class="someClassObject"></div>
```

`v-example:arg.modifier="value" `，这允许我们使用修饰符。下面的示例允许我们在单击事件上调用`preventDefault()`。

```html
<button v-on:submit.prevent="onSubmit"></button>
```

传递一个参数

```html
<p v-tack:left="70">I'll now be offset from the left instead of the top</p>
```

```js
Vue.directive('tack', {
  bind(el, binding, vnode) {
    el.style.position = 'fixed';
    const s = (binding.arg == 'left' ? 'left' : 'top');
    el.style[s] = binding.value + 'px';
  }
});
```

传递多个参数

```html
<p v-tack="{ top: '40', left: '100' }">Stick me 40px from the top of the
page and 100px from the left of the page</p>
```

```js
Vue.directive('tack', {
  bind(el, binding, vnode) {
    el.style.position = 'fixed';
    el.style.top = binding.value.top + 'px';
    el.style.left = binding.value.left + 'px';
  }
}); 
```

### plugin

插件通常用来为 Vue 添加全局功能。插件的功能范围没有严格的限制——一般有下面几种：

1. 添加全局方法或者属性。如：[vue-custom-element](https://github.com/karol-f/vue-custom-element)
2. 添加全局资源：指令/过滤器/过渡等。如 [vue-touch](https://github.com/vuejs/vue-touch)
3. 通过全局混入来添加一些组件选项。如 [vue-router](https://github.com/vuejs/vue-router)
4. 添加 Vue 实例方法，通过把它们添加到 `Vue.prototype` 上实现。
5. 一个库，提供自己的 API，同时提供上面提到的一个或多个功能。如 [vue-router](https://github.com/vuejs/vue-router)

使用

```js
// 调用 `MyPlugin.install(Vue)`
Vue.use(MyPlugin, { someOption: true })

new Vue({
  // ...组件选项
})
```

开发

```js
MyPlugin.install = function (Vue, options) {
  // 1. 添加全局方法或属性
  Vue.myGlobalMethod = function () {
    // 逻辑...
  }

  // 2. 添加全局资源
  Vue.directive('my-directive', {
    bind (el, binding, vnode, oldVnode) {
      // 逻辑...
    }
    ...
  })

  // 3. 注入组件选项
  Vue.mixin({
    created: function () {
      // 逻辑...
    }
    ...
  })

  // 4. 添加实例方法
  Vue.prototype.$myMethod = function (methodOptions) {
    // 逻辑...
  }
}
```

## Vue CLI

使用构建工具，可以让我们更方便的编写组件，并且使用`es6` 等语法。

```shell
yarn global add @vue/cli @vue/cli-service-global
vue create project or vue ui
cd project
yarn
vue serve or yarn run dev
```

简单的`.vue` 模板组件

```js
<template>
  <div>
     <!-- Write your HTML with Vue in here -->	
  </div>
</template>

<script>
  export default {
     // Write your Vue component logic here
  }
</script>

<style scoped>
  /* Write your styles for the component in here */
</style>
```

引入组件

```js
<new></new>

import New from './components/New.vue';
export default {
  components: {
    New
  }
}
```

也可以给组件取别名

```js
<app-new></app-new>

import New from './components/New.vue';
export default {
  components: {
    appNew: New
  }
}
```

## Vue Router & NUXT

### Vue Router

官方文档很详细了，这里不多叙述

### NUXT

服务端渲染，不需要定义路由视图，

## Vuex

组件数量如果比较多，组件之间可能不存在父子组件的关系，这时候就要引入状态管理的机制了。

```js
export const store = new Vuex.Store({
  state: {
    counter: 0
  },
  //showing things, not mutating state
  getters: {
    tripleCounter: state => {
      return state.counter * 3;
    }
  },
  //mutating the state
  //mutations are always synchronous
  mutations: {
    //showing passed with payload, represented as num
    increment: (state, num) => {
      state.counter += num;
    }
  }, 
  //commits the mutation, it's asynchronous
  actions: {
    // showing passed with payload, represented as asynchNum (an object)
    asyncIncrement: ({ commit }, asyncNum) => {
      setTimeout(() => {
        //the asyncNum objects could also just be static amounts
        commit('increment', asyncNum.by);
      }, asyncNum.duration);
    },
    async actionA ({ commit }) {
        commit('gotData', await getData())
     },
     async actionB ({ dispatch, commit }) {
        await dispatch('actionA') // 等待 actionA 完成
        commit('gotOtherData', await getOtherData())
     }
  }
});
```

在组件本身上，我们将`使用computed for getters`(这是有意义的，因为这个值已经为我们计算过了)，使用`commit`访问`mutations`的方法，以及使用`dispatch`访问`actions`的方法

```js
computed: {
  value() {
    return this.$store.getters.tripleCounter;
  }
},
methods: {
  increment() {
    this.$store.commit('increment', 2)
  },
  asyncIncrement() {
    this.$store.dispatch('asyncIncrement', 2)
  }
}
```

使用`spred opetrator`

```js
computed: {
  localComputed () { /* ... */ },
  // 使用对象展开运算符将此对象混入到外部对象中
  ...mapState({
    // ...
  })
},
methods: {
    ...mapActions([
      // map this.increment() to this.$store.commit('increment')
      'increment', 
      'decrement',
      'asyncIncrement'
    ])
  }
```

注意，`mutations` 必须是同步函数，`actions` 支持异步操作

多模块

```js
const moduleA = {
  state: { ... },
  mutations: { ... },
  actions: { ... },
  getters: { ... }
}

const moduleB = {
  state: { ... },
  mutations: { ... },
  actions: { ... }
}

const store = new Vuex.Store({
  modules: {
    a: moduleA,
    b: moduleB
  }
})

store.state.a // -> moduleA 的状态
store.state.b // -> moduleB 的状态
```

## Composition API

**VUE3** 引入的新特性，[composition-api-introduction](https://v3.vuejs.org/guide/composition-api-introduction.html)

当然**VUE2**也可以使用，

```shell
npm i @vue/composition-api -S
```

以前`vue2`的时候，组件结构规范，比如`data()` 声明数据变量，`methods` 声明方法，这样的结构当然有看起来当然结构清晰，但是另一方面，会导致数据变量分散开来，因为这个原因，`vue`又引发魔法的`this`的，使得组件里面属性里面的`this`总是指向当前组件。

引入`Composition API`的，相当于就把方法，变量等组件属性抽离出来，形成一个整体的功能函数，按需载入，返回所需，它们不会修改或改变函数范围之外的东西，所以在多次执行时，总是会可靠地接收到相同的值和相同的输入；当然对`typescript`也支持更好，毕竟`vue3`才有`typescript`重写，同时消除了让人困惑的魔法`this`

下面举个简单例子，

`Components`写法

```js
const App = {
    data() {
        return {
            count: 0
        }
    },
    props: { 
      countA: {
        type: Number,
        required: true
      }
    },
    mounted(){
      console.log('component is mounted!')
    }
    methods: {
        increment: function() {
            this.count++;
            console.info(this.count)
        }
    }
}
```

`Composition API` 写法

```js
const { ref, reactive, computed } = Vue
const App = {
   props: { 
      countA: {
        type: Number,
        required: true
      }
    },
    setup(props, context) {
        const count = ref(0)
        const state = reactive({
            count: 0,
            double: computed(() => state.count * 2)
        })
        watch(state, (newValue, oldValue) => {
            console.log(newValue.count)
        })
        watchEffect(() => {
            console.log(state.count)
        })
        onMounted(() => {
          console.log('component is mounted!')
        })
        function increment() {
            count.value++
            state.count++;
        	console.info(count.value)
            console.info(state.count)
        	console.info(props.countA)
        }
        context.emit('eventName')
        return {
             count,
            // state
            ...toRefs(state)
             increment
        }
    }
}
```

**Single File Components** `<script setup>`

```js
<script setup>
  const state = reactive({
    count: 0
  })

  onMounted(() => {
    console.log(state.count)
  })

  return {
    state
  }
</script>
```



## 相关链接

[frontendmaster: introction to vue.js](https://frontendmasters.com/courses/vue/)

[github: sdras intro-to-vue ](https://github.com/sdras/intro-to-vue)

[vue.js org](https://v3.vuejs.org/)

[github:nuxt](https://github.com/nuxt)