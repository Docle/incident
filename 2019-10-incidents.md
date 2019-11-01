---
title: 记仇小本本
date: 2019-11-01
tags: [NCR]
---

## numeric character reference(NCR) 转义序列

- **Problem**

  某个网站接口返回的数据形如：

  ```
  &#24352;&#24503;&#26126;
  ```

- **Solution**

  形如：

  ```
  &name;
  &#dddd;
  &#xhhhh;
  ```

  这样的字符是HTML、XML等SGML的转义序列，而不是某种编码格式。

  以 HTML 为例，这三种转义序列都称作 character reference：

  - 第一种是 character entity reference，后接预先定义的 entity 名称，而 entity 声明了自身指代的字符。
  - 后两种是 numeric character reference（NCR），数字取值为目标字符的 Unicode code point；以「&#」开头的后接十进制数字，以「&#x」开头的后接十六进制数字。

  在 Python3.6 中可以使用标准库 `html` 提供的方法转义：

  ```
  >>> import html
  >>> html.unescape('&#24352;&#24503;&#26126;')
  '张德明'
  ```

- **Reference**

  https://www.zhihu.com/question/21390312/answer/18091465
