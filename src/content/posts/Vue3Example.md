---
title: Vue.js的v-bind示例
published: 2025-10-21
description: ''
image: ''
tags: []
category: ''
draft: false 
lang: ''
---
下面是一个实用的Vue.js `v-bind` 示例，展示了如何动态绑定图片、链接、样式和类。这个例子涵盖了日常开发中的常见场景，便于你快速上手。

### 🎯 示例：动态用户卡片

假设我们有一个用户信息卡片，需要动态显示头像、个人链接、样式和状态。

```
<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <title>Vue v-bind 示例</title>
    <script src="https://unpkg.com/vue@3/dist/vue.global.js"></script>
    <style>
        .active { border: 2px solid #4CAF50; }
        .inactive { border: 2px solid #f44336; }
        .vip { background-color: #fffacd; }
        .card { padding: 15px; margin: 10px; border-radius: 5px; }
    </style>
</head>
<body>
    <div id="app">
        <!-- 使用对象一次性绑定多个属性 -->
        <div v-bind="userCardAttributes">
            <img :src="avatarSrc" :alt="userName + '的头像'"> <!-- 绑定图片地址和替代文本 -->
            <h3 :style="{ color: titleColor }">{{ userName }}</h3> <!-- 绑定内联样式 -->
            <p>状态:
               <span :class="{ 'active': isActive, 'inactive': !isActive }">
                 {{ isActive ? '在线' : '离线' }}
               </span> <!-- 根据条件绑定类 -->
            </p>
            <a :href="profileLink" :title="'访问' + userName + '的主页'">查看主页</a> <!-- 绑定链接和提示 -->
            <button @click="toggleStatus">切换状态</button>
        </div>
    </div>

    <script>
        const { createApp } = Vue;
        createApp({
            data() {
                return {
                    avatarSrc: 'https://example.com/avatar.jpg',
                    userName: '张三',
                    titleColor: '#2c3e50',
                    isActive: true,
                    profileLink: 'https://example.com/profile/zhangsan',
                    isVip: true
                }
            },
            computed: {
                // 计算属性用于动态绑定多个属性
                userCardAttributes() {
                    return {
                        id: 'user-card-001',
                        class: {
                            'card': true,
                            'vip': this.isVip // VIP用户有特殊背景
                        }
                    };
                }
            },
            methods: {
                toggleStatus() {
                    this.isActive = !this.isActive;
                }
            }
        }).mount('#app');
    </script>
</body>
</html>
```

### 💡 代码要点解析

这个例子演示了 `v-bind` (或其简写 `:`) 的几种常见用法：

1. **绑定基本属性**
    - `:src="avatarSrc"`：将图片的 `src` 属性绑定到数据中的 `avatarSrc`。
    - `:href="profileLink"`：将链接的 `href` 属性绑定到数据中的 `profileLink`。
2. **动态绑定CSS类 (`:class`)**
    - 对象语法：`:class="{ 'active': isActive, 'inactive': !isActive }"`。这里，类 `active` 是否存在取决于数据 `isActive` 的真假值。当 `isActive` 为 `true` 时，元素会拥有 `active` 类。
    - 在计算属性 `userCardAttributes` 中，也使用了对象语法来绑定多个类。
3. **动态绑定内联样式 (`:style`)**
    - 对象语法：`:style="{ color: titleColor }"`。将样式属性绑定到数据。注意，CSS属性名可以用驼峰式（如 `fontSize`）或短横线分隔（需加引号，如 `'font-size'`）。
4. **一次性绑定多个属性 (`v-bind="对象"`)**
    - 通过 `v-bind="userCardAttributes"` 一次性绑定了 `id` 和 `class` 属性。这个计算属性返回一个对象，对象的键是属性名，值是对应的数据。
5. **在属性中使用表达式**
    - `:alt="userName + '的头像'"`：在绑定的属性中可以使用简单的JavaScript表达式。

### ⚠️ 使用注意

- **简写形式**：`v-bind:` 可以简写为一个冒号 `:`。例如，`v-bind:src` 等价于 `:src`。
- **属性值为 `null` 或 `undefined`*：如果绑定的值为 `null` 或 `undefined`，则该属性不会被渲染在最终的HTML元素上。
- **布尔属性**：对于如 `disabled`、`checked` 等布尔属性，当绑定的值为[真值](https://developer.mozilla.org/zh-CN/docs/Glossary/Truthy)时，属性会出现；为[假值](https://developer.mozilla.org/zh-CN/docs/Glossary/Falsy)时，属性会被移除。例如 `<button :disabled="isLoading">提交</button>`，当 `isLoading` 为 `true` 时，按钮会被禁用。

# 内置指令 {#built-in-directives}

## v-text {#v-text}

更新元素的文本内容。

- **期望的绑定值类型：**`string`
- **详细信息**
    
    `v-text` 通过设置元素的 [textContent](https://developer.mozilla.org/en-US/docs/Web/API/Node/textContent) 属性来工作，因此它将覆盖元素中所有现有的内容。如果你需要更新 `textContent` 的部分，应该使用 [mustache interpolations](https://www.notion.so/guide/essentials/template-syntax#text-interpolation) 代替。
    
- **示例**
    
    ```
    <span v-text="msg"></span>
    <!-- 等同于 -->
    <span>{{msg}}</span>
    
    ```
    
- **参考**[模板语法 - 文本插值](https://www.notion.so/guide/essentials/template-syntax#text-interpolation)

## v-html {#v-html}

更新元素的 [innerHTML](https://developer.mozilla.org/en-US/docs/Web/API/Element/innerHTML)。

- **期望的绑定值类型：**`string`
- **详细信息**

`v-html` 的内容直接作为普通 HTML 插入—— Vue 模板语法是不会被解析的。如果你发现自己正打算用 `v-html` 来编写模板，不如重新想想怎么使用组件来代替。

::: warning 安全说明
在你的站点上动态渲染任意的 HTML 是非常危险的，因为它很容易导致 [XSS 攻击](https://en.wikipedia.org/wiki/Cross-site_scripting)。请只对可信内容使用 HTML 插值，**绝不要**将用户提供的内容作为插值
:::

在[单文件组件](https://www.notion.so/guide/scaling-up/sfc)，`scoped` 样式将不会作用于 `v-html` 里的内容，因为 HTML 内容不会被 Vue 的模板编译器解析。如果你想让 `v-html` 的内容也支持 scoped CSS，你可以使用 [CSS modules](https://www.notion.so/sfc-css-features#css-modules) 或使用一个额外的全局 `<style>` 元素，手动设置类似 BEM 的作用域策略。

- **示例**
    
    ```
    <div v-html="html"></div>
    
    ```
    
- **参考**[模板语法 - 原始 HTML](https://www.notion.so/guide/essentials/template-syntax#raw-html)

## v-show {#v-show}

基于表达式值的真假性，来改变元素的可见性。

- **期望的绑定值类型：**`any`
- **详细信息**
    
    `v-show` 通过设置内联样式的 `display` CSS 属性来工作，当元素可见时将使用初始 `display` 值。当条件改变时，也会触发过渡效果。
    
- **参考**[条件渲染 - v-show](https://www.notion.so/guide/essentials/conditional#v-show)

## v-if {#v-if}

基于表达式值的真假性，来条件性地渲染元素或者模板片段。

- **期望的绑定值类型：**`any`
- **详细信息**
    
    当 `v-if` 元素被触发，元素及其所包含的指令/组件都会销毁和重构。如果初始条件是假，那么其内部的内容根本都不会被渲染。
    
    可用于 `<template>` 表示仅包含文本或多个元素的条件块。
    
    当条件改变时会触发过渡效果。
    
    当同时使用时，`v-if` 比 `v-for` 优先级更高。我们并不推荐在一元素上同时使用这两个指令 — 查看[列表渲染指南](https://www.notion.so/guide/essentials/list#v-for-with-v-if)详情。
    
- **参考**[条件渲染 - v-if](https://www.notion.so/guide/essentials/conditional#v-if)

## v-else {#v-else}

表示 `v-if` 或 `v-if` / `v-else-if` 链式调用的“else 块”。

- **无需传入表达式**
- **详细信息**
    - 限定：上一个兄弟元素必须有 `v-if` 或 `v-else-if`。
    - 可用于 `<template>` 表示仅包含文本或多个元素的条件块。
- **示例**
    
    ```
    <div v-if="Math.random() > 0.5">
      Now you see me
    </div>
    <div v-else>
      Now you don't
    </div>
    
    ```
    
- **参考**[条件渲染 - v-else](https://www.notion.so/guide/essentials/conditional#v-else)

## v-else-if {#v-else-if}

表示 `v-if` 的“else if 块”。可以进行链式调用。

- **期望的绑定值类型：**`any`
- **详细信息**
    - 限定：上一个兄弟元素必须有 `v-if` 或 `v-else-if`。
    - 可用于 `<template>` 表示仅包含文本或多个元素的条件块。
- **示例**
    
    ```
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
    
- **参考**[条件渲染 - v-else-if](https://www.notion.so/guide/essentials/conditional#v-else-if)

## v-for {#v-for}

基于原始数据多次渲染元素或模板块。

- **期望的绑定值类型：**`Array | Object | number | string | Iterable`
- **详细信息**
    
    指令值必须使用特殊语法 `alias in expression` 为正在迭代的元素提供一个别名：
    
    ```
    <div v-for="item in items">
      {{ item.text }}
    </div>
    
    ```
    
    或者，你也可以为索引指定别名 (如果用在对象，则是键值)：
    
    ```
    <div v-for="(item, index) in items"></div>
    <div v-for="(value, key) in object"></div>
    <div v-for="(value, name, index) in object"></div>
    
    ```
    
    `v-for` 的默认方式是尝试就地更新元素而不移动它们。要强制其重新排序元素，你需要用特殊 attribute `key` 来提供一个排序提示：
    
    ```
    <div v-for="item in items" :key="item.id">
      {{ item.text }}
    </div>
    
    ```
    
    `v-for` 也可以用于 [Iterable Protocol](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Iteration_protocols#The_iterable_protocol) 的实现，包括原生 `Map` 和 `Set`。
    
- **参考**
    - [列表渲染](https://www.notion.so/guide/essentials/list)

## v-on {#v-on}

给元素绑定事件监听器。

- **缩写：**`@`
- **期望的绑定值类型：**`Function | Inline Statement | Object (不带参数)`
- **参数：**`event` (使用对象语法则为可选项)
- **修饰符**
    - `.stop` - 调用 `event.stopPropagation()`。
    - `.prevent` - 调用 `event.preventDefault()`。
    - `.capture` - 在捕获模式添加事件监听器。
    - `.self` - 只有事件从元素本身发出才触发处理函数。
    - `.{keyAlias}` - 只在某些按键下触发处理函数。
    - `.once` - 最多触发一次处理函数。
    - `.left` - 只在鼠标左键事件触发处理函数。
    - `.right` - 只在鼠标右键事件触发处理函数。
    - `.middle` - 只在鼠标中键事件触发处理函数。
    - `.passive` - 通过 `{ passive: true }` 附加一个 DOM 事件。
- **详细信息**
    
    事件类型由参数来指定。表达式可以是一个方法名，一个内联声明，如果有修饰符则可省略。
    
    当用于普通元素，只监听[**原生 DOM 事件**](https://developer.mozilla.org/en-US/docs/Web/Events)。当用于自定义元素组件，则监听子组件触发的**自定义事件**。
    
    当监听原生 DOM 事件时，方法接收原生事件作为唯一参数。如果使用内联声明，声明可以访问一个特殊的 `$event` 变量：`v-on:click="handle('ok', $event)"`。
    
    `v-on` 还支持绑定不带参数的事件/监听器对的对象。请注意，当使用对象语法时，不支持任何修饰符。
    
- **示例**
    
    ```
    <!-- 方法处理函数 -->
    <button v-on:click="doThis"></button>
    
    <!-- 动态事件 -->
    <button v-on:[event]="doThis"></button>
    
    <!-- 内联声明 -->
    <button v-on:click="doThat('hello', $event)"></button>
    
    <!-- 缩写 -->
    <button @click="doThis"></button>
    
    <!-- 使用缩写的动态事件 -->
    <button @[event]="doThis"></button>
    
    <!-- 停止传播 -->
    <button @click.stop="doThis"></button>
    
    <!-- 阻止默认事件 -->
    <button @click.prevent="doThis"></button>
    
    <!-- 不带表达式地阻止默认事件 -->
    <form @submit.prevent></form>
    
    <!-- 链式调用修饰符 -->
    <button @click.stop.prevent="doThis"></button>
    
    <!-- 按键用于 keyAlias 修饰符-->
    <input @keyup.enter="onEnter" />
    
    <!-- 点击事件将最多触发一次 -->
    <button v-on:click.once="doThis"></button>
    
    <!-- 对象语法 -->
    <button v-on="{ mousedown: doThis, mouseup: doThat }"></button>
    
    ```
    
    监听子组件的自定义事件 (当子组件的“my-event”事件被触发，处理函数将被调用)：
    
    ```
    <MyComponent @my-event="handleThis" />
    
    <!-- 内联声明 -->
    <MyComponent @my-event="handleThis(123, $event)" />
    
    ```
    
- **参考**
    - [事件处理](https://www.notion.so/guide/essentials/event-handling)
    - [组件 - 自定义事件](https://www.notion.so/guide/essentials/component-basics#listening-to-events)

## v-bind {#v-bind}

动态的绑定一个或多个 attribute，也可以是组件的 prop。

- **缩写：**
    - `:` 或者 `.` (当使用 `.prop` 修饰符)
    - 值可以省略 (当 attribute 和绑定的值同名时，需要 3.4+ 版本)
- **期望：**`any (带参数) | Object (不带参数)`
- **参数：**`attrOrProp (可选的)`
- **修饰符**
    - `.camel` - 将短横线命名的 attribute 转变为驼峰式命名。
    - `.prop` - 强制绑定为 DOM property (3.2+)。
    - `.attr` - 强制绑定为 DOM attribute (3.2+)。
- **用途**
    
    当用于绑定 `class` 或 `style` attribute，`v-bind` 支持额外的值类型如数组或对象。详见下方的指南链接。
    
    在处理绑定时，Vue 默认会利用 `in` 操作符来检查该元素上是否定义了和绑定的 key 同名的 DOM property。如果存在同名的 property，则 Vue 会将它作为 DOM property 赋值，而不是作为 attribute 设置。这个行为在大多数情况都符合期望的绑定值类型，但是你也可以显式用 `.prop` 和 `.attr` 修饰符来强制绑定方式。有时这是必要的，特别是在和[自定义元素](https://www.notion.so/guide/extras/web-components#passing-dom-properties)打交道时。
    
    当用于组件 props 绑定时，所绑定的 props 必须在子组件中已被正确声明。
    
    当不带参数使用时，可以用于绑定一个包含了多个 attribute 名称-绑定值对的对象。
    
- **示例**
    
    ```
    <!-- 绑定 attribute -->
    <img v-bind:src="imageSrc" />
    
    <!-- 动态 attribute 名 -->
    <button v-bind:[key]="value"></button>
    
    <!-- 缩写 -->
    <img :src="imageSrc" />
    
    <!-- 缩写形式的动态 attribute 名 (3.4+)，扩展为 :src="src" -->
    <img :src />
    
    <!-- 动态 attribute 名的缩写 -->
    <button :[key]="value"></button>
    
    <!-- 内联字符串拼接 -->
    <img :src="'/path/to/images/' + fileName" />
    
    <!-- class 绑定 -->
    <div :class="{ red: isRed }"></div>
    <div :class="[classA, classB]"></div>
    <div :class="[classA, { classB: isB, classC: isC }]"></div>
    
    <!-- style 绑定 -->
    <div :style="{ fontSize: size + 'px' }"></div>
    <div :style="[styleObjectA, styleObjectB]"></div>
    
    <!-- 绑定对象形式的 attribute -->
    <div v-bind="{ id: someProp, 'other-attr': otherProp }"></div>
    
    <!-- prop 绑定。“prop” 必须在子组件中已声明。 -->
    <MyComponent :prop="someThing" />
    
    <!-- 传递子父组件共有的 prop -->
    <MyComponent v-bind="$props" />
    
    <!-- XLink -->
    <svg><a :xlink:special="foo"></a></svg>
    
    ```
    
    `.prop` 修饰符也有专门的缩写，`.`：
    
    ```
    <div :someProperty.prop="someObject"></div>
    
    <!-- 等同于 -->
    <div .someProperty="someObject"></div>
    
    ```
    
    当在 DOM 内模板使用 `.camel` 修饰符，可以驼峰化 `v-bind` attribute 的名称，例如 SVG `viewBox` attribute：
    
    ```
    <svg :view-box.camel="viewBox"></svg>
    
    ```
    
    如果使用字符串模板或使用构建步骤预编译模板，则不需要 `.camel`。
    
- **参考**
    - [Class 与 Style 绑定](https://www.notion.so/guide/essentials/class-and-style)
    - [组件 - Prop 传递细节](https://www.notion.so/guide/components/props#prop-passing-details)

## v-model {#v-model}

在表单输入元素或组件上创建双向绑定。

- **期望的绑定值类型**：根据表单输入元素或组件输出的值而变化
- **仅限：**
    - `<input>`
    - `<select>`
    - `<textarea>`
    - components
- **修饰符**
    - [`.lazy`](https://www.notion.so/guide/essentials/forms#lazy) - 监听 `change` 事件而不是 `input`
    - [`.number`](https://www.notion.so/guide/essentials/forms#number) - 将输入的合法字符串转为数字
    - [`.trim`](https://www.notion.so/guide/essentials/forms#trim) - 移除输入内容两端空格
- **参考**
    - [表单输入绑定](https://www.notion.so/guide/essentials/forms)
    - [组件事件 - 配合 `v-model` 使用](https://www.notion.so/guide/components/v-model)

## v-slot {#v-slot}

用于声明具名插槽或是期望接收 props 的作用域插槽。

- **缩写：**`#`
- **期望的绑定值类型**：能够合法在函数参数位置使用的 JavaScript 表达式。支持解构语法。绑定值是可选的——只有在给作用域插槽传递 props 才需要。
- **参数**：插槽名 (可选，默认是 `default`)
- **仅限：**
    - `<template>`
    - [components](https://www.notion.so/guide/components/slots#scoped-slots) (用于带有 prop 的单个默认插槽)
- **示例**
    
    ```
    <!-- 具名插槽 -->
    <BaseLayout>
      <template v-slot:header>
        Header content
      </template>
    
      <template v-slot:default>
        Default slot content
      </template>
    
      <template v-slot:footer>
        Footer content
      </template>
    </BaseLayout>
    
    <!-- 接收 prop 的具名插槽 -->
    <InfiniteScroll>
      <template v-slot:item="slotProps">
        <div class="item">
          {{ slotProps.item.text }}
        </div>
      </template>
    </InfiniteScroll>
    
    <!-- 接收 prop 的默认插槽，并解构 -->
    <Mouse v-slot="{ x, y }">
      Mouse position: {{ x }}, {{ y }}
    </Mouse>
    
    ```
    
- **参考**
    - [组件 - 插槽](https://www.notion.so/guide/components/slots)

## v-pre {#v-pre}

跳过该元素及其所有子元素的编译。

- **无需传入**
- **详细信息**
    
    元素内具有 `v-pre`，所有 Vue 模板语法都会被保留并按原样渲染。最常见的用例就是显示原始双大括号标签及内容。
    
- **示例**
    
    ```
    <span v-pre>{{ this will not be compiled }}</span>
    
    ```
    

## v-once {#v-once}

仅渲染元素和组件一次，并跳过之后的更新。

- **无需传入**
- **详细信息**
    
    在随后的重新渲染，元素/组件及其所有子项将被当作静态内容并跳过渲染。这可以用来优化更新时的性能。
    
    ```
    <!-- 单个元素 -->
    <span v-once>This will never change: {{msg}}</span>
    <!-- 带有子元素的元素 -->
    <div v-once>
      <h1>Comment</h1>
      <p>{{msg}}</p>
    </div>
    <!-- 组件 -->
    <MyComponent v-once :comment="msg" />
    <!-- `v-for` 指令 -->
    <ul>
      <li v-for="i in list" v-once>{{i}}</li>
    </ul>
    
    ```
    
    从 3.2 起，你也可以搭配 [`v-memo`](https://www.notion.so/Vue3-293c1bfb223c80e7ae3be283bd56bfdf?pvs=21) 的无效条件来缓存部分模板。
    
- **参考**
    - [数据绑定语法 - 插值](https://www.notion.so/guide/essentials/template-syntax#text-interpolation)
    - [v-memo](https://www.notion.so/Vue3-293c1bfb223c80e7ae3be283bd56bfdf?pvs=21)

## v-memo {#v-memo}

- 仅在 3.2+ 中支持
- **期望的绑定值类型：**`any[]`
- **详细信息**
    
    缓存一个模板的子树。在元素和组件上都可以使用。为了实现缓存，该指令需要传入一个固定长度的依赖值数组进行比较。如果数组里的每个值都与最后一次的渲染相同，那么整个子树的更新将被跳过。举例来说：
    
    ```
    <div v-memo="[valueA, valueB]">
      ...
    </div>
    
    ```
    
    当组件重新渲染，如果 `valueA` 和 `valueB` 都保持不变，这个 `<div>` 及其子项的所有更新都将被跳过。实际上，甚至虚拟 DOM 的 vnode 创建也将被跳过，因为缓存的子树副本可以被重新使用。
    
    正确指定缓存数组很重要，否则应该生效的更新可能被跳过。`v-memo` 传入空依赖数组 (`v-memo="[]"`) 将与 `v-once` 效果相同。
    
    **与 `v-for` 一起使用**
    
    `v-memo` 仅用于性能至上场景中的微小优化，应该很少需要。最常见的情况可能是有助于渲染海量 `v-for` 列表 (长度超过 1000 的情况)：
    
    ```
    <div v-for="item in list" :key="item.id" v-memo="[item.id === selected]">
      <p>ID: {{ item.id }} - selected: {{ item.id === selected }}</p>
      <p>...more child nodes</p>
    </div>
    
    ```
    
    当组件的 `selected` 状态改变，默认会重新创建大量的 vnode，尽管绝大部分都跟之前是一模一样的。`v-memo` 用在这里本质上是在说“只有当该项的被选中状态改变时才需要更新”。这使得每个选中状态没有变的项能完全重用之前的 vnode 并跳过差异比较。注意这里 memo 依赖数组中并不需要包含 `item.id`，因为 Vue 也会根据 item 的 `:key` 进行判断。
    
    :::warning 警告
    当搭配 `v-for` 使用 `v-memo`，确保两者都绑定在同一个元素上。**`v-memo` 不能用在 `v-for` 内部。**
    :::
    
    `v-memo` 也能被用于在一些默认优化失败的边际情况下，手动避免子组件出现不需要的更新。但是一样的，开发者需要负责指定正确的依赖数组以免跳过必要的更新。
    
- **参考**
    - [v-once](https://www.notion.so/Vue3-293c1bfb223c80e7ae3be283bd56bfdf?pvs=21)

## v-cloak {#v-cloak}

用于隐藏尚未完成编译的 DOM 模板。

- **无需传入**
- **详细信息**
    
    **该指令只在没有构建步骤的环境下需要使用。**
    
    当使用直接在 DOM 中书写的模板时，可能会出现一种叫做“未编译模板闪现”的情况：用户可能先看到的是还没编译完成的双大括号标签，直到挂载的组件将它们替换为实际渲染的内容。
    
    `v-cloak` 会保留在所绑定的元素上，直到相关组件实例被挂载后才移除。配合像 `[v-cloak] { display: none }` 这样的 CSS 规则，它可以在组件编译完毕前隐藏原始模板。
    
- **示例**
    
    ```css
    [v-cloak] {
      display: none;
    }
    
    ```
    
    ```
    <div v-cloak>
      {{ message }}
    </div>
    
    ```
    
    直到编译完成前，`<div>` 将不可见。
    

# 一个错误示例

```
let password = ref('123456')
function addMima() {
		password= ref('2000') // 错误示例，不能直接赋值
}
<div>
  请输入账号:<input v-model="account" type="text">{{ account }}
</div>
<br>

<div>
  请输入密码:<input v-model="password" type="password">{{ password }}
```

这个时候发现，点击按钮之后未发生任何改变，是因为input中绑定的是初始的let password = ref('123456')

总结不要直接操作ref对象，而是要操作ref的value。

又比如

对于一个多变量结构体，有

```html
let account = ref('CloudTide')
let password = ref('123456')
let position = ref(['前端开发'])
let newPosition = ref('')
let If_showUserInfo = ref(false)
let Information = {
  account: account.value,
  password: password.value,
  position:

function changeImformation() {
  Information.account = 'sadasd'
  Information.password = 'asdasd'
  Information.position = ['dsadasd']
}

  <div> {{Information.account}}  {{Information.password}}   {{Information.position}}</div>
  <button v-on:click="changeImformation">Change Imformation</button>
<button v-on:click="addMima">Submit</button>
```

此时点击Changeinformation按钮，信息不改变，但是将

```
let Information = reactive({
  account: account.value,
  password: password.value,
  position: position.value,
})
```

则可以实现绑定（或者改成ref也行）

```
let Information = reactive({
  account: account.value,
  password: password.value,
  position: position.value,
})
function changeImformation() {
  account=toRef(Information,'account')
  password=toRef(Information,'password')
  position=toRef(Information,'position')
}
```

[一个错误示例](https://www.notion.so/293c1bfb223c800baf2ef179305b5200?pvs=21)