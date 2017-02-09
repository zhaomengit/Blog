
```python
i = 1

def testyield():
    while True:
        global i
        print("start --", i)
        n = yield

        print('n is --', n)
        i += 1
        print('end --', i)



l = testyield()
l.send(None)
l.send(10)
```

生成器的send()和close()方法

生成器中还有两个很重要的方法：send()和close()。

    send(value):
    
从前面了解到，next()方法可以恢复生成器状态并继续执行，其实send()是除next()外另一个恢复生成器的方法。

Python 2.5中，yield语句变成了yield表达式，也就是说yield可以有一个值，而这个值就是send()方法的参数，所以send(None)和next()是等效的。同样，next()和send()的返回值都是yield语句处的参数（yielded value）

关于send()方法需要注意的是：调用send传入非None值前，生成器必须处于挂起状态，否则将抛出异常。也就是说，第一次调用时，要使用next()语句或send(None)，因为没有yield语句来接收这个值。

    close():
    
这个方法用于关闭生成器，对关闭的生成器后再次调用next或send将抛出StopIteration异常。

下面看看这两个方法的使用：

```python
def Zrange(n):
    i= 0
    while i < n:
        val = yield i    # val 是send的参数
        print "val is: ", val
        i += 1

zrange = Zrange(5)

print zrange.next()

print zrange.next()

print zrange.send("Hello")

print zrange.close()

print zrange.send("world")
```

结果:

```
0
val is:  None
1
val is:  Hello
Traceback (most recent call last):
2
  File "/Users/zhaomeng1/work/qdagent/test2.py", line 18, in <module>
None
    print zrange.send("world")
StopIteration

Process finished with exit code 1
```
