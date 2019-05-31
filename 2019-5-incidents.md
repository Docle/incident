---
title: 记仇小本本
date: 2019-5-31
tags: [urlencode]

---

## 请求中带字典参数的编码

- **Date**: 2019-5-23

- **Problem**

  对 <https://etaxs.sn-n-tax.gov.cn/tycx-cjpt-web/view/sscx/gzcx/ybnsrzgcx/ybnsrzgcx.jsp?cdId=dlqcd-112&gnDm=gndm-5212&gdslxDm=3> 的查询请求抓包发现请求参数`bw`是字典。Chrome 的抓包中 General 部分如下：

  ```
  Request URL: https://etaxs.sn-n-tax.gov.cn/tycx-cjpt-web/cxptGz/tycxGzrkQuery.do?bw=%7B%22taxML%22:%7B%22head%22:%7B%22gid%22:%22311085A116185FEFE053C2000A0A5B63%22,%22sid%22:%22dzswj.gzcx.ybnsrzgcx%22,%22tid%22:%22+%22,%22version%22:%22%22%7D,%22body%22:%7B%22sbhmc%22:%229161010022060528X1%22,%22nsrsbh%22:%229161010022060528X1%22,%22nsrmc%22:%229161010022060528X1%22,%22shxydm%22:%229161010022060528X1%22,%22captcha%22:%22vdne3p%22%7D%7D%7D&gdslxDm=1
  Request Method: GET
  Status Code: 200 OK
  Remote Address: 127.0.0.1:1080
  Referrer Policy: no-referrer-when-downgrade
  ```

  Query String Parameters 部分如下：

  ```
  bw: {"taxML":{"head":{"gid":"311085A116185FEFE053C2000A0A5B63","sid":"dzswj.gzcx.ybnsrzgcx","tid":" ","version":""},"body":{"sbhmc":"9161010022060528X1","nsrsbh":"9161010022060528X1","nsrmc":"9161010022060528X1","shxydm":"9161010022060528X1","captcha":"vdne3p"}}}
  gdslxDm: 1
  ```

  requests 请求代码：

  ```python
  import requests
  
  url = 'https://etaxs.sn-n-tax.gov.cn/tycx-cjpt-web/cxptGz/tycxGzrkQuery.do'
  
  params = {
      "bw": {
          "taxML": {
              "head": {
                  "gid": "311085A116185FEFE053C2000A0A5B63",
                  "sid": "dzswj.gzcx.ybnsrzgcx",
                  "tid": " ",
                  "version": "",
              },
              "body": {
                  "sbhmc": '9161010022060528X1',
                  "nsrsbh": '9161010022060528X1',
                  "nsrmc": '9161010022060528X1',
                  "shxydm": '9161010022060528X1',
                  "captcha": 'captcha',
              }
          },
      },
      "gdslxDm": 1,
  }
  
  r = requests.get(url, params=params, verify=False)
  print(r.status_code)
  print(r.text)
  ```

  输出结果：

  ```
  404
  <html><head><title>Apache Tomcat/7.0.70 - Error report</title><style><!--H1 {font-family:Tahoma,Arial,sans-serif;color:white;background-color:#525D76;font-size:22px;} H2 {font-family:Tahoma,Arial,sans-serif;color:white;background-color:#525D76;font-size:16px;} H3 {font-family:Tahoma,Arial,sans-serif;color:white;background-color:#525D76;font-size:14px;} BODY {font-family:Tahoma,Arial,sans-serif;color:black;background-color:white;} B {font-family:Tahoma,Arial,sans-serif;color:white;background-color:#525D76;} P {font-family:Tahoma,Arial,sans-serif;background:white;color:black;font-size:12px;}A {color : black;}A.name {color :
  black;}HR {color : #525D76;}--></style> </head><body><h1>HTTP Status 404 - /tycx-cjpt-web/view/error/500.jsp</h1><HR size="1" noshade="noshade"><p><b>type</b> Status report</p><p><b>message</b> <u>/tycx-cjpt-web/view/error/500.jsp</u></p><p><b>description</b> <u>The requested resource is not available.</u></p><HR size="1" noshade="noshade"><h3>Apache Tomcat/7.0.70</h3></body></html>
  ```

- **Solution**

  对 params 做 urlencode

  ```python
  from urllib.parse import urlencode
  
  params = {
      "bw": {
          "taxML": {
              "head": {
                  "gid": "311085A116185FEFE053C2000A0A5B63",
                  "sid": "dzswj.gzcx.ybnsrzgcx",
                  "tid": " ",
                  "version": "",
              },
              "body": {
                  "sbhmc": '9161010022060528X1',
                  "nsrsbh": '9161010022060528X1',
                  "nsrmc": '9161010022060528X1',
                  "shxydm": '9161010022060528X1',
                  "captcha": 'captcha',
              }
          },
      },
      "gdslxDm": 1,
  }
  print(urlencode(params))
  ```

  结果如下：

  ```
  bw=%7B%27taxML%27%3A+%7B%27head%27%3A+%7B%27gid%27%3A+%27311085A116185FEFE053C2000A0A5B63%27%2C+%27sid%27%3A+%27dzswj.gzcx.ybnsrzgcx%27%2C+%27tid%27%3A+%27+%27%2C+%27version%27%3A+%27%27%7D%2C+%27body%27%3A+%7B%27sbhmc%27%3A+%279161010022060528X1%27%2C+%27nsrsbh%27%3A+%279161010022060528X1%27%2C+%27nsrmc%27%3A+%279161010022060528X1%27%2C+%27shxydm%27%3A+%279161010022060528X1%27%2C+%27captcha%27%3A+%27captcha%27%7D%7D%7D&gdslxDm=1
  ```

  与抓包的 Request URL 对比可以发现是不一样的(注：%3A+ 其实是:，这里说的不同不是指这两个的不同 )。

  ```
  Request URL: https://etaxs.sn-n-tax.gov.cn/tycx-cjpt-web/cxptGz/tycxGzrkQuery.do?bw=%7B%22taxML%22:%7B%22head%22:%7B%22gid%22:%22311085A116185FEFE053C2000A0A5B63%22,%22sid%22:%22dzswj.gzcx.ybnsrzgcx%22,%22tid%22:%22+%22,%22version%22:%22%22%7D,%22body%22:%7B%22sbhmc%22:%229161010022060528X1%22,%22nsrsbh%22:%229161010022060528X1%22,%22nsrmc%22:%229161010022060528X1%22,%22shxydm%22:%229161010022060528X1%22,%22captcha%22:%22vdne3p%22%7D%7D%7D&gdslxDm=1
  ```

  对于url来说，所有的键值对的值应该是字符串类型的，因此需要对 `bw` 的值做 json 序列化。

  ```python
  from urllib.parse import urlencode
  
  params = {
      "bw": {
          "taxML": {
              "head": {
                  "gid": "311085A116185FEFE053C2000A0A5B63",
                  "sid": "dzswj.gzcx.ybnsrzgcx",
                  "tid": " ",
                  "version": "",
              },
              "body": {
                  "sbhmc": '9161010022060528X1',
                  "nsrsbh": '9161010022060528X1',
                  "nsrmc": '9161010022060528X1',
                  "shxydm": '9161010022060528X1',
                  "captcha": 'captcha',
              }
          },
      },
      "gdslxDm": 1,
  }
  print(urlencode(params))
  ```

  先用json序列化字典再编码的params：

  ```
  bw=%7B%22taxML%22%3A+%7B%22head%22%3A+%7B%22gid%22%3A+%22311085A116185FEFE053C2000A0A5B63%22%2C+%22sid%22%3A+%22dzswj.gzcx.ybnsrzgcx%22%2C+%22tid%22%3A+%22+%22%2C+%22version%22%3A+%22%22%7D%2C+%22body%22%3A+%7B%22sbhmc%22%3A+%229161010022060528X1%22%2C+%22nsrsbh%22%3A+%229161010022060528X1%22%2C+%22nsrmc%22%3A+%229161010022060528X1%22%2C+%22shxydm%22%3A+%229161010022060528X1%22%2C+%22captcha%22%3A+%22captcha%22%7D%7D%7D&gdslxDm=1
  ```

  完整requests请求：

  ```python
  import requests
  import time
  import json
  
  url = 'https://etaxs.sn-n-tax.gov.cn/tycx-cjpt-web/cxptGz/tycxGzrkQuery.do'
  
  params = {
      "bw": json.dumps({
          "taxML": {
              "head": {
                  "gid": "311085A116185FEFE053C2000A0A5B63",
                  "sid": "dzswj.gzcx.ybnsrzgcx",
                  "tid": " ",
                  "version": "",
              },
              "body": {
                  "sbhmc": '9161010022060528X1',
                  "nsrsbh": '9161010022060528X1',
                  "nsrmc": '9161010022060528X1',
                  "shxydm": '9161010022060528X1',
                  "captcha": 'captcha',
              }
          },
      }),
      "gdslxDm": 1,
  }
  
  r = requests.get(url, params=params, verify=False)
  print(r.status_code)
  print(r.text)
  ```

  结果：

  ```
  200
  {"message":"验证码校验出错","code":"90"}
  ```

- **Reference**

  <https://blog.csdn.net/mouday/article/details/82658908>
