---
title: 记仇小本本
date: 2018-11-6
update: 2018-11-24
tages: [Requests,BeautifulSoup,func_timeout]
---

## Requests 返回 403 Forbidden 页面

* **Date**: 2018-11-6

* **Problem**

  请求[职友集](https://m.jobui.com)的时候，返回 403 页面。开始代码如下：

  ```python
  import requests

  url = 'https://www.jobui.com/'
  r = requests.get(url, verify=False)
  print(r.text)
  ```

  控制台打印信息：

  ```html
  <html>
  <head><title>403 Forbidden</title></head>
  <body bgcolor="white">
  <center><h1>403 Forbidden</h1></center>
  <hr><center>nginx</center>
  </body>
  </html>
  ```

* **Solution**

  该错误发生是因为 requests 请求是没有定义 User-Agent。修改代码如下：

  ```python
  import requests

  url = 'https://www.jobui.com/'
  headers = {'User-Agent': 'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_11_5) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/50.0.2661.102 Safari/537.36'}
  r = requests.get(url, headers=headers, verify=False)
  print(r.text)
  ```

* **Reference**

  https://stackoverflow.com/questions/38489386/python-requests-403-forbidden

## BeautifulSoup 对象的选择域

* **Date**: 2018-11-19

* **Text**

  如下先在某个文件中找到一个 `id=test` 的 `div` 标签，接着想找到该标签下的直接子 `li` 标签：

  ```python
  from bs4 import BeautifulSoup

  a = """
  <div id="test">
  <li>1</li>
  <span><li>2</li></span>
  <li>1</li>
  </div>
  """
  soup = BeautifulSoup(a)
  div = soup.select_one('div#test')
  li = div.select('div > li')
  print(li)
  ```

  执行程序会发现 `li` 是一个空列表。

  其实对 select 和 find 方法来说，选择的标签应该是在其内部包含的标签。对于上面选择到的 `div` 打印出来如下：

  ```html
  <div id="test">
  <li>1</li>
  <span><li>2</li></span>
  <li>1</li>
  </div>
  ```

  看似这个对象最外层是一个 `div` 标签，但我们这样理解或许会更好：最外层的这一个标签是用来表识当前的 BeautifulSoup 对象的，而这个对象内部包含的内容实际上如下：

  ```html
  <li>1</li>
  <span><li>2</li></span>
  <li>1</li>
  ```

  即这个对象内部是没有 `div` 标签的，因此 `div.select('div > li')` 会找不到 `div` 标签，也就找不到 `li` 标签了。如下的代码可以找到相应的内容：

  ```python
  soup.select('div#test > li')  # 找到 <div> 的直接子 <li>
  div.select('span > li')  # 找到 <span> 对直接子 <li>
  ```

## BeautifulSoup select 方法的优雅写法

* **Date**: 2018-11-14

* **Text**

  刚开始使用 BeautifulSoup 的时候习惯的使用 find 方法，后来发现 select 方法与css 选择器一样写起来更简洁开始转用 select 方法。但不自觉的就受到 find 方法的影响，比如以下是某个html 文本中的一段，用 find 方法和 select 方法通过 class 属性找到该 `div` 标签，然后找到其下的 `ul` 标签：

  ```python
  from bs4 import BeautifulSoup

  a = """
  <div class='swf-contA'>
  <h2 class='fs16'>步步高教育电子招聘概况</h2>
  <ul class='fs16'>
  <li>
          <dfn>在招职位：</dfn>
      </li>
      <li>
          <dfn>人员结构：</dfn>
      </li>
      <li>
          <dfn>历年招聘：</dfn>
      </li>
  </ul>
  </div>
  """
  soup = BeautifulSoup(a)
  # find 方法
  ul = soup.find('div', attrs={'class': 'swf-contA'}).find('ul')
  # select 方法
  ul = soup.select_one('div.swf-contA').select_one('ul')
  ```

  看起来 select 方法已经比 find 方法要简洁了不少，但其实还有更优雅的写法：

  ```python
  ul = soup.select_one('div.swf-contA ul')
  ```

  这样写还有一个好处是，在爬虫中因为有很多变数，所以会有很多判断，如判断是否真的存在找到 ul，如果是连续的 select 或 find 方法如下：

  ```python
  ul = soup.select_one('div.swf-contA').select_one('ul')
  if ul:
      print('success')
  else:
      print('fail')
  ```

  正常来说以上代码没有问题，但如果爬取的页面改版了或者你不小心把属性的值打错了，程序就不像预期的运行了，例如以下：

  ```python
  ul = soup.select_one('div.swf').select_one('ul')
  if ul:
      print('success')
  else:
      print('fail')
  ```

  执行代码不会打印 `fail` ，而是抛出了错误：

  ```
  发生异常: AttributeError
  'NoneType' object has no attribute 'select_one'
    File "/Users/abel/Desktop/Untitled-1.py", line 25, in <module>
      ul = soup.select_one('div.swf').select_one('ul')
  ```

  在第一个 select_one 没有找到返回 `None` 的情况下再次调用 select_one 引发了这个错误。而如果是以下代码：

  ```python
  ul = soup.select_one('div.swf ul')
  if ul:
      print('success')
  else:
      print('fail')
  ```

  程序如期打印 `fail` 。

## Python 超时装饰器 func_timeout

* **Date**: 2018-11-17

* **Text**

  在一个爬虫程序中经常会出现卡死的情况，打印日志查看确定卡死的位置在requests 的方法中，但无法进一步确定导致该异常的原因

  既然无法解决问题，那么我们可以选择绕过这个问题。一个很容易想到的方法：是否可以监控函数的执行时间，一旦超过了时间便退出重新发起请求。google 发现了func_timeout 这个库。修改 requests 请求的示例代码如下：

  ```python
  import time
  from func_timeout import func_timeout, FunctionTimedOut
  import requests

  url = 'https://www.jobui.com'
  headers = {'User-Agent': 'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_11_5) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/50.0.2661.102 Safari/537.36'}
  try:
      r = func_timeout(100, requests.get, args=(url,), kwargs={'headers': headers})
  except FunctionTimedOut:
      print('time out')
  except Exception as _e:
      print(_e)
  else:
      print(r.status_code)
  ```

* **Reference**

  https://github.com/kata198/func_timeout

* **Ghost work**

  requests 请求为何会在中间卡死尚未得知。