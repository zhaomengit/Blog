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
执行 fab(5)，我们可以得到如下输出：

    >>> fab(5) 
        1 
        1 
        2 
        3 
        5

结果没有问题，但有经验的开发者会指出，直接在 fab 函数中用 print 打印数字会导致该函数可复用性较差，因为 fab 函数返回 None，其他函数无法获得该函数生成的数列。

要提高 fab 函数的可复用性，最好不要直接打印出数列，而是返回一个 List。以下是 fab 函数改写后的第二个版本：

###清单 2. 输出斐波那契數列前 N 个数第二版
```python
def fab(max): 
    n, a, b = 0, 0, 1 
    L = [] 
    while n < max: 
        L.append(b) 
        a, b = b, a + b 
        n = n + 1 
    return L
```

可以使用如下方式打印出 fab 函数返回的 List:

    >>> for n in fab(5): 
    ...     print n 
    ... 
    1 
    1 
    2 
    3 
    5
    
改写后的 fab 函数通过返回 List能满足复用性的要求，但是更有经验的开发者会指出，该函数在运行中占用的内存会随着参数 max 的增大而增大，如果要控制内存占用，最好不要用 List来保存中间结果，而是通过 iterable 对象来迭代。
例如，在 Python2.x 中，代码：

###清单 3. 通过 iterable 对象来迭代
```python
for i in range(1000): pass
```
会导致生成一个 1000 个元素的 List，而代码：
```python
for i in xrange(1000): pass
```
则不会生成一个 1000 个元素的 List，而是在每次迭代中返回下一个数值，内存空间占用很小。因为 xrange 不返回 List，而是返回一个 iterable 对象。

利用 iterable 我们可以把 fab 函数改写为一个支持 iterable 的 class，以下是第三个版本的 Fab：

###清单 4. 第三个版本 
```python
class Fab(object): 

    def __init__(self, max): 
        self.max = max 
        self.n, self.a, self.b = 0, 0, 1 

    def __iter__(self): 
        return self 

    def next(self): 
        if self.n < self.max: 
            r = self.b 
            self.a, self.b = self.b, self.a + self.b 
            self.n = self.n + 1 
            return r 
        raise StopIteration()
```

