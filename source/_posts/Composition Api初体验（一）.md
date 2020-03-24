---
title: 'Composition Api初体验'
date: 2020-03-03 11:02:33
abstract: "A set of additive, function-based APIs that allow flexible composition of component logic."
header_image: /posts/2.png
screenshot_image: /posts/2_screenshot.png
comments: true #是否可评论
toc: true #是否显示文章目录
categories:  #分类
    - Vue
tags:   #标签
    - vue-api
---

去年七月，Vue发布[Composition API RFC](https://vue-composition-api-rfc.netlify.com/)

它的出现解决了很多问题，当然也带来了新的问题。

### **组件的「逻辑复用」**
平时开发中，我们都采用组件式开发，因为组件可以复用，节省开发成本。但是项目中不仅仅有UI Component，更多的是带有逻辑的组件，这些组件的逻辑复用一直改变着组件的编写方式。

#### mixins ####
[mixins](https://cn.vuejs.org/v2/api/#mixins)将mixin组件与原组件的选项进行合并，达到逻辑复用的效果。弊端也很明显，组件数据或方法来源不清晰，如果多层嵌套，简直是灾难。而且mixin组件的方法或属性还可能与原组件的命名空间发生冲突，导致方法失效或数据丢失。
#### Renderless Component ####
后来出现了Renderless Component的方案，需要借助[vue.$scopedSlots](https://cn.vuejs.org/v2/api/#vm-scopedSlots)属性
<!-- 看起来和React的HOC（高阶组件）很像，思想都是来源于装饰者模式，只是使用方法有着些许不同。
React由于是all js，把逻辑封装在高阶组件之后，可以直接通过props传给嵌套在其内部的组件。 -->
具体做法是在Renderless Component中把需要复用的逻辑放在*$scopedSlots.default*中，通过render函数渲染，内部的组件就可以通过[v-slot](https://vuejs.org/v2/guide/components-slots.html#Scoped-Slots)获取数据或逻辑。
Renderless Component的编写方式还有很多有趣的用法可以参考以下文章
* [Renderless Components in Vue.js](https://adamwathan.me/renderless-components-in-vuejs/)
* [Renderless Components: 5 Wild Experiments](https://michaelnthiessen.com/renderless-components-5-wild-experiments/)

这种方案通过组合的方式，可以达到很高的灵活程度。缺点也很明显，产生了额外的组件实例消耗。
#### Composition Api ####
终于说到今天的主角。Composition Api的设计初衷之一就是为了更好的实现组件间的逻辑复用。
简单介绍下用法，首先它提供了一个新的api--*Setup* 。在这个入口函数中，可以调用Composition Api提供的一系列新api，用来代替Vue 2x的选项，返回值会被merge到上下文中供模板渲染使用。不难想到，这个函数正是为了可以让我们调用封装好的逻辑。看下面一个例子：
```js
var vue = new Vue({
    setup (props, context) {
        // we can abstract fetch function into useFetchAccount
        const { fetch, data } = useFetchAccount()
        // same as mounted
        onMounted(() => {
            fetch()
        })
        const emitFuc = () => {
            context.emit('Emitted')
        }

        return {
            propA: props.propA,
            emitFuc,
            data
        }
    }
})
```
刚开始用的时候，有一种react hook既视感，思想和用法很相似（还有封装好的hook都喜欢叫use-xxx..）但是也有些许差异，具体可以参考这篇文章。
* [Comparing React Hooks with Vue Composition API](https://dev.to/voluntadpear/comparing-react-hooks-with-vue-composition-api-4b32)

P.S. 组件复用的方式还有HOC（高阶函数），在React中使用比较广泛，由于是all js，把逻辑封装在高阶组件之后，可以直接通过props传给嵌套在其内部的组件。而在Vue中使用不是很多，相关文献也比较少。
&nbsp;&nbsp;

### **「使用感受」**
这份RFC刚出的时候，项目组前端大佬就把composition api引进来，前端部分进行了一次大重构，把以前的Renderless Component全部干掉，使用hook的方式进行复用，虽然工作量很大，但是还是蛮开心可以在工作中用到新鲜出炉的api。另一方面，这个API的引入解决了vue 2.0的 “Options-based API” 对typescript支持不好的问题，所以我们也可以开心的用起TS了。开心归开心，坑还是有的，关于TS在项目中踩到的一些坑后面再总结出来。

