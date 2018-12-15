---
title: 记仇小本本
date: 2018-12-15
update: 2018-12-15
tags: [re.split,re.sub]

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

