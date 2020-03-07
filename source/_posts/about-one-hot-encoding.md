---
title: 关于 One Hot 编码
date: 2020-03-01 10:34:11
update: 2020-03-07 12:10:11
tags: [机器学习]
---

总爱忘事，放着自己忘了可以看看。
<!--more-->

## 什么是 One-Hot
在离散数据处理中，一般用数字代表该值的类别，形成一种 mapping 关系，如：
```json
{
    "中国" : 0,
    "美国" : 1,
    "日本" : 2,
    "俄罗斯" : 3
}
```
如果这里有三个数据，分别是 `中国`、`美国` 和 `俄罗斯`，那么在训练过程中，`中国` 与 `美国` 的距离 $d_1$ 为
$$ d_1 = 1 - 0 = 1 $$
`中国` 与 `俄罗斯` 的距离 $d_2$ 为
$$ d_2 = 3 - 0 = 3 $$
这样计算下来，似乎 `中国` 与 `俄罗斯` 的差距比与 `美国` 的大的多，然而实际情况是这组离散数据各自与各自的差距应当相等。所以就有了 One Hot 编码。

对于 $k$ 种类别，我们定义一个 $k$ 维向量 $V$，对于每个分类 $i$，在 $V_i$ 的位置上为 $1$，其他位置为 $0$。如
$$ V_{中国} = \begin{bmatrix} 1 \\ 0 \\ 0 \\ 0 \end{bmatrix} $$
$$ V_{美国} = \begin{bmatrix} 0 \\ 1 \\ 0 \\ 0 \end{bmatrix} $$
$$ V_{日本} = \begin{bmatrix} 0 \\ 0 \\ 1 \\ 0 \end{bmatrix} $$
$$ V_{俄罗斯} = \begin{bmatrix} 0 \\ 0 \\ 0 \\ 1 \end{bmatrix} $$

这样，`中国` 与 `美国`、`中国` 与 `俄罗斯` 的差距都会是

$$ |\overrightarrow{V_{中国}V_{美国}}| = |\overrightarrow{V_{中国}V_{俄罗斯}}| = \sqrt{1^2 + (-1)^2} = \sqrt{2} $$

就显得合理的多。

## TensorFlow 实现
`tf` 已经给出了[相应的实现](https://www.tensorflow.org/api_docs/python/tf/one_hot)
```
tf.ont_hot(
    indices, depth, on_value=None, off_value=None, axis=None, dtype=None, name=None
)
```
`indices` 是一个 多维矩阵，`depth` 是分类的个数。  
`one_value` 是 $V_i$ 所对应的值，默认值是 $1$，`off_value` 是其他位置所对应的值，默认值是 $0$。  
`dtype` 是输出向量里值的类型，且 `one_value` 和 `off_value` 也必须是 `dtype` 的类型。

以上。