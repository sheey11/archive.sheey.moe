---
title: 写了个通知新评论的小工具
date: 2020-03-16 10:34:11
update: 2020-03-16 10:34:11
tags: [前端, Python]
---


# 前言
为啥要写这东西？Well, Valine Admin 并不能做到 24 小时的监控，很多人的评论甚至要一天甚至一个星期之后才能收到通知邮件，而 Leancloud 的实例又很贵（1元/天，我有这闲钱还不如去买加速器），而且因为 Blog VPS 还有空闲资源，所以不如自己检测咯。

# Valine Checker 
写这玩意首先就要方便，所以自然而然的选了 python 来写。鉴于 leancloud sdk 也完善的差不多了，所以写起来很方便。除了发邮件的模块费了一点时间，其他进展都算挺快的。  
然后我就想到了异步，没想多的就是想玩玩，然后把日志记录写成了 `async`，接着 `await` 和 `async` 满天飞，然而这个小东西其实并没有什么真正需要异步的（现在已经去掉了）。

## 版本兼容问题
Leancloud sdk 没有写清楚依赖库 `Werkzeug` 的版本，而他已经更新到了 `1.0+` 的版本，许多函数被移到了其他的 `namespace` 就和 leancloud 不兼容。我当时还挺气的，去找了 sdk 的 git 仓库，结果发现连 travis 都没通过？？？我他吗直接疑惑，当即就提了个 [issue](https://github.com/leancloud/python-sdk/issues/443)。  
接着再去看了看 travis 的详情，看了下前几个版本 python3 是通过的，应该是支持 python3 的，接着跟着报错详情找到了 `Werkzeug` 这个库，在 `pypi` 上找到了他的详情，发现他是最近才更新 `1.0` 版本的所以我觉得这是不是版本问题。所以跟着 travis 的 build 日期找到了那时最新的 `0.16.1` 版本。装上就完美解决啦~

## 网络问题
好的，一切测试都没啥问题，放服务器上试试呢。  
于是 ssh 连上服务器，一顿操作没啥问题。创建服务，不管它啦~  
随后问题就来了，大概运行了 3 天之后，连上去发现服务 failure 了，日志也没记录，妈耶。于是直接给 `main` 函数加上了 `try` `catch`，再来三天。  
> A thround years later.jpg
日志可算有点用了，他大概说
```
Connection reset by peer
```
行吧，看日志上文，这还在等待状态。我再次疑惑，又去提了个 issue: [关于长连接(>3天)](https://github.com/leancloud/python-sdk/issues/445)，官方给出的答案是
> > ConnectionResetError: [Errno 104] Connection reset by peer
> 
> 从这个报错来看应该是网络断了。  
> Python SDK 并没有长连接机制，比如 obj.save() 就是发一个 POST 的 HTTP 请求，遇到这类网络错误也不会自动重连。  
> 如果想要重试，可以自行 except 相关的异常，然后重试失败的操作。

没有搞懂是网络问题还是 leancloud sdk 代码的问题，按理说服务器的网络应该是不太会断的。所以直接暴力地给整个代码套上了一层 `while True`。  
想到还可能会出错，也方便删日志和管理评论，就有了 [valine checker admin](https://github.com/Sheey11/valine-checker-admin)。

# Valine Checker Admin
[Valine Checker](https://github.com/Sheey11/valine-checker) 是用来管理评论的，而 [Valine Checker Admin](https://github.com/Sheey11/valine-checker-admin) 就是用来管理 Valine Checker 的。（？禁止套娃）  
和 Valine Admin 一样，可以对评论进行标记或者删除。除此之外，也可以查看日志，启动或者停止 Valine Checker 之类的。  
因为很早就想学学 Vue（的皮毛），所以自然就用了 Vue 来写，然后因为我太懒了，并没有用 nodejs 重写 valine checker。  
用了一套 [Zeit-UI](https://vue.zeit-ui.co/zh-cn/button)（从 [Tony](https://www.ouorz.com/post/617) 那里看到的），接着是折腾 vue-cli，大概弄懂怎么玩了之后就开写啦。

## 和 express 的交互
起先两个东西运行在不同的端口上，跨域就很是问题，之前没有了解过这类东西，于是花了比较多的时间解决跨域问题。对 express 设置 `Access-Origin-Control`。

## 路由问题
照着 `vue-cli` 的来，我装了最轻量的 vue 框架，结果没装路由（对前端我真的是一窍不通）。找了下 vue-router 的文档，没找到咋装（其实现在看来首页就写的有），于是用 vue 重新生成了个项目😂。

## js 问题
js 的弱类型语法写着是爽，但是缩进问题感觉比 python 还严重，各种回调，vue 的 `methods` 挤在一堆，`data`、`methods`、`mounted` 甚至 `computed` 挤在一堆看起来不太舒服。以后还是得分组件写。

## 防注入
这个问题我也不知道我是怎么想到的。有一天晚上，我躺床上刷小黑盒，然后这个问题就突然 occurred to me。日志读取和删除操作的 api 参数是日志的文件名，如果捏造一个请求修改文件名就可以对其他文件和文件夹进行操作，所以第二天起来就加了个验证。

# 最后
感觉代码还是写的太粗糙了。不过算了，先点个 [star](https://github.com/Sheey11/valine-checker-admin) 吧~

以上。