---
title: 关于汉诺塔
date: 2019-10-09 23:15:07
update: 2019-10-09 23:15:07
tags:
---
偶然翻到沙雕 Python 有上有个关于汉诺塔的问题，说是递归的经典案例，于是顿时来了兴趣。但是看了半天并没有发现什么规律可循，翻了翻国内的文章，少之又少的同时又讲不懂，无奈走 [Wikipedia](https://www.wikiwand.com/en/Tower_of_Hanoi#/Recursive_soluti字说明、step by step 的图和 py 代码，算是看懂了(吧)。
![图来自 Wikipedia](https://sheey-blog-resources.oss-cn-hangzhou.aliyuncs.com/images/Tower_of_Hanoi_recursion.png)
其实文字和图已经讲得比较详细了，但是我还是有没懂的地方。解题步骤是不断将 `source` 里的移到 `target` 里，但图里有一步是将 `source` 的一部分移到 `spare` 里，这就不是通解了呀（我认为还要将这一步独立出来写），我就一直没搞懂。  
但是翻下面代码，一下子就恍然大悟。Python 代码如下：  

```python
A = [3, 2, 1]
B = []
C = []

def move(n, source, target, auxiliary):
    if n > 0:
        # move n - 1 disks from source to auxiliary, so they are out of the way
        move(n - 1, source, auxiliary, target)

        # move the nth disk from source to target
        target.append(source.pop())

        # Display our progress
        print(A, B, C, '##############', sep = '\n')

        # move the n - 1 disks that we left on auxiliary onto target
        move(n - 1, auxiliary, target, source)

# initiate call from source A to target C with auxiliary B
move(3, A, C, B)
```
注意第 `13` 行，这里把 `auxiliary` 写到了参数 `source` 的位置，`target` 写到了参数 `auxiliary` 的位置，`source` 写在 `target` 的位置。这样就只需要把上面的 `spare` 看成 `target`，`target` 作为辅助盘就完美解决了。  
其实图上的步骤与给出的代码不一样，我对照图写出来的代码是这样的：
```python
def move(n, source, auxiliary, target):
    if n > 0:
        move(n - 1, source, target, auxiliary)
        target.append(source.pop())
        print('A = {}\nB = {}\nC = {}\n----------'.format(A,B,C))
        move(n - 1, auxiliary, source, target)

A = [5,4,3,2,1]
B = []
C = []
move(5,A,B,C)
```

以上。