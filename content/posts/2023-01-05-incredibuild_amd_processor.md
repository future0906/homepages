---
title: 二奶机搭建和Incredibuild家用配置
author: shane
aliases: /2023/01/05/incredibuild_amd_processor/
categories:
  - HomeLab

---
由于家里闲置了一块B550主板和一个趣造机箱。再将自用的电脑升级到5905X，又多个闲置3900XT，刚好可以组台二奶机。于是折腾了大半个月终于将两台机器稳定下来。一路下来坑还是挺多的，吐槽一下。

<ol class="wp-block-list">
  <li>
    5950X蓝屏。一开始以为内存问题，毕竟是四条DDR4 3600(XMP) 16G，可能会不稳定。后面更换了内存DDR4 2400的内存，依然发现蓝屏。想走售后，不过这个CPU购于咸鱼。虽然是盒装，但是送保还挺麻烦的。后面上网查了可能是fTPM的问题，尝试着关掉，并且打开SVM。目前比较稳定，帝国4也不crash。机器目前也没有蓝屏。
  </li>
  <li>
    内存兼容性问题。由于之前64G感觉不太够用，所以换了光威的32G 3200(XMP)*4，没想到AMD的内存管理器太渣了，开XMP直接启动不了。没办法，目前只能按照2400的JDEC设置来跑了。经过上面折腾，AMD感觉还是YES不起来啊。各种的兼容问题太闹心了。下次装机还是用Intel的吧。
  </li>
  <li>
    Incredibuild 最近提供了一个Free License申请，不同限时的Free Trail，这个是永久免费的。可以参考<a href="https://www.incredibuild.com/pricing">这里</a>申请。虽然只能支持2个Agent，以及最多16个核作为Helper，但目前这个情况对家用是基本够用的。也可以暂时不用折腾FastBuild，UE对IB的支持比FastBuild好很多。
  </li>
  <li>
    由于IB的16个核是不算本地的，所以总共有32+16=48核作为编译。对于UE5.1，全新编译只用了25分钟。基本能达到在公司集群编译的水平。另外在联编的过程中，网络的占用其实只有400Mbps左右（双向），千兆网络对于IB应该不会成为瓶颈，也没没必要升级万兆网络。
  </li>
</ol>