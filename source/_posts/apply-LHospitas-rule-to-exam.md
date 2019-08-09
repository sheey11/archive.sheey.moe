---
title: 洛必达法则在高中数学导数题中的应用
date: 2019-05-11 18:46:20
update: 2019-05-11 18:46:20
tags: [学习, 数学, 笔记]
---
来看一道高考题(2010 全国新标卷):  
> 设函数 $f(x)=e^x-1-x-ax^2$  
> (1) 若 $a=0$，求 $f(x)$ 的单调区间;  
> (2) 当 $x \geqslant 0$ 时 $f(x) \geqslant 0$, 求 $a$ 的取值范围。

<!--more-->

直接看第(2)问，我们分离参数得:  
$$a \leqslant \frac{e^x-x-1}{x^2}$$
设 $g(x)$:
$$g(x) = \frac{e^x-x-1}{x^2}$$
对 $g(x)$ 求导，可得:
$$g'(x)=\frac{x^2(e^x-1)-2x(e^x-x-1)}{x^4}$$
令分子等于 $0$，解得:
$$x=0$$
但是当我们把 $x=0$ 代入 $g(x)$ 得:
$$g(0) = \frac{0}{0}$$
这显然不存在。但是这时可以用洛必达法则直接求出 $a$ 的取值范围。根据洛必达法则:
$$\begin{aligned} 

    \lim_{x \to 0^+}g(x) & = \lim_{x \to 0^+}\frac{e^x-x-1}{x^2} \\
     & = \lim_{x \to 0^+}\frac{(e^x-x-1)'}{(x^2)'} \\
     & = \lim_{x \to 0^+}\frac{e^x-1}{2x} \\
     & = \lim_{x \to 0^+}\frac{e^x}{2} \\
     & = \frac{1}{2}

\end{aligned}
$$
所以
$$a \leqslant \frac{1}{2}$$
**注意**：只有当 $g(x_0)=\frac{0}{0}$ 或 $g(x_0)=\frac{\infin}{\infin}$ 时，才能使用洛必达法则。

再来看一道题：  
> $$kx\geqslant \frac{\sin x}{2+\cos x} (x>0)$$
分参得：
$$k\geqslant \frac{\sin x}{x(2+\cos x)}$$
设 $g(x)$:
$$g(x) = \frac{\sin x}{x(2+\cos x)}$$
求导:
$$g'(x) = \frac{x-2 \sin x-(\sin x-2 x) \cos x}{x^2 (\cos (x)+2)^2}$$
令 $0$, 解得:
$$x = 0$$
代入得:
$$g(0) = \frac{0}{0}$$
由洛必达法则:
$$
\begin{aligned}
\lim_{x \to 0} g(x) & = \lim_{x \to 0} \frac{\sin x}{x(2+\cos x)} \\
    & = \frac{\cos x}{-x \sin x+\cos x+2} \\
    & = \frac{1}{3}
\end{aligned}
$$
故
$$ k \leqslant \frac{1}{3} $$

以上。

---

悄悄放首歌。  
{% aplayer "渡月橋 ~君 想ふ~" "倉木麻衣" "https://sheey.moe/res/music/songs/渡月橋_君想ふ-倉木麻衣.mp3" "https://sheey.moe/res/music/covers/渡月橋_君想ふ-倉木麻衣.jpg" "lrc:https://sheey.moe/res/music/lyrics/%E6%B8%A1%E6%9C%88%E6%A9%8B_%E5%90%9B%E6%83%B3%E3%81%B5-%E5%80%89%E6%9C%A8%E9%BA%BB%E8%A1%A3.lrc" %}

<!-- {% aplayerlist %}
{
    "autoplay": false,  
    "mutex": true,
    "theme": "#ff7a9e",
    "music": [
        {
            "title": "渡月橋 ~君 想ふ~",
            "author": "倉木麻衣",
            "url": "https://sheey.moe/res/music/songs/渡月橋_君想ふ-倉木麻衣.mp3",
            "pic": "https://sheey.moe/res/music/covers/渡月橋_君想ふ-倉木麻衣.jpg",
            "lrc": "https://sheey.moe/res/music/lyrics/%E6%B8%A1%E6%9C%88%E6%A9%8B_%E5%90%9B%E6%83%B3%E3%81%B5_-%E5%80%89%E6%9C%A8%E9%BA%BB%E8%A1%A3.lrc"
        },
        {
            "title": "ebb and flow",
            "author": "Ray",
            "url": "https://sheey.moe/res/music/songs/ebb_and_flow-Ray.mp3",
            "pic": "http://p2.music.126.net/U-wSGCPWS1Qj5KhSj0rZog==/109951163534517211.jpg?param=130y130",
            "lrc": "https://sheey.moe/res/music/lyrics/ebb_and_flow-Ray.lrc"
        }
    ]
}
{% endaplayerlist %} -->