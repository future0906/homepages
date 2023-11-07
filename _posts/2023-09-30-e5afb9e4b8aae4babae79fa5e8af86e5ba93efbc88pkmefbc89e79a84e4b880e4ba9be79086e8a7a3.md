---
id: 200
title: 对个人知识库（PKM）的一些理解
date: '2023-09-30T01:42:26+08:00'
author: shane
layout: article
guid: 'https://yaozhixiang.com/?p=200'
permalink: /2023/09/%e5%af%b9%e4%b8%aa%e4%ba%ba%e7%9f%a5%e8%af%86%e5%ba%93%ef%bc%88pkm%ef%bc%89%e7%9a%84%e4%b8%80%e4%ba%9b%e7%90%86%e8%a7%a3/
categories:
    - 未分类
---

# PKM

PKM是Personal Knowledge Management的缩写。这个跟当时PIM（Personal Information Management）类似，核心都在Management上。  
实际上对于Information，使用管理的方法是没问题的。因为信息总类都基本是固定的（联系人，日程之类的）；而Knowledge的内容都是不固定的，而且是很难做到预先的层级划分。  
如果你使用传统的EverNote，Joplin等工具，对知识进行树形分类你会发现怎么都分不对；最后，为了分类的正确性，而不停调整分类。

## P.A.R.A.方法

这个方法我实践下来，更像是PIM的管理方法，而不是PKM。虽然我觉得，将自己感兴趣的东西和正在做的东西分成AREA和PROJECT这种方式深得我心。但实际实践下来却发现，这个方法实际上更关心AREA和PROJECT，对RESOURCES的管理方式一点没有提及。或者说Resources实际上还是按照传统的进行树形分类。所以我觉得P.A.R.A.并不是一个个人知识管理的好办法。甚至说它不是一个PKM的方法，更像一个个人OKR系统。

## 识别什么是Knowledge

通过整理自己的笔记（从WIZ-&gt;NOTION-&gt;OBSIDIAN-&gt;OBSIDIAN+JOPLIN）发现自己这么多年所谓的”笔记”，其实大部分都这些类型：

- 网上摘抄，参考之前自己写的：[笔记整理、DataHoarder和NAS](https://yaozhixiang.com/2022/09/%e7%ac%94%e8%ae%b0%e6%95%b4%e7%90%86%e3%80%81datahoarder%e5%92%8cnas/)
    - Maybe Later类型，感觉可能会有用，想保留下来（Tab数量已经爆棚）
    - 操作手册类型的笔记。都是整篇内容取录下来
    - Awesome类笔记
- 自己工作中总结出来的命令（Cheatsheet之类的）方便后面翻阅
- 项目中的一些有用的信息之类（账号、地址）

其实上面这些都不是你自己知识，只有当你对里面的一些内容有自己的看法、总结时，才是你真正的知识。虽然这些不是你的知识，但也是属于有用的东西。我觉得可以放到Joplin上，这类内容，其实很容易就会有一个归属的，而且这种归属还特别容易分类。

## Zettelkasten

前面说了树形的分类会导致不停的要调整分类，会陷入类似满足数据库范式一些悖论，整个系统因为要满足范式规范而进行复杂的表设计。正如Mongo对范式的问题给出解法：既然要满足高范式设计这么麻烦，我干脆就只用最低的范式去做这个事情。Zettelkasten就是这么个方法：既然分类这么麻烦，我索性不分类了。所有的卡片都是一样处理，放到同样的地方。这里有两个重要的点：  
1\. 我先不管分类，先把想法和东西先记录下来  
2\. 后面需要回头对卡片用其他方法索引起来，并且索引的过程中其他相关内容进行回溯更新  
其中重点的是2：需要不停审视你的笔记、建立索引。意味着你的笔记要不停的被Review和更新，而不是躺在某个分类发臭。  
至于ZKID，其实我觉得对于Obsidian或者其他支持超链接的笔记，是不太重要的。对于卢曼那个用原始卡片盒，ZKID是索引主题的关键。

- - - - - -

References  
[什么是 Zettelkasten 卡片盒笔记法？](https://www.zhihu.com/question/384309878/answer/2713962647)