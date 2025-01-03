---
title: 利用运营商固网和Asterisk在家搭建呼叫中心的一些总结
author: shane
date: 2021-08-08T17:52:29+00:00
aliases: /2021/08/09/利用运营商固网和asterisk在家搭建呼叫中心的一些总结/
categories:
  - HomeLab

---
这几年推进光进铜推，实际上现在家庭的固话都是VoIP的。家里的光猫被封印在弱电箱，电话线拉不出来，固话就一直荒废了，趁着这两天有点时间，折腾了一下想尝试将固话用起来。最终成功实现了，自动外呼、IVR菜单、呼叫转移等功能。不得不说，Asterisk的确很强大，很多PBX系统都是在这个基础上做起来的。不过似乎局端的设备都Asterisk的认证支持有点问题，过一段时间就会掉线，所以目前是打住了。先将步骤和碰到的问题总结下来吧，方便后续其他同学折腾。

<ol class="wp-block-list">
  <li>
    首先光猫要获得超级管理员权限，然后设置一个新的Vlan，Vlan ID用46，记得业务类型选择其他，不要选择语音。选择语音之后就不能用桥接类型，导致接口本身获得IP而不是对应连接的设备获得IP。
  </li>
  <li>
    桥接配置后需要获得SIP的鉴权信息。我的光猫型号是<strong><code>HN8145V</code></strong>，可以将配置备份导出之后，用<a href="https://raw.githubusercontent.com/Mr-Sheep/Mr-Sheep.github.io/master/resource/ctce_cfg.exe">ctce_cfg.exe</a>解密，然后再用这个工具<a href="https://github-releases.githubusercontent.com/115249892/9c5187dc-e98f-11e7-9d5e-4dfa834ba4f4?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAIWNJYAX4CSVEH53A%2F20210807%2Fus-east-1%2Fs3%2Faws4_request&X-Amz-Date=20210807T181435Z&X-Amz-Expires=300&X-Amz-Signature=d0eec5d9dc9a3b975d07f48fa5f613eef55aa468a653ca191fc15e6db6c39710&X-Amz-SignedHeaders=host&actor_id=1061481&key_id=0&repo_id=115249892&response-content-disposition=attachment%3B%20filename%3Dhuawei-win.zip&response-content-type=application%2Foctet-stream" data-type="URL" data-id="https://github-releases.githubusercontent.com/115249892/9c5187dc-e98f-11e7-9d5e-4dfa834ba4f4?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAIWNJYAX4CSVEH53A%2F20210807%2Fus-east-1%2Fs3%2Faws4_request&X-Amz-Date=20210807T181435Z&X-Amz-Expires=300&X-Amz-Signature=d0eec5d9dc9a3b975d07f48fa5f613eef55aa468a653ca191fc15e6db6c39710&X-Amz-SignedHeaders=host&actor_id=1061481&key_id=0&repo_id=115249892&response-content-disposition=attachment%3B%20filename%3Dhuawei-win.zip&response-content-type=application%2Foctet-stream">huawei-win</a>解密鉴权的$2加密内容。
  </li>
  <li>
    在获得鉴权和网络接通以后，可以在Win下用<a href="https://www.microsip.org/download/MicroSIP-3.20.6.zip">MicroSIP</a>测试一下能不能直接拨号，具体的参数填写请参照这个截图：
  </li>
</ol>

![MicroSIP-Account](./MicroSIP-account1.png)

4. 测试的时候必须注意SIP对应的网络信息和接口是独立的内网网段，所以必须收到设置路由，包括SIP服务器、SIP代理、DNS服务器等相关的地址。这个也是我觉得比较麻烦而且不靠谱的地方，因为你不知道SIP代理、DNS地址什么时候会变。而且你不知道中间过程中会不会涉及到其他地址。所以如果想长期搞的同学还是建议单独弄个设备，默认网关走SIP的内网，这样设备就会上不了网，但是会省却很多麻烦。

5. 如果测试拨号过程中出现能呼叫、接听。但是接听之后，没法说话或者其他情况。建议还是检查一下网络和Asterisk的NAT设置。我这里就卡了一段时间，出现了能听，但是不能说的情况。主要是由于说和听分别由两条单工的RTP负责的。其中一个方向的RTP网络出现了问题，就会导致这种情况。

6. MicroSIP拨号成功后，就可以配置Asterisk服务器，具体可以参考这篇<a href="http://lsjtt.asuscomm.com:88/?post=1099" data-type="URL" data-id="http://lsjtt.asuscomm.com:88/?post=1099">文章</a>。这个配置还是挺好懂的，就是配了两个内网SIP用户和一个外线。

7.Astersik关于IVR菜单，可以参考篇<a href="https://wiki.asterisk.org/wiki/display/AST/Creating+a+Simple+IVR+Menu" data-type="URL" data-id="https://wiki.asterisk.org/wiki/display/AST/Creating+a+Simple+IVR+Menu">文章</a>。自动外呼的话，可以参考<a href="https://www.voip-info.org/asterisk-auto-dial-out/" data-type="URL" data-id="https://www.voip-info.org/asterisk-auto-dial-out/">这篇</a>。这两个我觉得挺有意思的，前者可以做一个很酷家庭语音菜单网关+留言信箱。后者可以结合家里的设备做报警外呼。

参考：

<https://wiki.asterisk.org/wiki/display/AST/Creating+a+Simple+IVR+Menu>

<https://www.voip-info.org/asterisk-auto-dial-out/>

<http://zhmail.com/2016/09/24/configuring-epon-e8-c-gateway-to-access-ctc-voip-network/>

<http://lsjtt.asuscomm.com:88/?post=1099>