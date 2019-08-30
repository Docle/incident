---
title: 记仇小本本
date: 2019-8-30
tags: [json.loads, GB2312, GBK]
---

## Invalid control character with Python json.loads

- **Problem**

  使用 `json.loads()` 方法格式化以下的字符串时出错：

  ```python
  import json
  
  s = """
  {"docs":[
  {
  "content":"<br>
  　　2018年第3号&nbsp;<br>"
  }
  ]}
  """
  print(json.loads(s))
  ```

  错误信息如下

  ```
  json.decoder.JSONDecodeError: Invalid control character at: line 4 column 16 (char 28)
  ```

- **Solution**

  可以通过给` loads()` 传递 `strict=False` 参数来允许字符串中出现控制字符：

  ```python
  json.loads(a, strict=False)
  ```

  > If strict is false (`True` is the default), then control characters will be allowed inside strings. Control characters in this context are those with character codes in the 0–31 range, including `'\t'` (tab), `'\n'`, `'\r'` and `'\0'`.

- **Reference**

  https://stackoverflow.com/questions/22394235/invalid-control-character-with-python-json-loads

## GB2312、GBK、GB18030 字符集的主要区别

- **TEXT**

  1. **GB2312**

     **GB 2312 或 GB 2312-80 是中国国家标准简体中文字符集**，全称《信息交换用汉字编码字符集·基本集》，又称 GB 0，由中国国家标准总局发布，1981 年 5 月 1 日实施。GB 2312 编码通行于中国大陆；新加坡等地也采用此编码。中国大陆几乎所有的中文系统和国际化的软件都支持 GB 2312。

     **GB 2312 标准共收录 6763 个汉字**，其中一级汉字 3755 个，二级汉字 3008 个；同时收录了包括拉丁字母、希腊字母、日文平假名及片假名字母、俄语西里尔字母在内的 682 个字符。

     - GB 2312 的出现，基本满足了汉字的计算机处理需要，它所收录的汉字已经覆盖中国大陆99.75% 的使用频率。

     - 对于人名、古汉语等方面出现的罕用字，GB 2312 不能处理，这导致了后来 GBK 及 GB 18030 汉字字符集的出现。

  2. **GBK**

     **GBK** 即**汉字内码扩展规范**，**K** 为汉语拼音 Kuo Zhan（扩展）中“扩”字的声母。英文全称 Chinese Internal Code Specification。

     **GBK 共收入 21886 个汉字和图形符号**，包括：

     - GB 2312 中的全部汉字、非汉字符号。
     - BIG5 中的全部汉字。
     - 与 ISO 10646 相应的国家标准 GB 13000 中的其它 CJK 汉字，以上合计 20902 个汉字。
     - 其它汉字、部首、符号，共计 984 个。

     GBK 向下与 GB 2312 完全兼容，向上支持 ISO 10646 国际标准，在前者向后者过渡过程中起到的承上启下的作用。

  3. **GB18030**

     **GB 18030**，全称：**国家标准 GB 18030-2005《信息技术中文编码字符集》**，是中华人民共和国现时最新的内码字集，是 GB 18030-2000《信息技术信息交换用汉字编码字符集基本集的扩充》的修订版。
     **GB 18030** **与 GB 2312-1980 和 GBK 兼容，共收录汉字70244个。**

     - 与 UTF-8 相同，采用多字节编码，每个字可以由 1 个、2 个或 4 个字节组成。
     - 编码空间庞大，最多可定义 161 万个字符。
     - 支持中国国内少数民族的文字，不需要动用造字区。
     - 汉字收录范围包含繁体汉字以及日韩汉字

- **Reference**

  https://www.zhihu.com/question/19677619
