---
id: 64
title: '腾讯DDNS-DNSPOD 双wan口版本运行脚本'
date: '2021-04-25T00:32:54+08:00'
author: shane
layout: article
guid: 'https://yaozhixiang.com/?p=64'
permalink: /2021/04/tencent-ddns-openwrt-dual-multi-wan-ddns-dnspod-wan/
categories:
    - 未分类
---

不知道什么原因，最近家里的宽带居然能双拨叠加带宽了，并且两次拨号都能拿到公网IP。考虑到不要就是浪费的原则，在OpenWRT配好了双WAN负载均衡后，就剩下双公网IP没有处理。

之前一直用QNAP的DDNS，比较遗憾的是，我在QNAP插件里面没有找到双IP注册的方式。并且考虑到QNAP自身拿wan口的IP也不容易，所以就想在路由上打主意。

刚开始确实找到DNSPOD在OpenWRT的[DDNS插件](https://github.com/Tencent-Cloud-Plugins/tencentcloud-openwrt-plugin-ddns)，安装以后发现依旧是不支持多WAN口的。简单测试了一下，DNSPod本身的DDNS是支持多IP的（免费的解析好像支持两个IP，刚好）。还好插件本身是开源的，改起来还算简单。

先改一下插件的页面，将interface改成列表形式，打开文件:`/usr/lib/lua/luci/model/cbi/tencentddns.lua`，将interface改成DynamicList，删掉第一个iface:value，增加你自己的wan口。注意保留internet，这个是用于请求公网来获取IP地址的。

然后修改：`/usr/sbin/tencentddns`，改的内容比较多，可以直接从这个[gist](https://gist.github.com/future0906/d247d2031283a43a2214948b30df7707)上面复制，或者从这里[下载](https://cloud.189.cn/t/ZzaUjmZ3aaMr)。

最后不得不说，bash的语法依然像一坨糊糊，而它的子集ash，更是糊中之王。真的是为难了那些做openwrt插件的朋友。本来这个修改预计用1个小时左右，没想花了3天，前后总共用了3个小时左右。反正社区都有强大的luci，为什么不用lua做一个默认的shell呢？而且lua虚拟机本身占用也极低，在嵌入式设备也不用担心。

PS：国内托管WP无法直接嵌入gist，而国内gitee，coding.net根本无法发布公开的gist，原因大家都懂。国内目前能自由对外发布言论的渠道基本都被堵死。博客托管、评论系统几乎都要接受备案和审查，否者一律不能对公众发布。就连gitee-gist这种也不能幸免。我能理解为什么这么做，却很难从心里认同这件事儿。