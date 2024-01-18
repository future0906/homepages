---
id: 192
title: HybridCLR浅析
date: '2023-07-18T01:59:51+08:00'
author: shane
layout: article
guid: 'https://yaozhixiang.com/?p=192'
permalink: /2023/07/hybridclr-unity-introduce/
categories:
    - 未分类
---

周日心血来潮开了一下Unity，突然想起之前有一个据说秒天秒地秒空气的热更框架huatuo，打算找来研究一下原理。

搜索了一下，发现huatuo已经归给掌趣，作者后来自己开了一个HybridCLR。知乎、B站、Google搜了一圈，发现基本都是同样的内容，像极公关稿。说什么工作在IL2CPP层、没有虚拟机、性能牛B、开箱即用，玄乎到不行。好奇行心驱使下，看了一下源代码，大概明白工作原理：

1. 在IL2CPP生成完Unity自身C#对应的C++代码之后，插入一些HybridCLR的C++代码，其中包含所谓的interpreter和transform。
2. interpreter本质就是一个虚拟机，用来执行`HiOpcode`。
3. transform就是用来做IL-&gt;HiOpCode转译
4. interpreter传宣是register based，但是代码看来就是stack based。不知道作者是否有商业版本的实现
5. HiOpCode就是基于Unity的一个特化指令集。只要指令集命中的执行效率肯定高，除此之外通用性能可见的不太理想。另外虚拟机的指令集是变长的，想要优化效率估计有困难。可以简单类比CISC和RISC的区别。
6. 相比现有脚本解决方案，绕一层C#再到引擎接口，HybridCLR直接通过interpreter调用IL2CPP::vm接口，确实性能会有优势。还是那个前提，只要你的代码能命中特化的opcode。
7. 这个方案的本质还是Unity不提供纯C++库的调用方式。IL2CPP::vm这个胶水层虽然比直接C#要薄，但是相对纯C++接口还是太厚了。如果Unity自己能提供一层C/C++的封装（类似fmod那样），对脚本接入就不用这么纠结。

综上所述，HybridCLR所获得的性能提升主要在更薄的胶水层和特化的OpCode；相对应的通用解析性能可以预见的不高（盲猜，没有任何证据，不用杠。杠就是你对）。对于纯C#的项目，想获得一些热更修复的能力，可以考虑。如果是想达项目的脚本化，HybridCLR估计有难度。

ILRuntime的原理其实也类似，只是我记得最早ILRuntime是直接运行IL OpCode的。今天看了，好像也用了类似的方式。