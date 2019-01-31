---
title: 记仇小本本
date: 2019-1-15
update: 2019-1-31
tags: [win32com,pdfminer]
---

## Python 使用 win32com 把 .doc 文件另存为 .docx 文件

* **Date**: 2019-1-15

* **Problem**

  Python 可以使用 python-docx 库处理 .docx 文件，但对于 .doc 文件却没有一个好用的库。可以选择手动把 .doc 文件另存为 .docx 文件，但显然在实际生产中或大批量处理的时候，使用程序实现转换才是更好的选择。

* **Solution**

  以一个批量处理的程序为例：

  ```python
  from docx import Document
  from win32com import client as wc
  import os

  i = 0
  word = wc.Dispatch('Word.Application')
  for f in os.listdir('D:/doc_test'):
      if f.endswith('.doc'):
          try:
              
              d = word.Documents.Open('D:/doc_test/{}'.format(f))
          except Exception:
              print(f)
          else:
              try:
                  d.SaveAs('D:/doc_test/docx/{}.docx'.format(i), 12, False, "", True, "", False, False, False, False)
                  i += 1
                  print(i)
              except Exception:
                  print(f)
              finally:
                  d.Close()
  word.Quit()
  ```

* **Reference**

  * http://www.111cn.net/phper/160604.htm
  * https://cloud.tencent.com/developer/ask/188144

## Use pdfminer as a library

* **Date**: 2019-1-25

* **Problem**

  遇到一个需要使用 Python 提取 PDF 文档中文本的问题，搜索找到的比较好的一个工具 pdfminer，但 pdfminer 官方并不提供简单的直接作为模块导入使用。

* **Solution**

  Python3 安装 pdfminer

  ```
  $ pip install pdfminer.six
  ```

  示例代码：

  ```python
  from pdfminer.pdfinterp import PDFResourceManager, PDFPageInterpreter
  from pdfminer.converter import TextConverter
  from pdfminer.layout import LAParams
  from pdfminer.pdfpage import PDFPage
  from io import StringIO
  import os
  import re

  def convert_pdf_to_txt(path, pages=None):
      if not pages:
          pagenums = set()
      else:
          pagenums = set(pages)
      output = StringIO()
      manager = PDFResourceManager()
      converter = TextConverter(manager, output, laparams=LAParams())
      interpreter = PDFPageInterpreter(manager, converter)

      infile = open(path, 'rb')
      for page in PDFPage.get_pages(infile, pagenums):
          interpreter.process_page(page)
      infile.close()
      converter.close()
      text = output.getvalue()
      output.close()
      return text


  text = convert_pdf_to_txt('test.pdf')
  ```

* **Reference**

  https://stackoverflow.com/questions/5725278/how-do-i-use-pdfminer-as-a-library

* **Ghost work**

  该方法只能是把 PDF 文档的文本全部提取出来，但不能保存文档的格式，也就难以进行更多深入的操作。
