---
title: 一周总结 - 离高考37天
date: 2019-05-01 01:01:00
update: 2019-05-01 01:01:00
tags: [一周总结, Linux, Aliyun, Minecraft]
---

这周学校没啥大事情要写的，就写 blog 相关的吧。

首先把图床换到了[阿里云 OSS](https://www.aliyun.com/product/oss)，由于流量费超低(0.08/GB)，而且价格<0.01时不计费，咱的小站访问量也很低（实际上根本没有访问量），所以就相当于只开了个储存包白嫖。  
买了个 [ECS](https://www.aliyun.com/product/ecs)，开 Minecraft 服务器玩。虽然咱的 Linux 非常渣，但是在各路大佬的帮助下，成功搭起了一个小服务器。  
之前完全没有注意到阿里云的云翼计划，无论是否完成学生认证，只要年龄在 `24` 以下，就可以享受白给带来的快乐。Mark 一下[云翼计划](https://promotion.aliyun.com/ntms/act/campus2018.html)。  
![薅羊毛](https://sheey-blog-resources.oss-cn-hangzhou.aliyuncs.com/images/aliyun-wool.png)

<!--more-->

## 如何搭一个 Minecraft 服务器
1. 首先得装 Java，然而 Oracle 似乎最近出台了一个规定说不允许用 Package Manager 装（似乎是这样的）。所以安装会有一点麻烦。  
   首先去 下载 JDK [最新的版本(目前是8u211)](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)。Oracle 现在下载都要求登陆了。于是咱们注册账号，验证邮件。选择对应版本，点击下载后可以复制链接然后到 ECS 里下载。
   ```bash
   wget DOWNLOAD_URL
   ```
2. 解压 Java。并将其移动到 `/usr/java/` 下。
   ```bash
   tar zxvf jdk-8u211-linux-x64.tar.gz # 解压 jdk
   mkdir /usr/java # 创建目录
   mv jdk1.8.0_211 /usr/java # 移动
   rm jdk-8u211-linux-x64.tar.gz # 删除压缩文件
   ```
3. 把 Java 和 Javac 添加到系统中去。  
   ```bash
   sudo update-alternatives --install /usr/bin/java java /usr/java/jdk1.8.0_211/bin/java 200 #添加 Java
   sudo update-alternatives --install /usr/bin/javac javac /usr/java/jdk1.8.0_211/bin/javac 200 #添加 Javac
   ```
4. 测试一下。  
   ```bash
   java -version
   ```
5. 把 Minecraft 服务端下载下来。  
   ```bash
   wget -O mc-server-1.11.2.jar https://launcher.mojang.com/v1/objects/f00c294a1576e03fddcac777c3cf4c7d404c4ba4/server.jar
   ```
6. 配置一堆东西。首先运行一次服务端，会生成一个 `eula.txt` 文件。  
   ```bash
   java -Xmx1024M -Xms1024M -jar mc-server-1.11.2.jar
   ```
   他会告诉你启动失败，你得先同意他的 `EULA`。我们打开 `eula.txt`，将其中的 `eula=false` 改为`eula=true`。当然你也可以在运行之前先创建一个 `eula.txt`，同意后再运行服务端。
   ```bash
   vim eula.txt
   ```
   随后再次运行服务端，会生成一个新的世界，以及目录下的配置文件 `server.properties`， 打开，修改想改的属性，如 `max-players`, `motd` 等。
7. 导入地图。我们将准备好的地图扔到与 `server.jar` 同目录下，修改 `server.properties` 中的 `level-name`，使其与文件夹名称一致。运行服务器，登陆一次，在 `/world/playerdata` 下会生成我们角色的数据文件，我们只需要将准备好的地图文件中的角色数据改成相同名称，覆盖即可。
8. 运行服务端。  
   ```bash
   java -Xmx1024M -Xms1024M -jar mc-server-1.11.2.jar &
   exit
   ```
   随后关闭 ssh 连接即可。当然也可以使用 `screen` 之类的命令将终端丢到后台运行。

以上。