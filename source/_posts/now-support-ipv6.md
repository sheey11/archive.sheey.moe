---
title: 本 blog 现已加入 IPv6 豪华套餐
date: 2020-02-11 18:57:30
update: 2020-02-11 18:57:30
---

在阿里云申请到了 IPv6 的测试限权，给 blog 上了 IPv6。  
整个过程其实有点折腾，我服务器是 Ubuntu 18.04 的，但是我没注意阿里云文档上写的是 Ubuntu 14/16，饶了好多圈子。中间还去提交了工单。  
<!--more-->
因为 Ubuntu 从 17 开始网络配置工具就从原来的 `network service` 换成了 `netplan`, 而阿里云文档和自动配置工具还是针对的老版本，所以配置完全不起作用。  
配置过程是这样的。按照[手动配置文档](https://help.aliyun.com/document_detail/108465.html#title-pwe-jun-yin)，获取一些信息：  
> - IPv6 地址
> - IPv6 网关
> - IPv6 虚拟交换机 CIDR 地址段（其实不需要，默认是前 64 位）

把这些信息写进：`/etc/netplan/99-eth0.yaml`, 像这样：
```yaml
network:
  version: 2
  renderer: networkd
  ethernets:
    eth0:
      dhcp4: yes
      dhcp6: no
      addresses: ["YOUR_IPv6_ADDERSS/64"]
      gateway: GATEWAY
```
保存好，然后再
```sh
netplan apply
```
就大功告成啦！

以上。