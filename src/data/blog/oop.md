---
author: 'Lanxinmob'
title: '暑期日记10'
postSlug: 'summer-diary-10'
featured: false
draft: false
tags:
  - '笔记'
  - 'Python'
ogImage: ''
description: 'oop'
pubDatetime: 2025-07-15T09:00:00Z
toc: true
---

## Stream

- 类似链表，但是惰性计算
- ```python
  class Stream:
        """A lazily computed linked list."""
        class empty:
            def __repr__(self):
                return 'Stream.empty'
        empty = empty()
        def __init__(self, first, compute_rest=lambda: empty):
            assert callable(compute_rest), 'compute_rest must be callable.'
            self.first = first
            self._compute_rest = compute_rest
        @property
        def rest(self):
            """Return the rest of the stream, computing it if necessary."""
            if self._compute_rest is not None:
                self._rest = self._compute_rest()
                self._compute_rest = None
            return self._rest
        def __repr__(self):
            return 'Stream({0}, <...>)'.format(repr(self.first))
  ```
-  创建了一个实例`empty`就相当于链表末尾的`None`，但是又可以区分于`None`。
   
- `self._compute_rest` `self._rest`这种下划线是广泛遵守的编程规定，意思是这是作为内部变量用于内部细节的实现，区分于外部用户调用时使用的公共接口。这里如果没有下划线，调用 `rest` 时就会不断调用自己陷入无限递归。
- `self._compute_rest = compute_rest`这里是将函数对象赋予实例属性，而`self._rest = self._compute_rest()`是调用了这个函数对象将返回值赋予实例属性。
#### 装饰器
- `@property`对于紧跟在后面定义的函数创建的是一个只读的属性。当你访问这个“属性”时，你不需要加括号 ()，就像访问一个普通的变量一样，但实际上 Python 会自动替你执行它下面的那个函数，并返回函数的结果。这样显然比加上（）更像是一个属性。
- ```python
  class SafeCircle:
    def __init__(self, radius):
        # 将 radius 变成一个属性，以便在创建时就进行检查
        self.radius = radius
  
    @property
    def radius(self):
        """这是 'getter'，用于获取值"""
        return self._radius
  
    @radius.setter
    def radius(self, value):
        """这是 'setter'，在赋值时调用，用于检查值的合法性"""
        if value < 0:
            raise ValueError("Radius cannot be negative.")
        print(f"--- Setter 被调用，将半径设置为 {value} ---")
        self._radius = value 
  
    c_safe = SafeCircle(10) # <-- Setter 在这里被第一次调用
  
    c_safe.radius = 20 # <-- Setter 再次被调用
  ```
- `@rest.setter`（rest是函数名也就是属性名）使用这个装饰器就可以在外部对属性直接进行赋值。
#### 字符串表示形式
- `str()`用户友好，易读

- `repr()`开发者友好，精确，用于调试

- 调用 `print()` 时会首先尝试寻找并调用该对象的`__str__()`方法，如果没有就回退，寻找并调用该对象的`__repr__()`方法

- 在python的交互式环境 (>>>)中相反，总是调用`__repr__()` 来显示结果。

- ```python
  eval(repr(object)) == object
  >>> repr(min)
  '<built-in function min>'
  
  >>> from datetime import date
  >>> tues = date(2011, 9, 12)
  >>> repr(tues)
  'datetime.date(2011, 9, 12)
  >>> str(tues)
  '2011-09-12'
  ```