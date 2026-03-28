---
author: 'Lanxinmob'
title: 'React Note'
postSlug: 'React Note'
featured: false
draft: false
tags:
  - '笔记'
  - 'react'
  - '前端'
ogImage: ''
description: ''
pubDatetime: 2026-01-17T09:00:00Z
toc: true
---

## 渲染列表

**在 map() 循环里，** 紧跟在 return 后面的第一个标签（无论是 HTML 标签还是自定义组件），必须拥有 key。

```javascript
import { recipes } from './data.js';

function Recipe({ id, name, ingredients }) {
  return (
    <div>
      <h2>{name}</h2>
      <ul>
        {ingredients.map(ingredient =>
          <li key={ingredient}>
            {ingredient}
          </li>
        )}
      </ul>
    </div>
  );
}

export default function RecipeList() {
  return (
    <div>
      <h1>菜谱</h1>
      {recipes.map(recipe =>
        <Recipe {...recipe} key={recipe.id} />
      )}
    </div>
  );
}
```

#### 保持组件的存粹

- 一个组件必须是纯粹的，就意味着：
  - **只负责自己的任务。** 它不会更改在该函数调用前就已存在的对象或变量。
  - **输入相同，则输出相同。** 给定相同的输入，组件应该总是返回相同的 JSX。
  - 你不应该改变任何用于组件渲染的输入。这包括 props、state 和 context。通过设置 state 来更新界面，而不要改变预先存在的对象。
  - 需要时可以创建局部变量来满足突变需求。

#### state 如同一张快照

```javascript
import { useState } from 'react';

export default function Counter() {
  const [number, setNumber] = useState(0);

  return (
    <>
      <h1>{number}</h1>
      <button onClick={() => {
        setNumber(number + 5);
        setTimeout(() => {
          alert(number);
        }, 3000);
      }}>+5</button>
    </>
  )
}

```

**一个 state 变量的值永远不会在一次渲染的内部发生变化，** 即使其事件处理函数的代码是异步的。在 **那次渲染的** `onClick` 内部，`number` 的值即使在调用 `setNumber(number + 5)` 之后也还是 `0`。它的值在 React 通过调用你的组件“获取 `UI` 的快照”时就被“固定”了。

#### 在下次渲染前多次更新同一个 state

**React 会等到事件处理函数中的** 所有 **代码都运行完毕再处理你的 state 更新。** 

如果你想在下次渲染之前多次更新同一个 state，你可以像 `setNumber(n => n + 1)` 这样传入一个根据队列中的前一个 state 计算下一个 state 的 **函数**，而不是像 `setNumber(number + 1)` 这样传入 **下一个 state 值**。

## [挑战二](https://zh-hans.react.dev/learn/updating-arrays-in-state#remove-an-item-from-the-shopping-cart)

在 JavaScript 中，**return 关键字后面不能直接换行**。如果你换行了，JavaScript 会自动在 return 后面加一个分号（这叫自动分号插入机制，ASI），导致函数返回 undefined，而下一行的 JSX 代码根本不会执行。

如果函数体只有一行代码，直接去掉花括号 {}，箭头后面的表达式会自动被返回。

```javascript
setArtists(
  artists.filter(a => a.id !== artist.id)
);
```

如果保留花括号，就必须加上 return 关键字，手动返回。

```javascript
setArtists(
  artists.filter(a => {
    return a.id !== artist.id;
  })
);
```

## [挑战四](https://zh-hans.react.dev/learn/updating-arrays-in-state#fix-the-mutations-using-immer)

- **Immer库** 
  它的原理是给你一个“**Draft**”。你可以直接在草稿上进行修改（比如 d.title = ...），就像写普通的、可变的 JS 代码一样。修改这个特定的对象，**Immer 会在后台自动监听你对 draft 做的所有修改，然后生成那个不可变的、全新的状态对象。**因为它是通过“监听修改”来工作的，所以**不需要**返回任何东西。

- ```javascript
   function handleChangeTodo(nextTodo) {
      setTodos(draft=>{
      const d = draft.find(t => t.id === nextTodo.id);
      d.title = nextTodo.title;
      d.done = nextTodo.done;
      })
    }
  ```

- **let** 是 JavaScript 中用来**声明变量**的一个关键字。

  let 声明的变量只在它所在的 **代码块 { ... }** 内有效。和 `const` 不同，可以给 `let` 声明的变量重新赋值。

  在同一个作用域内，你不能用 `let` 声明同名的变量。

# 用 React 实现 UI：

#### 1.**定位**你的组件中不同的视图状态

- **无数据**：表单有一个不可用状态的“提交”按钮。
- **输入中**：表单有一个可用状态的“提交”按钮。
- **提交中**：表单完全处于不可用状态，加载动画出现。
- **成功时**：显示“成功”的消息而非表单。
- **错误时**：与输入状态类似，但会多错误的消息。

#### 2.**确定**是什么触发了这些 state 的改变

- **人为**输入。比如点击按钮、在表单中输入内容，或导航到链接。
- **计算机**输入。比如网络请求得到反馈、定时器被触发，或加载一张图片。

#### 3.**表示**内存中的 state（需要使用 `useState`）

#### 4.**删除**任何不必要的 state 变量

#### 5.**连接**事件处理函数去设置 state













---

参考：

[React](https://zh-hans.react.dev/learn/)





