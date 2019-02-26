---
title: 记仇小本本
date: 2019-2-26
update: 2019-2-26
tags: [Requests]

---

## Requests post 请求无法被服务器解析

- **Date**: 2019-2-26

- **Problem**

  在请求 https://12366.xj-n-tax.gov.cn/yhs-web/cxzx/index.html#/zdsswfajgg?code=zdsswfajgg&id=606 的时候原始代码如下：

  ```python
  headers = {
      'Host': '12366.xj-n-tax.gov.cn',
      'Accept': '*/*',
      'Accept-Encoding': 'gzip, deflate,br',
      'Accept-Language': 'zh-CN,zh;q=0.9',
      'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.109 Safari/537.36',
  	'Content-Type': 'application/json;charset=UTF-8',
  	'Content-Length': '80',
  	'X-Requested-With': 'XMLHttpRequest',
      'Proxy-Connection': 'keep-alive',
  	'Referer': 'https://12366.xj-n-tax.gov.cn/yhs-web/cxzx/index.html'
  }
  url = 'https://12366.xj-n-tax.gov.cn/yhs-web/api/zdwfaj/ajlbcx'
  data = {
  	"nsrsbh": "",
  	"nsrmc": "",
  	"fddbrxm": "",
  	"pageSize": 10,
  	"sswfrlx": "00",
  	"pageIndex": 1,
  }
  r = requests.post(url, data=data, headers=headers, verify=False)
  print(r.text)
  ```

  打印信息如下：

  ```
  {"message":"系统出现故障，要稍后再发送","value":null,"success":false,"msgCode":"9001","resultMap":{},"msgParams":[],"jylsh":"9a15071f0d81490dad0510442cfcffa6"}
  ```

  即使添加了 cookies 也无法得到数据。

- **Solution**

  请求头中字段 `Content-Type` 为 `application/json`，表示请求中的消息主体以 json 串提交，故需要对 `data` 进行 json 编码再发送请求，

  ```python
  data = json.dumps(data)
  ```

- **Reference**

  https://zhuanlan.zhihu.com/p/37105991
