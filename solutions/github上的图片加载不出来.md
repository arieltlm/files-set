# github上的图片加载不出来

[toc]

## 1.法一：修改DNS

[从根源上解决github图片显示不出来的问题](https://blog.csdn.net/lr157/article/details/105665798/)

DNS中添加google的DNS服务器`8.8.8.8; 8.8.4.4`

**【实践结果】**：加载出来了一部分图片

【副作用】：chrome打开网页特别慢，知乎上的图片都加载不出来了。

此法不可取

## 2.法二：解决github中图片不显示的问题

hosts中增加github的：

```
# GitHub Start
192.30.253.112    Build software better, together
192.30.253.119     gist.github.com
151.101.184.133     assets-cdn.github.com
151.101.184.133     raw.githubusercontent.com
151.101.184.133     gist.githubusercontent.com
151.101.184.133     cloud.githubusercontent.com
151.101.184.133     camo.githubusercontent.com
151.101.184.133     avatars0.githubusercontent.com
151.101.184.133     avatars1.githubusercontent.com
151.101.184.133     avatars2.githubusercontent.com
151.101.184.133     avatars3.githubusercontent.com
151.101.184.133    avatars4.githubusercontent.com
151.101.184.133     avatars5.githubusercontent.com
151.101.184.133     avatars6.githubusercontent.com
151.101.184.133     avatars7.githubusercontent.com
151.101.184.133     avatars8.githubusercontent.com
# GitHub End
```

据法一中的分析，这个域名配置可能变，所以不稳定

**【实践结果】** ：此法好使

## 3.法三：加ip域名

[Github网页上图片加载不出来](https://blog.csdn.net/qq_33126131/article/details/106228628)

复制 `raw.githubusercontent.com` 

去 [https://www.ipaddress.com](https://www.ipaddress.com/) 搜索,把给出的IP地址存储到 **host** 文件中:

如 `199.232.28.133 raw.githubusercontent.com`

**【实践结果】:**并不起作用

## 4.法四：翻墙

## 5.法五：去gitee.com（码云浏览）