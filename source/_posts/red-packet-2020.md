---
title: 我也来发红包啦！
date: 2020-02-17 12:00:00
update: 2020-02-18 12:00:00
---

# 前言
之前在 GitHub 上看到了 Soha 的红包，后来又找到了好多大佬发的红包，我也来凑个热闹。虽然春节早过了，但是现在宅在家里闲着也是闲着，不如发个红包。

<!--moer-->

# 规则
**目前解析已公布，欢迎提出意见和建议**  

~~从 [Soha](https://soha.moe/post/find-soha-red-packet-2020.html) 和 [Tony](https://www.ouorz.com/594) 那里复制过来的，改了一点点~~
1. 这是一个 CTF 闯关游戏，利用你的知识（现学也是可以的啦）解决所有题目，获得红包口令，使用口令进入支付宝领取红包。
2. 整个游戏一共 4 关，每闯过两关会得到一个红包密码，即在第 3 关、解开第 4 关的谜题后各得到一个红包密码。 每一关都有通往下一关的线索。
3. 你可能需要使用电脑，且熟悉对图片的一些编辑操作。
4. 请勿将解题方法、思路和答案泄露给他人。
5. 那么，从这里开始吧 → https://hb.sheey.moe

# 领取须知
把得到红包密码前面加上 `不过如此` 即可得到红包口令，如密码为 `123456789`, 即使用口令 `不过如此123456789` 领取红包。  
领取红包的方法见 → [🔗此处链接](https://jingyan.baidu.com/article/5553fa8288eb6c65a239340f.html)

第一个红包 (第三关): 共 5 个，5.00 元。  
第二个红包 (第五关): 共 3 个，10.00 元。

开始时间：2020 年 2 月 17 日 12:00:00  
结束时间：2020 年 2 月 18 日 12:00:00

结束之后会在这里发布答案和解析！

祝大家好运~

# 答案与解析

截止到目前，共有 3 人领取了红包。  
第一个红包被领走 2 个，最早在 `14:44:29` 被领走（其实还应该更早）。  
第二个红包被领走 1 个，在 `17:21:54` 被领走。

## 第一关
首先我们打开 https://hb.sheey.moe，作者让我们输入白嫖密码，接着来到了一个非常诡异的页面。  
![第一关](https://sheey-blog-resources.oss-cn-hangzhou.aliyuncs.com/images/lvl-1-sc.png)  
果断按下 F12，切换到控制台，看见了非常明显的提示。  
```
Encrypted data:
	[ggcf-""[U!f[XXl!`bX":Z]Kh%X>cd>6I&\%![g`_.
```
点击右边的 `decrypt.mim.js:1` 来到 `js` 的源码，可以看到开头就是一个 `decrypt` 函数。~~这作者真尼玛贴心~~  
解码后，得到了下一关的 `URL`：
```
https://hb.sheey.moe/GgjXu2eKpqKCV3i2.html
```
## 第二关
![第二关](https://sheey-blog-resources.oss-cn-hangzhou.aliyuncs.com/images/lvl-2-sc.png)

嗯，没啥问题，打开查看线索。

![线索](https://sheey-blog-resources.oss-cn-hangzhou.aliyuncs.com/images/clue-sc.png)  
其实这里并没有线索，只是另一个障眼法。  
回到刚才的页面，按 F12 打开控制台，在仔细找了一遍 控制台、源码、引用的资源后，没有发现什么明显的线索。  
抓包看看？  
按下 F5 刷新网页，仔细寻找一下获取到的信息，不难看出返回的 `header` 里面有这样一条：
```
locatiom: https://hb.sheey.moe/WHtPJRko294ndUvh.json
```
![抓到的包](https://sheey-blog-resources.oss-cn-hangzhou.aliyuncs.com/images/lvl-2-capture.png)

这就是真正线索啦！

访问网页，得到一串 `json` 信息(`data` 被精简掉了）：

```json
{
    "msg": "这里才是真正的线索哒 JOJO",
    "format":"square",
    "data":"101001011010001011011111011011001101"
}
```

注意到 `data` 是一串二进制数据，而 `format` 提示这串数据是 `square` 方形，而这段数据的长度刚好是 `31 ^ 2 = 961`，所以这串数据应该是按每 `31` 个字符为一行，形成 `31 x 31` 的方形。  
联想到*规则*里说到需要编辑图片，不难想到这个数据是一个图片，每个字符代表一个像素，我们把它画出来，得到一个二维码：  
![二维码](https://sheey-blog-resources.oss-cn-hangzhou.aliyuncs.com/images/lvl-2-qrcode.png)

扫出来得到的就是下一关的网址。  
```
https://hb.sheey.moe/df6511d788004e4b.html
```

## 第三关

![第三关](https://sheey-blog-resources.oss-cn-hangzhou.aliyuncs.com/images/lvl-3-sc.png)

其实这里题出的不是很好，我参考了 [Nazo game](http://nazo.one-story.cn/)，解密方法是手机的拨号键盘，按照给出的提示把对于的拨号数字点出来就得到了下一关的网址。

但是其实很多人是把路径连起来，这样就完全看不出来是什么了，这一关是我出题出的不好，下次会注意的。

画图得到了一个字符串：
```
TqPU.html
```
联合之前的网址，可以猜出来下一关的网址是
```
https://hb.sheey.moe/TqPU.html
```

## 第四关 - 最后一关
访问这个网址，服务器给我们下载了一个名为 `telnet.txt` 的文件，内容是：  
```
欢迎来到最后一关！
祝大家2020 新年快乐，大家有没有回顾过去的一年呢？
再告诉你一个秘密：6I341,'/VX7!4=D02
```
这“秘密”四不像的，肯定有鬼。

注意到文件名是 `telnet.txt`，那么这关肯定要用到 `telnet` 工具啦。我们先试试连接 `23` 端口。  
![连接 23 端口](https://sheey-blog-resources.oss-cn-hangzhou.aliyuncs.com/images/lvl-4-telnet-1.png)

嗯，看样子不让连，试着找找其他端口呢。~~有一位兄弟直接端口扫描了~~

注意到 `telnet.txt` 里有 `2020`, `6`, `34`, `7`, `4`, `02` 几个数字，首先排除一位数的，`34` 也连不上，于是咱们连 `2020`:  

![2020 就是我们要找的端口](https://sheey-blog-resources.oss-cn-hangzhou.aliyuncs.com/images/lvl-4-telnet-2.png)

看样子成功了，我们输入 `telnet.txt` 中的密码：
```
 > Please enter the password: 6I341,'/VX7!4=D02
 > Sorry, wrong password.
```

看来给到的并不是真正的密码。注意到  `telnet.txt` 中有这么一句话:
```
祝大家2020 新年快乐，大家有没有回顾过去的一年呢？
```
这里的 `2020` 指的是端口号，那后面那句话的意思肯定就没有那么单纯了。

我们回到第一关，~~输入白嫖密码~~，用 `decrypt` 解密密码。
```
 > decrypt('6I341,\'/VX7!4=D02')
 > "GZDEB=8@giH2ENUA"
```
这就是我们要的密码啦，最后一关顺利通过。

![](https://sheey-blog-resources.oss-cn-hangzhou.aliyuncs.com/images/lvl-4-telnet-3.png)

# 小插曲
1. 最开始二维码 `data` 数据有错，第一个其实应该更早的时候就应该被领了。
2. 手机键盘实在是没考虑周全，向各位选手道个歉。
3. 完全不知道 Windows 上的 `telnet` 是发送单个字符的，而 `Unix` 是发送一行，所以在 `17:30` 左右紧急修复了这个问题。

# 其他信息
服务端已经放在了 GitHub 上，在[这里](https://github.com/Sheey11/hongbao-2020)，有兴趣的可以去看看，~~点个 star 什么的~~  
明年~~应该~~还会继续出谜题的~~吧~~。

祝大家 2020 ~~新年~~快乐~