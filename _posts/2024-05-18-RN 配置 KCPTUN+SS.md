---
layout: post
title:  "RN 配置 KCPTUN 加速 SS"
date:   2024-05-18 10:00:00 +0800
categories: VPS
tag: Tools
---
* content
{:toc}

[KCPTUN各平台客户端下载汇总 附KCPTUN搭建流程](https://medium.com/@mways/kcptun%E5%90%84%E5%B9%B3%E5%8F%B0%E5%AE%A2%E6%88%B7%E7%AB%AF%E4%B8%8B%E8%BD%BD%E6%B1%87%E6%80%BB-%E9%99%84kcptun%E6%90%AD%E5%BB%BA%E6%B5%81%E7%A8%8B-183e49bd54e6)
# KCPTUN 加速 SS

## VPS：RackNerd
[VPS 推荐网站](https://p3terx.com/)

[ 检查 VPS 的 IP 是否被封？](https://www.vps234.com/hostwinds-ip-blocked-fix-isp/)
### RackNerd 哪个机房好？
* 优先选择圣何塞(San Jose)、洛杉矶(Los Angeles)、西雅图(Seattle)这几个机房，都在美国西海岸，离中国大陆近延迟低。
* 对于可选项，通过 `ping ip` 测试延迟和丢包率，西海岸延迟 200ms 左右。
### 注意事项和其它说明
* 建议购买至少 1GB 内存配置的 VPS ，以获得更好的使用体验。
* 建站需求至少 2GB 双核， 4GB 以上更佳。最推荐 AMD 高性能系列。
* 如果分到了被墙的 IP 可以在购买后 72 小时内免费更换 (点击 Change IP 按钮自助更换)，之后更换一次 IP 需支付 3 美元。

## 配置SS+KCPTUN

### SS
1.连接上服务器后，下载并执行脚本 shadowsocks-all.sh
```shell
ssh root@IP
wget --no-check-certificate -O shadowsocks-all.sh https://raw.githubusercontent.com/teddysun/shadowsocks_install/master/shadowsocks-all.sh
chmod +x shadowsocks-all.sh
./shadowsocks-all.sh 2>&1 | tee shadowsocks-all.log
```
2.推荐安装Shadowsocks-libev版，因为它较为轻便，资源占用较小。
3.设置参数。重新配置参数`./shadowsocks-all.sh`

### KCPTUN
1.连接上服务器后，下载并执行脚本 kcptun.sh
```bash
ssh root@IP
wget --no-check-certificate https://github.com/kuoruan/shell-scripts/raw/master/kcptun/kcptun.sh
chmod +x ./kcptun.sh
./kcptun.sh
```
2.执行脚本后，设置参数，大部分选择默认值即可
* 端口：默认29900，即为KCPTUN与其客户端连接使用的端口，**被封了就换这个端口**。
* 要加速的地址：默认127.0.0.1，及VPS本地地址。
* 要加速的端口：SS/SSR使用的端口。
* 密码：用于KCPTUN客户端连接使用，不要使用默认密码。
* 加密方式选择：较强的加密方式会影响网速，建议默认aes或不加密。
* 加速模式：默认fast即可。随后可以手动修改为其它模式，测试加速效果。
* MTU：默认1350即可。
* sndwnd：<span style="color: #ff0000;">发送窗口大小，与服务器的上传带宽大小有关，这项与rcvwnd的比例会影响加速效果，可以暂时设置为默认的512。
* rcvwnd：<span style="color: #ff0000;">接收窗口大小，与服务器的下载带宽大小有关，也可以暂设置为默认的512，或者1024也可以。
* 


## KCPTUN 重新配置端口

换过IP之后，

```bash
ssh root@IP
```

KCPTUN重新配置

```bash
./kcptun.sh reconfig
```

把IP改了就行。

如需更新：

```bash
./kcptun.sh update
```

