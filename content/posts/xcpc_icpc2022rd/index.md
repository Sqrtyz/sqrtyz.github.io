---
title: 【算法竞赛】ICPC 2022 Regional Dairy
category: XCPC
date: 2023-01-01
---

### 流水账

进入大学以来的第一场 ICPC。前一天两点钟才睡觉，因为在捣鼓这个博客。

开局发现一分钟内 D 题过了快有一百来个，顿时人都麻了。我寻思题面第一页不是写了 Do not open before the contest has started 吗？

签掉之后看了下榜，发现 C 和 F 有过。于是先想了下 C，感觉 $n \leq 5000$ 怎么乱搞都行，然后就过了。

这个时候队友说 A 有思路了，于是乎上位敲 A。然后我去看 F。

F 是一道构造题，想了一下转化成一维的就很简单了，但是队友这个时候还在锤 A。于是去看了下有一两个人过的 E 题（埋下祸根）

E 题想到缩点后就不怎么会了。开始以为是在叶子节点之间建生成树然后生成树计数，我寻思这玩意儿可做吗？

这个时候队友过了 A 样例了，好家伙交上去格式错误？

去了个结尾换行再交，还是格式错误。

这个时候我突然意识到多半是傻逼 PTA 把 SPJ 给的错误反馈认为是格式错误了，于是让队友看一下精度问题。队友手玩了几个数据感觉没啥问题。中途我发现一堆人过了炉石题，看了下题鉴定为大爆搜。

于是我先上机把 F 过了（这个时候 penalty 已经爆了），然后继续写炉石题，结果居然爆搜挂了？

调了半天没调出来，当时人已经有点不好了。这个时候队友提出要测一组 A 的手造样例，好家伙输出 0。

然后让队友来调 A，我同时手玩一下炉石题的样例。

然后队友调了半天终于不输出 0 了，但是好像输出的答案误差有点大（精度爆了），大概到了 1e-3 的级别，但题目是 1e-9。

于是队友专门把代码长度翻了三倍特判，最后大概在 180 分钟的时候把 A 过了。

这个时候我也发现炉石题的问题——概率不能最后再来统计，然后大概过了 10 分钟把 L 过了。

这个时候看榜，E 和 I 都有人过。于是我继续想 E，队友想 I。队友想了会儿 I 后说细节有点多，于是最后一起想 E。直到最后都没想出来…… 然后发现思路都有点问题，E 是个 treedp……

讲题的时候说 I 一个在队友的贪心基础上再加个值域线段树就可以搞定，考场过了 18 个，顿时感觉亏爆

### 结果

滚榜的时候那叫一个刺激。最后居然刚好垫底 Au…… 感觉这场 ICPC Regional 是不是水分有点大啊（