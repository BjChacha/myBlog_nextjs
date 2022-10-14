---
title: '声明式编程 VS 命令式编程'
date: '2022-10-14'
# lastmode: '2022-10-06'
tags: ['log', 'programme']
draft: false
summary: '未来也许属于声明式编程，起码现在还不是。'
# images: ['']
authors: ['default']
layout: PostLayout
---

<!-- # 声明式编程 VS 命令式编程 -->

## 什么是声明式编程/命令式编程

取自维基百科的定义

- Declarative programming is a programming paradigm ... that expresses the logic of a computation **without describing its control flow**.[^1]
- Imperative programming is a programming paradigm ... that uses statements that **change a program's state**.[^2]

总结来说：

- 声明式编程是通过*声明结果*来达到目的；
- 命令式编程是通过*描述过程*来达到目的；

也就是说，**命令式编程**相当于程序员在一步步地描述实现功能的步骤。比如要将一块蛋糕切成两块，那么**命令式编程**的做法就是：

1. 取出刀子；
2. 找到可以将蛋糕一分为二的虚拟切割线；
3. 用刀沿着切割线用力切；
4. 放回刀子。

如果是**声明式编程**，那么就是：

1. 给爷用刀把蛋糕切成一半。

再用代码来举例子，用`Python`查找价格超过`100`元的商品，一般使用**命令式编程**：

```python
def get_goods():
  res = []
  for good in goods:
    if good['price'] >= 100:
      res.append(good)
  return res
```

另外，用`SQL`语句查询相同条件的内容，则是**声明式编程**：

```sql
SELECT * FROM goods where price >= 100;
```

## 声明式编程/命令式编程怎么样

- 声明式编程比命令式编程对用户更友好，用户可以关心更少的细节；
- 声明式编程可以有多种底层实现，可以保持目标不变的前提下不断优化；
- 但声明式的颗粒度不及命令式编程，某些情况下仍需要命令式语言来辅助声明式语言的目标描述；
  - 这里不难理解：命令式的*多行描述方式*所含信息量会比声明式的*单行声明*要多。换句话说，需要用命令式编程才能描述的目标所含信息量，用单行声明式编程是无法充分表达出来的。
- 其实在用`C/C++`、`Java`等语言编程时，一般会将一个功能封装成一个函数，然后方便后续调用使用（比如`sm = sum(a, b)`），这里其实是*声明式*使用。即，**通过适当的封装、组件化，命令式也可以变得更加“声明式”**

## 总结

- 声明式编程使用方便，易于理解，但表达能力有限，为了强化表达能力而往往使用了命令式的扩展；
- 在日常的组件化、接口化的工作中，命令式编程也向声明式靠拢，变得更加简单易用；
- 个人觉得，没必要因为声明式/命令式而划分党派。一切行为的目的都是为了效率和可用性而服务的。\*_也许在未来，声明式编程会是未来，但起码现在还不是。_

## 参考

[^1]: https://en.wikipedia.org/wiki/Declarative_programming
[^2]: https://en.wikipedia.org/wiki/Imperative_programming
