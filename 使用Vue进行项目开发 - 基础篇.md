# 使用Vue进行项目开发 - 基础篇
## 目标人群
正在使用或者即将使用Vue进行开发的新同事
## 前言
本教程不深究Vue里面的实现原理，不面面俱到，只讲解项目开发常用到的知识点，方便大家尽快入门。
## 大纲
### 模板语法
- mustache 语法 {{ }} 和 v-text
```html
    <div>
        <span> Message: {{msg}} </span>
        <span> Message: <span v-text="msg"></span></span>
    </div>
```
```js
    var app = new Vue({
        el: '#app',
        data: {
            msg: 'Hello Vue!'
        }
    })
```
mustache 标签将会被替换为 data 对象上对应的 msg 属性的值。只要绑定的数据对象上的 msg 属性发生改变，插值内容也会随之更新。

注意：

1. 在Vue实例创建后，只有data里面已存在的属性才是响应式的。

2. 在 Vue 模板中的 HTML 属性上无法使用双花括号语法(mustache)。

- v-html
```html
    <div v-html="str"></div>
```
```js
    var app = new Vue({
        el: '#app',
        data: {
            str: '<span> Message: Hello Vue! </span>'
        }
    })
```
注意：

在网站中动态渲染任意的 HTML 是非常危险的，因为这很容易导致网站受到 XSS 攻击。请只对可信内容使用 HTML 插值，绝对不要对用户提供的内容使用 HTML 插值。

- v-bind 简写 :
```html
    <div>
        <span v-bind:id="id"> Message: Hello Vue! </span>
        <span :id="id"> Message: Hello Vue! </span>
    </div>
```
```js
    var app = new Vue({
        el: '#app',
        data: {
            id: 'msg'
        }
    })
```
- 使用Js表达式
```html
    <div :id="'list-' + id"></div>

    {{ number + 1 }}

    {{ ok ? 'YES' : 'NO' }}

    {{ message.split('').reverse().join('') }}
```
注意：每个绑定都只能包含单个表达式
```html
    <!-- 这是语句，不是表达式 -->
    {{ var a = 1 }}

    <!-- 流控制也无法运行，请使用三元表达式 -->
    {{ if (ok) { return message } }}
```
- class 和 style 的绑定

```html
    <div class="static" 
        :class="{ active: isActive}">
    </div>
```
```js
    data: {
        isActive: true
    }
```
```html
    <div class="static active"></div> 
```
上述语法意味着：

1. 原生class 和 v-bind:class 最后都会添加到class列表中。

2. active 这个 class 的存在与否，取决于 isActive 这个 data 属性的 truthy 值。

```html
    <div :style="{ color: activeColor, fontSize: fontSize + 'px' }, 'min-height':'100px'"></div>
    <div :style="styleObject"></div>
```
```js
    data: {
        activeColor：'red',
        fontSize: 13,
        styleObject: {
            color: 'red',
            fontSize: '13px'
        }
    }
```
注意：CSS 属性名称可以使用驼峰式(camelCase)或串联式(kebab-case)（使用串联式需要加引号）
### 条件渲染 v-if 和 v-show
- v-if
```html
    <div v-if="type === 'A'">
        A
    </div>
    <div v-else-if="type === 'B'">
        B
    </div>
    <div v-else-if="type === 'C'">
        C
    </div>
    <div v-else>
        Not A/B/C
    </div>
```
```html
    <!-- 当有多个元素同时依赖一个判断条件时候可以使用<template>元素 -->
    <template v-if="type === 'person'">
        <div>male</div>
        <div>female</div>
    </template> 
    <template v-else>
        <div>dog</div>
        <div>cat</div> 
    </template>    
```
- v-show
```html
    <div v-show="type === 'A'">
        A
    </div>
    <div v-else>
        B
    </div>
```
- v-show vs v-if
1. v-if 是真实的条件渲染（是否存在该元素），因为它会确保条件块在切换当中适当地销毁与重建条件块内的事件监听器和子组件。
2. v-show 只是css切换。 dispaly:none <---> display:block。
3. v-show 不支持 <template> 元素

### 列表渲染 v-for
- 循环数组
```html
<ul id="example-1">
<li v-for="(item,index) in items">
    {{index}} - {{ item.message }}
</li>
</ul>
```
- v-show vs v-if
```js
var example1 = new Vue({
  el: '#example-1',
  data: {
    items: [
      { message: 'Foo' },
      { message: 'Bar' }
    ]
  }
})
```
```html
<!-- 渲染结果 -->
0 - Foo
1 - Bar
```
- 循环对象
```html
<ul id="example-2">
<li v-for="(val,key) in obj">
    {{key}} - {{val}}
</li>
</ul>
```
```js
var example1 = new Vue({
  el: '#example-2',
  data: {
    obj: { 
        '姓名': '王楠',
        '工号'：'33383'
    },
  }
})
```
```html
<!-- 渲染结果 -->
姓名 - 王楠
工号 - 33383
```
- 带有 v-if 的 v-for
```html
<!-- 只显示已完成的事项 -->
<li v-for="todo in todos" v-if="!todo.isComplete">
    {{ todo }}
</li>
```
```js
data:{
    todos:[
        {
            name:'买菜',
            isComplete:true
        },
        {
            name:'洗菜',
            isComplete:true
        },
        {
            name:'做饭',
            isComplete:false
        },
    ]
}
```
```html
<!-- 渲染结果 -->
买菜
洗菜
```
上述代码表示：处于同一节点时，v-for 的优先级高于 v-if，v-if 将分别在循环中的每次迭代上运行，在你只想显示某几项时候很有用。

当然你可能需要满足条件时候采取渲染列表，可以通过v-if 放置于包裹元素上来实现
```html
<!-- 存在待办显示待办，不存在给出提示 -->
<ul v-if="todos.length">
    <li v-for="todo in todos">
        {{ todo }}
    </li>
</ul>
<p v-else>没有待办事项了!</p>
```
- 其他应用
```html
<!-- 使用 v-for 在整数值范围内迭代 -->
<div>
    <span v-for="n in 10">{{ n }}</span>
</div>
<!-- 在 <template> 上使用 v-for -->
<ul>
    <template v-for="item in items">
        <li>{{ item.msg }}</li>
        <li class="divider"></li>
    </template>
</ul>
```
### 表单控件 v-model
```html
<!-- v-model 指令在 表单控件 元素上创建双向数据绑定，并会根据控件类型自动选取正确的方法来更新元素 （v-model 绑定的 value 通常是静态字符串，对于勾选框是逻辑值）-->

<input v-model="message" placeholder="edit me">
<textarea v-model="message" placeholder="add multiple lines"></textarea>
<!-- 不指定 value 返回逻辑值 -->
<input type="checkbox" id="checkbox" v-model="checked">
<!-- 指定 value 返回 value 的值 -->
<input type="checkbox" id="checkbox" value="jack" v-model="names">

<!-- 会将输入的首尾空白去掉 -->
<input v-model.trim="message" placeholder="edit me">
```

### 事件 v-on 简写 @
- v-on 指令监听 DOM 事件来触发一些 JavaScript 代码。
```html 
<div id="example-3">
  <!-- `greet` 是在下面定义的方法名 -->
  <button v-on:click="greet">Greet</button>
  <button @click="greet2('TC',$event)">Greet</button>
</div>
```
```js
var example2 = new Vue({
  el: '#example-2',
  data: {
    name: 'Vue.js'
  },
  // 在 `methods` 对象中定义方法
  methods: {
    greet: function (event) {
        alert('Hello ' + this.name + '!')
        if (event) {
            alert(event.target.tagName)
        }
    },
    greet2: function (str,event){
        alert(str);
        if (event) {
            alert(event.target.tagName)
        }
    }
  }
})
```
- 事件修饰符

在事件处理程序中调用 event.preventDefault() 或 event.stopPropagation() 是非常常见的需求。尽管我们可以在 methods 中轻松实现这点，但更好的方式是：methods 只有纯粹的数据逻辑，而不是去处理 DOM 事件细节。

```html
<!-- 停止点击事件冒泡 -->
<a @click.stop="doThis"></a>

<!-- 提交事件不再重新载入页面 -->
<form @submit.prevent="onSubmit"></form>

<!-- 修饰符可以链式调用 -->
<a @click.stop.prevent="doThat"></a>
```
- 按键修饰符

```html
<!-- 按下enter 触发 -->
<input v-on:keyup.enter="search">
    
```
还有许多不常用的[修饰符](https://vuefe.cn/v2/guide/events.html#%E4%BA%8B%E4%BB%B6%E4%BF%AE%E9%A5%B0%E7%AC%A6-Event-Modifiers)，同时也可以[自定义修饰符](https://vuefe.cn/v2/api/#keyCodes)，大家可以查阅Vue文档学习。

### [生命周期](https://vuefe.cn/v2/guide/instance.html#%E7%94%9F%E5%91%BD%E5%91%A8%E6%9C%9F%E7%A4%BA%E6%84%8F%E5%9B%BE)

常用的钩子函数有
- [created](https://vuefe.cn/v2/api/#created)

    此时vue实例已经创建，但是DOM树还没有渲染。你可以通过 this 和 . 运算符来访实例中的数据（data）和 方法（methods）。也可以发起异步请求获取当前页面所需的数据。

- [mounted](https://vuefe.cn/v2/api/#mounted)

    此时 DOM 树已经渲染完毕,你可以获取到DOM元素，进行样式操作。比如元素需要用JS来动态计算高度。

- [actived](https://vuefe.cn/v2/api/#activated)

    当组件是keep-live状态,再次被激活。使用场景：保存列表页的搜索条件，再次返回时候重新获取下数据。

### computed 和 watch
- computed

    用来处理模板中的复杂逻辑，可以直接将其当做一个变量来使用。

```js
    data:{
        hide:false,
        profitTreeList:[]
    },
    computed:{
        filteredPorfitTree(){
            if(this.hide) {
                return this.filterInvalidFn(this.profitTreeList,'profitNo','parentProfit')
            } else {
                return this.profitTreeList
            }
        }
    }
```
filteredPorfitTree 依赖于 data 中的 hide 和 profitTreeList，当这两个值变化时候可以同步更新 filteredPorfitTree 的值。

- watch

用来观察和响应 Vue 实例上的数据变化，添加处理逻辑。

- watch Vs computed
```html
    <div id="demo">{{ fullName }}</div>
```
```js
    var vm = new Vue({
        el: '#demo',
        data: {
            firstName: 'Foo',
            lastName: 'Bar',
            fullName: 'Foo Bar'
        },
        computed: {
            fullName: function () {
                return this.firstName + ' ' + this.lastName
            }
        },
        watch: {
            firstName: function (newVal,oldVal) {
                this.fullName = val + ' ' + this.lastName
            },
            lastName: function (newVal,oldVal) {
                this.fullName = this.firstName + ' ' + val
            }
        }
    })
```
可以看出，当依赖于多个变量时候，使用computed更加优雅，也更简便。但考虑下面这样的情景：
```html
    <p>
        问一个答案是 yes/no 的问题：
        <input v-model="question">
    </p>
    <p>{{ answer }}</p>
```
```js
    data:{
        question:'',
        answer:''
    },
    watch:{
       question(){
           this.getAnswer();
       } 
    },
    method:{
        getAnswer: _.debounce(function () {
            if (this.question.indexOf('？') === -1) {
                this.answer = '问题通常需要包含一个中文问号。;-)'
                return
            }
            this.answer = '思考中……'
            var vm = this
            axios.get('https://yesno.wtf/api')
            .then(function (response) {
                vm.answer = _.capitalize(response.data.answer)
            })
            .catch(function (error) {
                vm.answer = '错误！API 无法处理。' + error
            })
        },500)
    }
```
在这个场景中，使用 watch 选项，可以使我们执行一个限制执行频率的（访问一个 API 的）异步操作，并且不断地设置中间状态，直到我们获取到最终的 answer 数据之后，才真正执行异步操作。而 computed 属性无法实现。

## 作业

- 题目：根据名称搜索用户,并将搜索到结果的明细以table形式展现,没有结果时候给出提示。

- 地址：http://jsonplaceholder.typicode.com/users?q=XX