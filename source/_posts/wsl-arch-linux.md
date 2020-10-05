---
title: 配置 WSL2 Arch Linux 桌面环境
date: 2020-10-05 10:34:11
update: 2020-10-05 10:34:11
---

## 安装

去[yuk7/ArchWSL2](http://github.com/yuk7/ArchWSL2)上下载最新的 Release，直接打开 `Arch2.exe` 安装。

> 如果 WSL 的版本默认是 `v1`，则需要转换为 WSL2.
>
> ```pwsh
> dism.exe /online /enable-feature /featurename:VirtualMachinePlatform /all /norestart
> # Restart computer
> wsl --set-default-version 2
> wsl --set-version Arch2 2
> ```

安装完成后可以用 `Windows Terminal` 或者用 `wsl` 命令登陆 `Arch`.


由于 `WSL2` 的 bug, 可能会出现  
```
The attempted operation is not supported for the type of object referenced.
```
的错误，只需要在 `pwsh` 下  
```pwsh
netsh winsock rest
```
就好（见 [microsoft/WSL#5351](https://github.com/microsoft/WSL/issues/5351)）。

## 配置

### 创建用户

首先创建一个账户，由于存在 bug 或其他的因素，导致用户无法访问自己的 `home` 目录，需要手动改一下权限。

```sh
$ useradd -d -g wheel sheey
$ chown sheey /home/sheey/
$ chgrp sheey /home/sheey/
$ passwd sheey
```

然后把 `wheel` 加入到 `sudoers` 里。

```sh
$ vim /etc/sudoers
```

### Shell 配置

接着安装 `zsh` 和 `oh-my-zsh`.

```sh
$ pacman -Syy zsh git
```

在安装 `oh-my-zsh` 之前，需要改一下 `hosts`，国内的 `raw.githubusercontent.com` 和 `raw.github.com` 的 DNS 已经被污染了，需要自己用在线的 ping 工具获取 ip，再写入 `/etc/hosts` 里。

安装 `oh-my-zsh`，因为 Arch 已经不提倡使用 `wget`，所以这里用 `curl`.
```sh
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
```

安装 `POWERLEVEL-10K`.

```sh
$ git clone --depth=1 https://gitee.com/romkatv/powerlevel10k.git ${ZSH_CUSTOM:-$HOME/.oh-my-zsh/custom}/themes/powerlevel10k
```

修改 `.zshrc`，放一个我自己的配置.
```
POWERLEVEL9K_LEFT_PROMPT_ELEMENTS=(os_icon user dir vcs)
POWERLEVEL9K_RIGHT_PROMPT_ELEMENTS=(status time)
POWERLEVEL9K_USER_DEFAULT_FOREGROUND="white"
POWERLEVEL9K_USER_DEFAULT_BACKGROUND="black"
POWERLEVEL9K_OS_ICON_BACKGROUND="black"
POWERLEVEL9K_OS_ICON_FOREGROUND="white"
POWERLEVEL9K_CONTEXT_DEFAULT_FOREGROUND="white"

ZSH_THEME="powerlevel10k/powerlevel10k"
```

## 桌面环境

这里我用的是 `gnome`，当然用 `xfce` 兼容性可能更好。

```sh
$ pacman -S gdm gnome gnome-terminal nautilus gnome-tweaks gnome-control-center gnome-backgrounds adwaita-icon-themes arc-gtk-theme gnome-session
$ systemctl enable gdm
```

接着在 Windows 上装好 [XLaunch](https://sourceforge.net/projects/xming/)，双击打开，`Display number` **一定要手动指定**，以及**必须钩上** `Disable access control`。

由于和 WSL1 不同，WSL2 运行在虚拟机里，所以和 Windows 的网络环境不一样，`X11` GUI 转发时需要填 Windows 的内网 ip.

这个内网 ip 是写在 `/etc/resolv.conf` 里的，所以我们直接拿出来。

```sh
$ echo "export DISPLAY=$(cat /etc/resolv.conf | grep nameserver | awk '{print $2}'):0" >> ~/.zshrc
```

以及其他的转发配置

```sh
$ echo "export LIBGL_ALWAYS_INDIRECT=1" >> ~/.zshrc
$ echo "export GDK_BACKEND=x11" >> ~/.zshrc
$ echo "export XDG_SESSION_TYPE=x11" >> ~/.zshrc
$ echo "export XKL_XMODMAP_DISABLE=1" >> ~/.zshrc
```

重启一下
```
$ reboot
```
> 重启之后可能会出现 `readonly-filesystem`，需要手动关闭 WSL, 在 pwsh 下
> ```pwsh
> wsl --shutdown Arch2
> ```
> 再重新连接 `Arch2` 即可。

打开 `gnome`:

```sh
$ gnome-session
```

多半在 XLaunch 里有画面了，但是说需要重新登陆。

我搜了半天也没找到和我类似的问题，可能确实是用 WSL2 + Arch + gnome 的太少了吧。

仔细研究了下，没在 log 里发现啥东西，想到按理说应该出现的是登陆界面而不直接是桌面，可能是 `gdm` 的问题，于是手动登陆。

```sh
$ login
$ gnome-session
```

遂成功。

## 用户的配置

接下来就简单多了，遇到的问题基本都是权限问题。

复制一份 `.zshrc` 给用户，修改下权限啥的。

```sh
$ cp ~/.zshrc /home/sheey/
$ chown sheey /home/sheey/.zshrc
$ chgrp sheey /home/sheey/.zshrc

$ cp -r ~/.oh-my-zsh /home/sheey/
$ chown sheey /home/sheey/.oh-my-zsh
$ chgrp sheey /home/sheey/.oh-my-zsh
```

切换到 `sheey`, 修改默认 `shell` 为 `zsh`.

```sh
$ su sheey
$ which zsh
/usr/bin/zsh
$ chsh /usr/bin/zsh
```

修改自己的 `.zshrc`, 把 `ZSH` 变量改成自己的目录 `/home/sheey/.oh-my-zsh`。  
接着直接进入 `zsh`。

```sh
$ exec zsh
```

## 其他问题

用 `sheey` 运行 gnome-session 依然是概率问题，大概率是失败，小概率成功。`gnome-session` 直接 `terminated`，问题在哪我也不知道，也没个 `log`，反正我是懒得搞了。

以上。