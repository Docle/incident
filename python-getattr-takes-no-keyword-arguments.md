## Python getattr() takes no keyword arguments

- **Problem**

  想通过 `pertial()` 函数给 `getattr()` 设置固定返回某个对象实例的属性值，且默认返回值设为 `None` ，如下：

  ```python
  from functools import partial
  
  class A():
      pass
  
  ins = A()
  ins.a = 'a'
  ins.c = 'c'
  
  my_getattr = partial(getattr, ins, default=None)
  
  my_getattr('a')
  ```

  运行报错

  ```
  TypeError: getattr() takes no keyword arguments
  ```

- **Solution**

  由于 Python 的底层实现是 C 语言，许多内置函数和方法实际上不支持关键字参数。所以，尽管文档说明中`getattr()`的第三个参数名称为`default`，该函数也必须使用位置参数。若要实现`pertial()`的效果可以使用匿名函数

  ```python
  lambda name: getattr(ins, name, None)
  ```

  或自行封装函数

  ```python
  def my_getattr(name):
      return getattr(ins, name, None)
  ```

- **Reference**

  <https://stackoverflow.com/questions/24463202/typeerror-get-takes-no-keyword-arguments/24463222#24463222>
