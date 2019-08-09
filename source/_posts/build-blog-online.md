---
title: 在服务器上持续化 build blog
date: 2019-08-09 21:01:00
update: 2019-08-09 21:01:00
tags: blog
---

最近本地更新了[关于](https://sheey.moe/about)页面，也 push 到了 github, 但是发现网站上并没有更新，于是 ssh 连上服务器发现是 `caddy-git` 的问题。
`hexo g -d` 实际上是用 `git push --force` 在推送，而因为是新电脑，没有 clone 一遍就直接 push，导致 HEAD 对不上，所以服务器这边 pull 自然会出错。
仔细研究了下 `caddy-git` 之后，发现他其实可以在 pull 之后执行命令，那么直接把 build 需要的文件放 github, 让服务器来构建岂不是一举两得。

<!-- more -->

---

在 `caddy-git` 的 [repo](https://github.com/abiosoft/caddy-git) 上可以看到 `caddy-git` 的语法是这样的：  
> ```json
git [repo path] {
	repo        repo
	path        path
	branch      branch
	key         key
	interval    interval
	clone_args  args
	pull_args   args
	hook        path secret
	hook_type   type
	then        command [args...]
	then_long   command [args...]
}
```
其中  
> - **path** is the path to clone the repository into; default is site root. It can be absolute or relative (to site root).
> - **command** is a command to execute after successful pull; followed by **args** which are any arguments to pass to the command. You can have multiple lines of this for multiple commands. **then_long** is for long executing commands that should run in background.

所以我们只需要直接用 `then` 执行 `hexo g` 就好啦。

---
首先修改网站的根目录，修改为 `sheey.moe/public`。  
暂时将 pull 下来的文件放到 `tmp` 下，之后在复制过去。  
将 `Caddyfile` 里的 git 部分按需要修改。我的是这样哒：
```
git {
    repo https://github.com/Sheey11/sheey.moe.git
    path ../tmp
    pull_args --force
    branch master
    interval 300
    then sh var/www/sheey.moe/build.sh
}
```
其中 `build.sh` 的作用是将 `source` 文件夹删除，随后从 `tmp` 下复制过去。
```
cd /var/www/sheey.moe
rm -r source
cp -f -r tmp/* .
hexo g
```
---
接着可以开始准备 repo 啦。由于 `npm_modules` 文件夹的特殊性，我就选择只在 github 上存放 `source` 以及 `_config.yml` 几个文件。把他们复制过去后再 `hexo g`。  
将需要 build 的文件放到 github 上去！在 `blog-source` 下：
```shell
git init
git remote add origin https://github.com/Sheey11/sheey.moe.git
```
添加需要忽略的文件夹到 `.gitignore`。随后 push 并且覆盖。
```sheel
git add .
git commit -m "init"
git push -u -f origin master
```
再在服务器上装一次 hexo 就行啦。（我为了方便，把 `package.json` 和其他一些东西直接复制过去了，然后 `npm i`）

---

最后，在 `Caddyfile` 目录下运行 `caddy` ，没有问题就可以换服务运行啦！

以上。