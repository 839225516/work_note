JavaScript基础
=================


**名称解析顺序**

JavaScript中的所有作用域，包括全局作用域，都有一个特别的名称 ``this`` 指向当前对象。

函数作用域内也有默认的变量 ``arguments`` ，其中包含了传递到函数中的参数。

比如，当访问函数内的 ``foo`` 变量时，JavaScript会按照下面顺序查找：

1. 当前作用域内是否有 ``var foo`` 的定义。

2. 函数形式参数是否有使用 ``foo`` 名称的。

3. 函数自身是否叫做 ``foo`` 。

4. 回溯到上一级作用域，然后从 #1 重新开始。


材料
---------

- Learning from jQuery
- JavaScript DOM编程艺术
- High Performance JavaScript
- `Learning JavaScript Design Patterns <http://addyosmani.com/resources/essentialjsdesignpatterns/book/>`_
- JavaScript Patterns
- 高性能网站建设指南
