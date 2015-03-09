使用装饰器的一个副作用就是，装饰之后函数丢失了它本来的\__name__,\__doc__及\__module__属性。
```python
def my_decorator(f):
    def wrapper(*args, **kwds):
        print 'Calling decorated function'
        return f(*args, **kwds)
    return wrapper

@my_decorator
def example():
    """Docstring"""
    print 'Called example function'

example()
print example.__name__
print example.__doc__
```
输出的结果是:

    >>>Calling decorated function
    >>>Called example function
    >>>wrapper
    >>>None
    
使用functools.wraps中@wraps装饰器可以让丢失的函数属性\__name__,\__doc__,\__module__恢复

```python
from functools import wraps
def my_decorator(f):
    @wraps(f)
    def wrapper(*args, **kwds):
        print 'Calling decorated function'
        return f(*args, **kwds)
    return wrapper

@my_decorator
def example():
    """Docstring"""
    print 'Called example function'

example()
print example.__name__
print example.__doc__
```
加入`@wraps`的输出结果是:

    >>>Calling decorated function
    >>>Called example function
    >>>example
    >>>Docstring
    
###参考
http://blog.jkey.lu/2013/03/15/python-decorator-and-functools-module/
  

