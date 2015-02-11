> 带有 yield 的函数在 Python 中被称之为 generator（生成器），何谓 generator ？
> 先抛开 generator，以一个常见的编程题目来展示 yield 的概念.

##如何生成斐波那契数列
斐波那契（Fibonacci）数列是一个非常简单的递归数列，除第一个和第二个数外，任意一个数都可由前两个数相加得到。用计算机程序输出斐波那契數列的前 N 个数是一个非常简单的问题，许多初学者都可以轻易写出如下函数：

###清单 1. 简单输出斐波那契数列前 N 个数 
```python
def fab(max): 
    n, a, b = 0, 0, 1 
    while n < max: 
        print b 
        a, b = b, a + b 
        n = n + 1
```

