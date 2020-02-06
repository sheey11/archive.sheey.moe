---
title: 学校的网课学习平台的文档？
date: 2020-02-06 18:47:11
update: 2020-02-06 18:47:11
---

# 前言
随着疫情的蔓延，各大学校都开始了网课 ~~（虽然四川教育厅不让开网课）~~ 这次的课程是学习冠状病毒相关的知识，我大概看了下看完了。准备写个自动完成的小东西，这里就把爬到的东西写成文档吧。

# 登陆
待完成。

# 获取课程列表
查询 `url` :
```
http://mooc1-2.chaoxing.com/visit/courses
```
登陆后通过该 `url` 查询课程。  
返回形式是 `html`, 实际上用正则表达式就可以很方便地查到每个课程的 `url`。

# 获取课程小节列表
访问每个课程的 `url` 可以得到每个课程小节的 `url`。
每个课程 `url` 内带有的信息有:  
|数据名称|描述|
|-|-|
|`chapterid`|课程小节唯一标识|
|`courseid`|课程唯一标识|
|`clazzid`|暂不明确|
|`enc`|暂不明确|  
在这个页面获取所有课程小节的 `chapterid` ，备用

# 自动完成课程小节

## 准备工作
访问课程小节页面，课程页面会有一个 `setlog` 请求资源，访问返回`'success'` 即可。意义不明，但应该是必须要访问。如：
```
https://fystat-ans.chaoxing.com/log/setlog?personid=12345678&courseId=12345678&classId=12345678&encode=12345678&chapterId=12345678
```
这个 `url` 带有未知的 `persionid` 和 `encode` 信息，只能通过爬取课程小节访问。

## 获取课程
`url` :
```
https://mooc1-2.chaoxing.com/mycourse/studentstudyAjax 
```
向改 `url` POST 发送数据，即可得到课程小节的 `html` 代码。

数据名称|对应的数据|备注
-|-|-
`courseId`|`courseid`|注意大小写
`clazzid`|`clazzid`|
`chapterId`|`chapterId`|
`cpi`|`97924359` 或 `0`|第一次前者，之后后者
`verificationcode` |`''`|一般是空字符串

## 信息获取
课程小节页面和用 POST 方式获取的页面都带有 `cards` 的请求，例如
```
https://mooc1-2.chaoxing.com/knowledge/cards?clazzid=12345678&courseid=12345678&knowledgeid=12345678&num=0&ut=s&cpi=0&v=20160407-1
```

用 GET 方式访问这个 `url` ，返回格式为 `json`，格式比较复杂。  
其中的重要数据有

|数据名称|描述|
-|-
`jobid`|小节的唯一标识
`otherInfo`|其他信息，以后会用到
`fid`|暂不明确，以后会用到
`userid`|用户的唯一标识
`reportUrl`|发送小节完成信息的 `url`
`clazzId`|暂不明确
`knowledgeid`|暂不明确
`objectId`|视频文件唯一标识，可能有多个

## 视频信息获取
### 合成文件查询 `url`

将 `objectId` 和 `fid` 和在一起，在前面加上 `url`: 
```
https://mooc1-2.chaoxing.com/ananas/status/
```
即可得到视频文件查询 `url`, GET访问得到的数据有

|数据名称|描述|
-|-
`dtoken`|发送视频完成信息的 `token`
`duration`|视频总时间
`download`|视频地址
`filename`|视频名称，没啥用
`objectId`|视频文件唯一标识
`key`|暂不明确

对于每个视频文件(`objectId`)，分别发送完成信息。

### 发送完成信息
#### 合成 `enc_pre`
`enc_pre` 的格式为 
```
[{0}][{1}][{2}][{3}][{4}][{5}][{6}][{7}]
```
每个大括号都需要填入数据：
|括号 ID|数据|描述|
-|-|-
{0}|`clazzid`|
{1}|`userid`|
{2}|`jobid`|
{3}|`objectId`|
{4}|视频总时长 * `100`|其实是当前视频时间
{5}|`d_yHJ!$pdA~5`|固定字符串
{6}|视频总时长 * `100`|
{7}|固定字符串 `0_ENDTIME`|其中`ENDTIME` 为视频时长秒数

对 `enc_pre` 进行 `md5` 加密得到 `enc`。


#### 合成完成信息 `url`
`url` = `reportUrl` + `dtoken`

#### 发送信息
向该 url 以 GET 方式发送以下信息：

|数据名称|值|描述
-|-|-
`clazzId`|`clazzId`
`playingTime`|总时长秒数|其实是当前时长秒数
`duration`|总时长秒数|
`clipTime`|固定字符串 `0_ENDTIME`|其中`ENDTIME` 为视频时长秒数
`objectId`|`objectId`|
`otherInfo`|`otherInfo`|
`jobid`|`jobid`|
`userid`|`userid`|
`isdrag`|`4`|已完成是 `4`
 `view`|`pc`|从 `pc` 上看
`enc`|`enc`|`md5` 加密后的 `enc_pre`
`rt`|`0.9`|不知道这是什么
`dtype`|`Video`|小节的 `type`，待补充
`_t`|当前时间戳|

当收到回复为
```json
{
    isPasse : true
}
```
即为课程完成。