---
title: 记仇小本本
date: 2018-12-15
update: 2018-12-27
tags: [re.split,re.sub,Requests]

---

## Python 使用 re.split 和 re.sub 优雅的处理 string

- **Date**：2018-12-15

- **Text**

  字符串操作中字符分割和替换应该是两个最常用的操作了，对于这两个操作 Python 的字符串类型提供了 `split()` 和 `replace()` 方法，然而这两个方法一次都只能处理 ”一个“ 字符，在很多情况下并不能足够的胜任，如根据多个字符切割字符串或者比较删除字符串中的空白符。对于讲究简洁的 Python 来说，当然有更优雅的方法。

  - 根据多个字符切割字符串

    如把 `hello,world.i am fine.` 根据 `,` `.` ` ` 分割。

    如果单纯的使用 `str.split()` 来实现将十分不便，而使用 `re.split()` 只要一行代码即可：

    ```python
    >>> s = 'hello,world.i am fine.'
    >>> re.split(r'[,. ]', s)
    ['hello', 'world', 'i', 'am', 'fine', '']
    ```

  - 删除多个字符或者把多个字符替换成特定的字符

    如常见的删除字符串中的空白符，而空白符有很多种：` ` `\r` `\t` `\n`  等。

    如果使用 `str.replace()` 实现如下：

    ```python
    >>> s = 'it is\rhard to\tdo it with replace\n'
    >>> s.replace(' ', '').replace('\r', '').replace('\t', '').replace('\n', '')
    'itishardtodoitwithreplace'
    ```

    作为一个 Python 程序员怎么能忍受如此丑陋的写法呢，`re.sbu()` 的实现就优雅的多了：

    ```python
    >>> s = 're.sub is\ra\tgraceful method\n'
    >>> re.sub(r'[ \r\t\n]', r'', s)
    're.subisagracefulmethod'
    ```

    既然用到了正则表达式，还有更简洁的写法：

    ```python
    >>> s = 're.sub is\ra\tgraceful\xa0method\n.\u3000'
    >>> re.sub(r'\s', r'', s)
    're.subisagracefulmethod.'
    ```

    `\s` 可以匹配任意空白字符，甚至包括 `\xa0` 和 `\u3000` 。

## Requests 设定 timeout 参数防止程序失去响应

- **Date**: 2018-12-27

- **Problem**

  在十一月的一个笔记 [Python 超时装饰器 func_timeout](https://github.com/Docle/incident/blob/master/2018-11-incidents.md#python-%E8%B6%85%E6%97%B6%E8%A3%85%E9%A5%B0%E5%99%A8-func_timeout) 中提到了 requests 的一个失去响应的问题。

- **Solution**

  最近查看 request 文档的时候发现了 `timeout` 参数，文档说明如下：

  > 你可以告诉 requests 在经过以 `timeout` 参数设定的秒数时间之后停止等待响应。基本上所有的生产代码都应该使用这一参数。如果不使用，你的程序可能会永远失去响应

  因为在生产环境中一直使用的是封装好的方法而忽略了在使用原始 request 方法时使用 `timeout` 参数，故而程序再运行一段时间后会因为发生服务器无响应失去响应的情况。

  修改原来的示例代码如下：

  ```python
  import requests
  
  url = 'https://www.jobui.com'
  headers = {'User-Agent': 'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_11_5) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/50.0.2661.102 Safari/537.36'}
  try:
      r = requests.get(url, headers=headers, timeout=100)
  except requests.exceptions.Timeout:
      print('time out')
  except Exception as _e:
      print(_e)
  else:
      print(r.status_code)
  ```

- **Reference**

  http://docs.python-requests.org/zh_CN/latest/user/quickstart.html#id10
 
