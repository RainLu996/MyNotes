# Vue

## 一、Vue简介

### 1.1 使用Vue的背景

- 使用jQuery的复杂性问题
    - 使用jQuery进行前后端分离开发，既可以实现前后端交互（Ajax），又可以完成数据渲
        染；
    - 存在的问题：jQuery需要通过**HTML标签拼接**、**DOM节点操作**来完成数据的显示，因此开发效
        率低并且容易出错，页面渲染的效率也较低；
    - vue是继jQuery之后的又一优秀的前端框架：专注于前端数据的渲染。其**语法简单**、**渲**
        **染效率高**；



### 1.2 业内流行的前端框架

- 前端三要素：HTML、CSS、JavaScript 
    - HTML决定网页结构
    - CSS决定显示效率
    - JavaScript决定网页功能（交互、数据显示）
- UI框架：
    - Bootstrap 
    - AmazeUI
    - Layui
- JS框架： 
    - jQuery（jQuery UI） 
    - React 
    - angular 
    - node.js：后端开发 
    - vue：集各种前端框架的优势发展而来



### 1.3 什么是MVVM

- `M-数据模型（Model）`：负责数据存储。泛指后端进行的各种业务逻辑处理和数据操控，主要围绕数据库系统展开。主要由JS来构建；
- `V-视图（View）`：负责用户页面的展示。主要由 HTML 和 CSS 来构建；
- `VM-视图模型（View Model）`：负责业务逻辑处理（比如Ajax|Axios请求等），对数据进行加工后交给视图展示，通过Vue类创建的对象叫Vue实例化对象，这个对象就是MVVM模式中的VM层，模型通过它可以将数据绑定到页面上，视图可以通过它将数据映射到模型上。

> ​		将结构布局（UI）与业务逻辑分开，通过viewmodel在结构布局和业务逻辑之间进行通信。



- 优势
    - 低耦合
    - 可重用性高
    - 分层开发，便于维护



## 二、Vue基本使用

### Vue常用7个属性

- `el属性`：用来指示Vue编译器从什么地方开始解析vue的语法，可以说是一个占位符。
- `data属性`：用来组织从view中抽象出来的属性，可以说将视图的数据抽象出来存放在data中。
- `template属性`：用来设置模板。会替换页面元素，包括占位符。
- `methods属性`：放置页面中的业务逻辑，js方法一般都放置在methods中。
- `render属性`：创建真正的Virtual Dom。
- `computed属性`：用来计算属性。
- `watch属性`：
    - watch:function(new,old){}
    - 监听data中数据的变化
    - 两个参数。一个返回新值，一个返回旧值





### 钩子函数



### 缩写
​		`v-` 前缀作为一种视觉提示，用来识别模板中 Vue 特定的特性。当你在使用 Vue.js 为现有标签添加动态行为 (dynamic behavior) 时，`v-` 前缀很有帮助，然而，对于一些频繁用到的指令来说，就会感到使用繁琐。同时，在构建由 Vue.js 管理所有模板的[单页面应用程序 (SPA - single page application)](https://link.zhihu.com/?target=https%3A//en.wikipedia.org/wiki/Single-page_application) 时，`v-` 前缀也变得没那么重要了。因此，Vue.js 为 `v-bind` 和 `v-on` 这两个最常用的指令，提供了特定简写：

- `v-bind` 缩写

```vue
<!-- 完整语法 -->
<a v-bind:href="url"></a>
<!-- 缩写 -->
<a :href="url"></a>
```



- `v-on` 缩写

```vue
<!-- 完整语法 -->
<a v-on:click="doSomething"></a>
<!-- 缩写 -->
<a @click="doSomething"></a>
```







## Vue面试

[(85条消息) vue面试题整理(2022-持续更新中...)_老古懂的博客-CSDN博客](https://blog.csdn.net/qq_45659769/article/details/119564784)

### 为什么说Vue是一个渐进式的JavaScript框架, 渐进式是什么意思？
​		VUE允许你将一个网页分割成可复用的组件，每个组件都包含属于自己的HTML、CSS、JavaScript以用来渲染网页中相应的地方。对于VUE的使用可大可小，它都会有相应的方式来整合到你的项目中，所以说它是一个渐进式的框架。VUE是响应式的（reactive）这是VUE最独特的特性，也就是说当我们的**数据发生变更**时，VUE会帮你更新所有网页中用到它的地方。



### vue生命周期

- beforeCreate（创建前）：组件实例被创建之初，组件的属性生效之前
    //beforeCreate生命周期执行的时候，data和methods中的数据都还没有初始化。不能在这个阶段使用data中的数据和methods中的方法
    created（创建后） ：组件实例已经完全创建，属性也绑定，但真实 dom 还没有生成，$el 还不可用
    // data 和 methods都已经被初始化好了，如果要调用 methods 中的方法，或者操作 data 中的数据，最早可以在这个阶段中操作
    beforeMount（挂载前） ：在挂载开始之前被调用：相关的 render 函数首次被调用
    //执行到这个钩子的时候，在内存中已经编译好了模板了，但是还没有挂载到页面中，此时，页面还是旧的
    mounted（挂载后） ：在el 被新创建的 vm.$el 替换，并挂载到实例上去之后调用该钩子
    //到mounted周期的时候，Vue实例已经初始化完成了。此时组件脱离了创建阶段，进入到了运行阶段。 如果我们想要通过插件操作页面上的DOM节点，最早可以在和这个阶段中进行
    beforeUpdate（更新前） ：组件数据更新之前调用，真实DOM还没被渲染
    // 当执行这个钩子时，页面中的显示的数据还是旧的，data中的数据是更新后的，页面还没有和最新的数据保持同步
    update（更新后） ：组件数据更新之后
    //页面显示的数据和data中的数据已经保持同步了，都是最新的
    activated（激活前） ：keep-alive专属，组件被激活时调用
    //当组件被切回来时，再去缓存里找这个组件、触发 activated钩子函数。
    deactivated（激活后） ：keep-alive专属，组件被销毁时调用
    //当组件被换掉时，会被缓存到内存中、触发 deactivated 生命周期
    beforeDestory（销毁前） ：组件销毁前调用
    //Vue实例从运行阶段进入到了销毁阶段，这个时候上所有的 data 和 methods ， 指令， 过滤器 ……都是处于可用状态。还没有真正被销毁
    destoryed（销毁后） ：组件销毁前调用
    //这个时候上所有的 data 和 methods ， 指令， 过滤器 ……都是处于不可用状态。组件已经被销毁了。







## Vue扩展

### [vue中data和data()的区别](https://www.jb51.net/article/239474.htm)

### [computed计算属性和watch区别以及各自的使用](https://blog.csdn.net/qq_38110274/article/details/121242203)









