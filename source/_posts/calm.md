---
title: CALM：一致性与逻辑单调
date: 2018-05-13 09:33:17
tags:
 - 一致性
 - 单调逻辑
 - CALM
categories: 分布式架构
---


CALM原则的全称是 Consistency and Logical Monotonicity ，主要描述的是分布式系统中单调逻辑与一致性的关系，它的内容如下：
- 在分布式系统中，单调的逻辑都能保证 “最终一致性”，这个过程中不需要依赖中心节点的调度
- 任意分布式系统，如果所有的非单调逻辑都有中心节点调度，那么这个分布式系统就可以实现最终“一致性”

<!-- more -->

## 单调逻辑和非单调逻辑
如果一个结论是由单调逻辑根据一系列信息推理出来的，那么这个结论就会一直有效，即使后来又获得了新的信息。简单来说，就是后来的推论永远不会影响到之前推论的有效性。

反之，如果新的信息会导致之前的推论无效，那么这样的逻辑就是非单调逻辑。

### 一个逻辑非单调的例子
如果T是鸟，那么我们认为T可以飞，这样的逻辑可以表述为：
```
Bird(T) -> Fly(T) 
```
现在有一只鸟Tweet，那么`Bird(Tweet)`是成立的，那么是否就有`Fly(Tweet)`呢？根据现在已知的条件，我们可以很容易做出结论
```
Bird(Tweet) -> Fly(Tweet)
```
这样的逻辑看起似乎没什么问题，但是随着我们知识的增长，我们会发现一些特例：
1. 如果Tweet是企鹅或者鸡，那么Tweet不能飞
2. 如果Tweet的翅膀严重受伤了，那么Tweet不能飞

如果Tweet是上面条件中之一，那么`Fly(Tweet)`的结论就会失效。这样的逻辑就是非单调逻辑。

非单调逻辑的一个特点就是没有考虑全部的知识

### 一个逻辑单调的例子
例如数学上常见的逆否命题推论：
```
如果 P -> Q
那么 -Q -> -P
```
这个推论无论`P`和`Q`的内容是什么都是成立的。单调逻辑的特点就是已经考虑了全部的情况。

### 非单调逻辑
现实世界中总是有各种特例情况，我们通常无法再下结论之前考虑到所有的情况。所以大部分逻辑通常都是非单调逻辑（基于假设），非单调逻辑一般有5种类型的假设，例如：已知船通常可以用来渡河，现在有一艘船A，船A可以渡河吗？
- **主观认知逻辑(subjective autoepistemic)**：船A可以渡河，因为我没有得到任何船A无法渡河的信息。
- **原型逻辑(prototical)**：船A可以渡河，因为已知：船通常可以渡河
-  **最低风险逻辑（No-Risk）**: 如果假设船A不能渡河，并且如果我的假设错误，那么我就浪费这一次渡河的机会（这是无法接受的），所以假设船A可以渡河。（最低风险是依据自己的利益来假设的，也可用类似的逻辑最终推导出“船A不能渡河”，具体的结果依据自身的利益来决定）
-  **最优猜测逻辑(Best-Guest)**：首先我需要渡河，但是除了船A以外我不知道还有没有其他船，所以假设船A可以渡河
- **概率逻辑(Probabilistic)**: 超过80%的船可以渡河，所以假设船A也可以渡河

参考资料：
> [CALM: consistency as logical monotonicity](http://bloom-lang.net/calm/)
> [TAU\_winter08\_ArielLogic](http://www.cs.tau.ac.il/~annaz/teaching/TAU_winter08/Seminar/ArielLogic.pdf)
> [Non-monotonic logic - MBALib](http://wiki.mbalib.com/wiki/Non-monotonic_logic)