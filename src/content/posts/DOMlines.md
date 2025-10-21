---
title: DOM基础操作实例详解
published: 2025-10-21
description: ''
image: ''
tags: []
category: ''
draft: false 
lang: ''
---
DOM 操作是前端开发的核心，下面我将通过详细的实例为你系统梳理基础的 DOM 操作方法。为了让你快速建立起知识框架，我先用一个表格来汇总主要的操作类型、核心方法和关键要点。

| 操作类别 | 核心方法/属性举例 | 关键点/注意事项 |
| :--- | :--- | :--- |
| **元素获取** | `getElementById`, `querySelector`, `querySelectorAll` | `querySelectorAll` 返回静态 NodeList；`getElementsByClassName` 返回动态 HTMLCollection。 |
| **内容操作** | `textContent`, `innerHTML` | `innerHTML` 会解析 HTML 标签，但需注意 XSS 风险；`textContent` 性能更好且更安全。 |
| **属性操作** | `setAttribute`, `getAttribute`, `dataset` | `data-*` 自定义属性推荐使用 `dataset` API 进行操作。 |
| **样式操作** | `style`, `classList` | `classList` 提供 `add()`, `remove()`, `toggle()` 等方法，更利于管理多个类。 |
| **节点操作** | `createElement`, `appendChild`, `insertBefore`, `remove`, `replaceChild` | 大量操作时，使用 `DocumentFragment` 可提升性能。 |
| **事件处理** | `addEventListener`, `removeEventListener` | 事件委托利用事件冒泡，可高效管理动态元素事件。 |
| **节点遍历** | `parentNode`, `children`, `previousElementSibling` | `children` 只包含元素节点，而 `childNodes` 包含所有类型节点（如文本节点）。 |

接下来，我们深入看看每个类别下的详细代码实例和最佳实践。

### 💡 基础操作实例详解

#### 1. 获取 DOM 元素
获取元素是一切操作的前提。现代开发中，推荐使用 `querySelector` 和 `querySelectorAll`，因为它们支持强大的 CSS 选择器。

```javascript
// 获取单个元素
const header = document.getElementById('header'); // 传统方式，通过ID
const firstItem = document.querySelector('.item'); // 现代方式，获取第一个匹配的.item元素

// 获取元素集合
const itemsByClass = document.getElementsByClassName('item'); // 返回动态HTMLCollection
const itemsByTag = document.getElementsByTagName('li'); // 返回动态HTMLCollection
const allItems = document.querySelectorAll('.item'); // 返回静态NodeList

// 遍历NodeList，可以使用forEach方法
allItems.forEach(item => {
  console.log(item.textContent);
});
```

**重要区别**：`getElementsByClassName` 和 `getElementsByTagName` 返回的是 **动态集合**。如果 DOM 树发生变化，这个集合会自动更新。而 `querySelectorAll` 返回的是 **静态集合**，它像是在调用的一瞬间给 DOM 拍了个快照，后续的 DOM 变化不会反映在这个集合中。

#### 2. 操作元素内容与属性
动态改变元素的内容和属性是实现交互的基础。

```javascript
const contentDiv = document.querySelector('.content');

// 文本内容：使用 textContent（安全，不会解析HTML标签）
contentDiv.textContent = '<strong>安全文本</strong>'; // 页面上会直接显示这段字符串
console.log(contentDiv.textContent); // 获取所有文本内容，包括隐藏元素的

// HTML内容：使用 innerHTML（会解析HTML标签，注意XSS风险）
contentDiv.innerHTML = '<strong>加粗的文本</strong>'; // 页面会显示加粗的“加粗的文本”
// 使用innerHTML插入复杂结构
const user = { name: '张三' };
contentDiv.innerHTML = `<div class="welcome">你好，${user.name}!</div>`; // 使用模板字符串

// 属性操作
const link = document.querySelector('a');
// 标准属性可以直接读写
link.href = 'https://www.example.com';
// 自定义属性，推荐使用 data-* 和 dataset
link.setAttribute('data-user-id', '123'); // 设置自定义属性
console.log(link.dataset.userId); // 通过dataset读取，使用驼峰命名
// 通用属性方法
link.setAttribute('title', '提示文本'); // 设置
console.log(link.getAttribute('title')); // 获取
link.removeAttribute('title'); // 删除
```

#### 3. 操作元素样式与类
通过 JS 动态改变元素样式是常见需求。

```javascript
const box = document.querySelector('.box');

// 1. 直接操作style属性（行内样式）
box.style.color = 'blue';
box.style.backgroundColor = '#f0f0f0'; // 注意驼峰命名
box.style.marginTop = '10px';

// 2. 通过操作class类名（推荐，将样式写在CSS中，通过JS控制类）
box.className = 'new-class'; // 会覆盖原有类名
box.classList.add('active', 'highlight'); // 添加类，不会影响其他类
box.classList.remove('old-class'); // 移除类
box.classList.toggle('hidden'); // 切换类：有则删除，无则添加
if (box.classList.contains('active')) { // 检查是否包含某个类
  // 执行某些操作
}
```

#### 4. 节点操作：创建、添加、删除
这是动态改变页面结构的核心。

```javascript
// 创建新元素
const newDiv = document.createElement('div');
newDiv.textContent = '我是新创建的div';
newDiv.className = 'new-element';

// 将新元素添加到DOM树中
const container = document.querySelector('#container');
// 追加到末尾
container.appendChild(newDiv);
// 插入到特定位置
const referenceElement = document.querySelector('.reference');
container.insertBefore(newDiv, referenceElement); // 在referenceElement之前插入

// 克隆元素
const clonedDiv = newDiv.cloneNode(true); // 参数true表示深度克隆，包括所有子节点

// 删除元素
// 现代方法（更简洁）
newDiv.remove();
// 传统方法（需要先找到父元素）
container.removeChild(newDiv);
```

**性能技巧**：当需要向 DOM 中一次性添加多个节点时，使用 `DocumentFragment` 作为临时容器可以显著提高性能，因为它能减少重排和重绘的次数。

```javascript
const fragment = document.createDocumentFragment(); // 创建一个文档片段

for (let i = 0; i < 100; i++) {
  const li = document.createElement('li');
  li.textContent = `项目 ${i}`;
  fragment.appendChild(li); // 先在内存中操作，不直接操作DOM
}

document.getElementById('list').appendChild(fragment); // 一次性插入真实DOM
```

#### 5. 事件处理
让页面能够响应用户交互。

```javascript
const button = document.querySelector('#myButton');

// 添加事件监听
button.addEventListener('click', function(event) {
  console.log('按钮被点击了！');
  console.log(event.target); // 获取触发事件的元素
});

// 事件委托：当需要给多个子元素添加相同事件时，高效的做法是给父元素添加一个事件监听器
const list = document.querySelector('#myList');
list.addEventListener('click', function(event) {
  if (event.target.tagName === 'LI') { // 检查事件源是否是LI元素
    console.log('你点击了:', event.target.textContent);
  }
});

// 移除事件监听：需要使用命名函数，匿名函数无法移除
function handleClick() {
  console.log('只会触发一次');
  button.removeEventListener('click', handleClick);
}
button.addEventListener('click', handleClick);
```

### 💎 最佳实践与常见误区

1.  **性能优化**：DOM 操作通常是昂贵的，应尽量减少直接操作。对于样式的多次修改，最好合并到一个类中一次切换。对于大量数据列表的更新，使用文档片段（`DocumentFragment`）或字符串模板一次性插入。
2.  **事件委托**：对于动态生成的元素列表，或者需要给大量相似元素绑定事件的情况，使用事件委托可以大幅减少事件监听器的数量，提升性能。
3.  **安全性**：使用 `innerHTML` 插入用户输入的内容时，一定要对内容进行转义，防止 XSS 攻击。如果只是显示纯文本，优先使用更安全的 `textContent`。

希望这些更详细的实例能帮助你更好地理解和运用 DOM 操作。实践是学习的关键，建议你多动手尝试这些代码。如果你对某个特定操作还有疑问，我们可以继续深入探讨。