---
title: 装 Arch Linux
date: 2020-10-05 10:34:11
update: 2020-10-05 10:34:11
---

这次不搞 WSL 了，搞真的。

<!--more-->

我是看着 Arch Linux 的 Installation Guide 装的，但是某些部分它不会详细讲，而是给你个链接让你自己看。众所周知 Lynx 其实是非常难用的，所以还是自己踩一遍坑自己记下来。

## 准备

去 [archlinux.org](https://archlinux.org/download/) 下安装镜像。用镜像启动到 Installation Media。  

## 分区

使用 `lsblk` 或者 `fdisk` 查看当前所有磁盘。

```sh
lsblk
# or
fdisk -l
```

找个要分区的磁盘，比如 `/dev/sda0`, 用 `fdisk` 分区。

```sh
fdisk /dev/sda0
```

进入 `fdisk`, 输入 `g` 选择 `GPT` 分区表，然后用 `n` 创建分区，注意需要一个 `EFI` 分区，一个或者多个 `Linux filesystem` 分区。当然内存不够可以整个交换分区，看 [Arch Linux Installation Guide](https://wiki.archlinux.org/index.php/Installation_guide#Partition_the_disks)。  
最后输入 `w` 保存。

### 格式化

对 `EFI` 分区格式化，UEFI 只支持 `FAT12/FAT16/FAT32` 格式[[1]][fat]。

```sh
mkfs.fat -F32 /dev/sda0p0
```

再格式化主分区：

```sh
mkfs.ext4 /dev/sda0p1
```

格式化好了就可以挂载啦，不过待会装软件还得先选镜像，先把镜像选了。

## 选择镜像

Arch 比较贴心，把所有国家的镜像都写在了 `/etc/pacman.d/mirrorlist` 里面，编辑之后保存就好。

```sh
vim /etc/pacman.d/mirrorlist
```

找到 `China`, 把下面 `Server` 的注释取消掉就好。  
这个文件也会被复制到新系统里，所以别乱搞哦。

## 开始装 Arch

先挂载主分区

```sh
mount /dev/sda0p1 /mnt
```

Arch 提供了一个安装 `packages` 到其他目录的工具：`pacstrap`.

```sh
pacstrap /mnt base linux-lts linux-firmware linux-lts-headers man-db man-pages
```

我这里选择了 `linux-lts`，有需要可以选 `linux`。

用 `arch-chroot` 将 `root` 目录改到 `/mnt` 下

```sh
arch-chroot /mnt
```

接着装点必要的软件。

```sh
pacman -Syyy bind vim git zsh openssh base-devel sudo
```

N卡显卡驱动，A卡不太清楚。

```sh
pacman -S nvidia-lts
```

再整点桌面环境

```sh
pacman -S gdm gnome gnome-terminal nautilus gnome-tweaks gnome-control-center adwaita-icon-theme arc-gtk-theme networkmanager

systemctl enable gdm
systemctl enable NetworkManager
```

### 创建用户

先给 `root` 上密码
```sh
passwd
```

在创建用户 `sheey`

```sh
useradd -m -g users -G wheel sheey
passwd sheey
```

在 `/etc/sudoers` 里给上 `wheel` 组的限权。

```sh
vim /etc/sudoers
# 把
# %wheel        ALL=(ALL)       ALL
# 这一行取消注释
```

### 生成 locale

编辑 `/etc/locale.gen`，`en_US` 和 `zh_CN` 任选，保证是 `UTF-8` 就好（不然 `gnome-terminal` 不给你启动）。

```sh
vim /etc/locale.gen
locale-gen
```

## 整点引导

先把 `EFI` 分区挂载上。

```sh
mkdir /boot/EFI
mount /dev/sda0p0 /boot/EFI
```

接着用 `fstab` 把当前挂载信息写到 `/etc/fstab` 里。

```sh
genfstab -U / >> /etc/fstab
```

安装 `grub`

```sh
pacman -S grub efibootmgr dosfstools os-prober mtools
```

写入引导，需要的话可以加上 `--removable` 选项[[2]][grub-removable]。

```sh
grub-install --target=x86_64-efi --bootloader-id=arch_linux --recheck
```

生成 `grub` 的配置文件：

```sh
grub-mkconfig -o /boot/grub/grub.cfg
```

## 完成

接着退出 `arch-chroot`，重启，在 `BIOS` 里选择这个磁盘启动，应该可以正常看到 `gdm` 让你输密码登陆了。

```sh
exit
reboot
```

## QFA

- Q: 我没装 `gnome-terminal` 咋办，就没法用终端了吗？  
  A: 按 `Alt Shift F4` 切到 `tty4` 之类的就可。

[fat]: https://wiki.archlinux.org/index.php/EFI_system_partition
[grub-removable]: https://wiki.archlinux.org/index.php/GRUB#Installation_2