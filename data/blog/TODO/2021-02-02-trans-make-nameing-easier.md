---
title: 'TRANS->让命名简单点'
description: '起名字是一门艺术，不管在编程还是生活。'
toc: true
hide: false
layout: post
categories: [translations, common]
author: Artem Zakharchenko
---

在编程工作中，规范统一的命名规则不仅方便协同合作，也会方便回顾自己的个人代码。网上有许多关于代码命名规范的说明，比如[《Google 开源项目风格指南》](https://zh-google-styleguide.readthedocs.io/en/latest/contents/)。本文着重讲的是关于命名内容的一些规范。

翻译自[原文](https://github.com/kettanaito/naming-cheatsheet)

### 用英文命名

不管怎么说，目前在编程界的主流语言是英语，基本所有主流的编程语法都是用英文写的，文档教程也是英文居多。所以这方面建议还是“入乡随俗”。

```js
/* Bad */
const primerNombre = 'Gustavo'
const amigos = ['Kate', 'John']

/* Good */
const firstName = 'Gustavo'
const friends = ['Kate', 'John']
```

### 命名规范

常见的命名规范有`CamelCase`(驼峰命名法)和`snake_case`(蛇形命名法)。一般选哪个都行，关键是要统一。

```js
/* Bad */
const page_count = 5
const shouldUpdate = true

/* Good */
const pageCount = 5
const shouldUpdate = true

/* Good as well */
const page_count = 5
const should_update = true
```

### S-I-D

命名一定要简短、直观、有描述性。

- 简短(Short)：命名太长不利于键入。
- 直观(Intuitive)：命名要符合日常语言习惯。
- 有描述性(Descriptive)：命名要能反映出其变量的意义。

```js
/* Bad */
const a = 5 // "a" could mean anything
const isPaginatable = a > 10 // "Paginatable" sounds extremely unnatural
const shouldPaginatize = a > 10 // Made up verbs are so much fun!

/* Good */
const postCount = 5
const hasPagination = postCount > 10
const shouldPaginate = postCount > 10 // alternatively
```

## 变量命名

### 避免缩写

**不**要用缩写。日常聊天缩写怪已经很烦了，编程还来缩写，还让不让人好好干活。

```js
/* Bad */
const onItmClk = () => {}

/* Good */
const onItemClick = () => {}
```

### 避免文本重复

多个变量命名的文本重复会造成信息冗余，可读性下降。前后有多个命名，尽量以变量特点为命名准则（参考前面的 S-I-D 原则）。

```js
class MenuItem {
  /* Method name duplicates the context (which is "MenuItem") */
  handleMenuItemClick = (event) => { ... }

  /* Reads nicely as `MenuItem.handleClick()` */
  handleClick = (event) => { ... }
}
```

### 反映预期结果

变量命名最好能够反映其预期的结果，尤其是布尔值的变量。

```js
/* Bad */
const isEnabled = itemCount > 3
return <Button disabled={!isEnabled} />

/* Good */
const isDisabled = itemCount <= 3
return <Button disabled={isDisabled} />
```

### 注意单复数

一旦使用了单复数作区别，则不能混淆其作用。

```js
/* Bad */
const friends = 'Bob'
const friend = ['Bob', 'Tony', 'Tanya']

/* Good */
const friend = 'Bob'
const friends = ['Bob', 'Tony', 'Tanya']
```

## 函数命名

### A/HC/LC 模式

A/HC/LC 模式指的是：

> prefix? + action (A) + high context (HC) + low context? (LC)

具体看表格：

| Name                 | Prefix | Action (A) | High context (HC) | Low context (LC) |
| -------------------- | ------ | ---------- | ----------------- | ---------------- |
| getUser              |        | get        | User              |                  |
| getUserMessages      |        | get        | User              | Messages         |
| handleClickOutside   |        | handle     | Click             | Outside          |
| shouldDisplayMessage | should | Display    | Message           |                  |

> Note: 上述语境的交换会影响整个命名的含义。比如说`shouldUpdateComponent`指的是用户去更新组件，而`shouldComponentUpdate`指的是组件的自我更新。换言之，High context 强调的是函数的主体。

### Action 命名

常见的 Action 命名用如下：

- `get`
- `set`
- `reset`
- `fetch`
- `remove`
- `delete`
- `compose`
- `handle`

### Prefixes 命名

前缀一般用于强调或补充命名的含义。常见的 Prefix 命名如下：

- `is`
- `has`
- `should`
- `min`/`max`
- `prev`/`next`

### 宾语

函数的命名可以存在宾语，用于表示函数作用的对象。

```js
/* A pure function operating with primitives */
function filter(predicate, list) {
  return list.filter(predicate)
}

/* Function operating exactly on posts */
function getRecentPosts(posts) {
  return filter(posts, (post) => post.date === Date.now())
}
```
