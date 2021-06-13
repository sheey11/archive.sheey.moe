---
title: 国内垃圾网络环境的究极解决办法
date: 2021-06-13 20:19:53
update: 2021-06-13 20:19:53
tags: Proxy
---

众所周知，国内的网络环境比朝鲜还差，有些程序自带代理设置，就可以很好的走`7890`端口，然而有些莫名其妙的东西要么不设置代理，要么设置了没用，比如 Discord，跟一坨屎一样。

于是 [Proxifier](https://www.proxifier.com/) 出现了，刚才对他的印象还是在“给某个程序强制走代理”的工具。最近突然想起它还可以设置网站，这就好办了，给 `github`、`golang.org` 设置上代理，傻逼的 `go` 就会自己走代理。

## 使用方法

1. 先在 `Profile` -> `Proxy Servers` 里加上自己的代理服务器：
  ![Proxy Servers](https://sheey-blog-resources.oss-cn-hangzhou.aliyuncs.com/images/proxy-servers.png)
2. 在 `Profile` -> `Proxification Rules` 里给上傻逼的程序名、傻逼的域名、傻逼的端口号。
  ![Proxification Rules](https://sheey-blog-resources.oss-cn-hangzhou.aliyuncs.com/images/proxificaiton-rules.png)
3. 点 `OK`, 静候其变。
