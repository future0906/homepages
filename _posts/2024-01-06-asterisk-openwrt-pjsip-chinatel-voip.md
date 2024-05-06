---
title: openwrt下用asterisk+pjsip稳定的拨出和接入电信固话（续）
layout: article
key: 2024-01-06-asterisk-openwrt-pjsip-chinatel-voip
---
在2021年的时候，曾经试过用chan_sip和asterisk拨号电信固话具体可以参考[这篇](https://blog.yaozhixiang.com/?p=78)。
当时基本跑通，只是挂一段时间后，电话会无法拨入。当时认为是电信VOIP局端需要一些特殊的协议没有继续折腾下去。刚好上周开始有点时间，又想慢慢折腾，顺便把方案升级了一下。chan_sip在asterisk21就会被抛弃，因此转去pjsip是大势所趋。但是全网的固话教程都是按照chan_sip配的。
作为对SIP完全没了解又不想浪费时间一点点啃文档，有什么办法呢？还好官方有一个chan_sip配置转pjsip的脚本，在[这里](https://github.com/asterisk/asterisk/tree/master/contrib/scripts/sip_to_pjsip)。可以用网上配置转成pjsip，有一个大概的配置，然后再按需修改。
张大妈有一篇文章[^1]，也是广州电信的，同样是广州电信的同学可以抄一下作业。总结起来有一下几点：

1. 要保证跟局端的网络层面是通畅的。如果你是有其他网络在这个设备上，要单独为VOIP的网络指定路由。通常来说需要静态路由的DNS服务器（可选，可以在本地DNS写死，只要用来解析outbound_proxy的地址，如bacxxxx.ctcims.cn），还有bac对应地址的网络段。
2. sip转成pjsip的配置，大体上问题不大，主要是outbound_proxy，需要按照[^2]这里提示的重新修改。电信的bacxxx就是outbound_proxy。
3. 也可以参考[^3]，里面也有一些关于pjsip的配置可以参考。
以下是我本地配置，仅供参考：

```ini
[global]
type = global
user_agent = HUAWEI-EchoLife HS8145V5 ; UA，最好改成你的光猫

[transport-udp]
type = transport
protocol = udp
bind = 0.0.0.0:5060
local_net = 192.168.1.0/255.255.255.0 ; 这里改成你自己的局域网子网

[reg_gd.ctcims.cn];名字随意
type = registration
retry_interval = 30
max_retries = 10
expiration = 1800
transport = transport-udp
outbound_auth = auth_reg_gd.ctcims.cn
client_uri = sip:+86XXXXXXXXX@gd.ctcims.cn; XXX改成你的电话号码
server_uri = sip:gd.ctcims.cn:5060
outbound_proxy = sip:bacxxxxxxx.ctcims.cn\;lr ; 改成你的outbound_proxy，注意`\;lr`这个要保留
support_outbound = yes

[auth_reg_gd.ctcims.cn]
type = auth
password = XXXXXXX; 你的VOIP密码
username = +86XXXXXXXXX@gd.ctcims.cn; 改成你VOIP账号

[trunk_ims]
type = aor
contact = sip:+86XXXXXXXXX@gd.ctcims.cn; VOIP账号
minimum_expiration = 1800
qualify_frequency = 15; XXX重要 没有这行挂久后会无法打入电话

[trunk_ims]
type = identify
endpoint = trunk_ims
match = bacxxxxxxx.ctcims.cn; 改成你的outbound_proxy

[trunk_ims]
type = auth
auth_type = userpass
username = +86XXXXXXXXX@gd.ctcims.cn; VOIP账号
password = XXXXXXX; 你的VOIP密码

[trunk_ims]
type = endpoint
context = external
dtmf_mode = inband
direct_media = no
trust_id_inbound = no
from_user = +86XXXXXXXXX; 改成你的电话号码带区号（区号开头的0省略）
from_domain = gd.ctcims.cn
outbound_auth = trunk_ims
aors = trunk_ims
outbound_proxy = sip:bacxxxxxxx.ctcims.cn\;lr ;改成你的outbound_proxy，注意`\;lr`这个要保留
allow = alaw,g722,ulaw
100rel = yes
```

碰到的问题：
1. 刚重启asterisk能正常拨入，过一段时间就无法打入电话。其实就是要在aor的section上，加上qualify_frequency。间隔一段时间发送心跳，以保证局端的NAT有效。
2. 没有声音，要保证allow里面的codec安装，并且跟你的客户端编码对应
3. 安装simple_bridge，否则sip往外线打电话可能无法转发。

由于每个地方的SIP局端设备都不一致，因此最重要还是要掌握调试方法。这几个命令相当有用：

```shell
core set verbose 5 # 提高日志等级
core set debug 5 # 提高调试日志等级
pjsip set logger on 
rtp set debug on
# 除了上面开始日志打印外，还pjsip还提供协议历史
pjsip set history on #打开协议历史记录
pjsip show history # 查看协议
pjsip show history entry 1 # 查看1号协议的内容
```


Update(20240110):
--------
VOIP接口DHCP返回的IP地址，居然会变。而且还是整个网段的变，网关地址也变了，导致静态路由不正确。解决方案有两个：
1. 用SMZDM的方案，用multiwan+策略路由。这方案最方便，但是引入了mwan3，对我来说有点重度了。
2. 增加DHCP脚本，每次分配的时候重新设一下网关。参考下面脚本，放到`/etc/udhcpc.user.d`

```shell
#!/bin/sh

. /lib/functions.sh

logger -t ${0##*/} ${@} $(env)
DHCPC_EVENT="${1}"
DHCPC_IF="${J_V_interface}"
DHCPC_GW="${router}"
echo $DHCPC_IF $DHCPC_EVENT
case ${DHCPC_EVENT} in
(bound|renew) ;;
(*) exit 0 ;;
esac

case ${DHCPC_IF} in
(VOIP) ;;
(*) exit 0 ;;
esac

handle_route() {
        local config=$1
        local custom=$2
        config_get route_interface $config interface
        config_get route_target $config target
        if [ $route_interface != "VOIP" ] ; then
                return
        fi
        uci set network.$config.gateway="$DHCPC_GW"
        ip route add $route_target via $DHCPC_GW dev ${interface}
}

logger -t ${0##*/} "Change Route"
ip route delete default dev "${interface}"
config_load network
config_foreach handle_route route
uci commit network
logger -t ${0##*/} "Change Route complete"
```

[^1]: [openwrt上运行asterisk搭建电话语音网关,对接电信ims-sip配置上遇到的一些坑](https://post.smzdm.com/p/ao9250nm/)
[^2]: [PJSIP with Proxies](https://docs.asterisk.org/Configuration/Channel-Drivers/SIP/Configuring-res_pjsip/PJSIP-with-Proxies/#asterisk-configuration)
[^3]: [Asterisk in Openwrt](https://openwrt.org/docs/guide-user/services/voip/asterisk)