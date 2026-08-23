# Plugin

本目录只存放 **Loon Auto Select 自己维护或实际增强过的插件**。

上游已经稳定维护、且本项目没有修改的插件不会复制到这里，仍直接引用原作者 / 上游地址。

目前维护：

```text
Prevent_DNS_Leaks.lpx
```

## Prevent_DNS_Leaks Enhanced v2.0.0

准确定位：

> **网络隐私检测辅助 + 检测站路由保护。**

它不是“安装后自动解决所有 DNS 泄漏”的万能开关。

### 它解决什么问题

主配置通过：

```ini
https://raw.githubusercontent.com/zosb/Loon-Auto-Select/main/Plugin/Prevent_DNS_Leaks.lpx, policy=兜底后备策略, enabled=true
```

加载插件。

插件中的 `PROXY` 会映射到主配置指定的策略，因此常见检测站不会因为规则遗漏而意外走 `DIRECT`。

当前覆盖五类检查：

| 检测方向 | 典型站点 | 主要观察内容 |
| --- | --- | --- |
| 综合网络隐私 | BrowserLeaks / IPLeak / Pixelscan / IPHey | IP、DNS、WebRTC、代理与浏览器环境 |
| DNS Resolver / DNS Leak | DNSLeakTest / DNSCheck.tools / bash.ws | 实际可见 DNS Resolver、DNS 查询路径 |
| IP / ASN / ISP | IPInfo / Whoer / ifconfig / IP.SB | 公网 IP、ASN、ISP、出口国家 / 地区 |
| IPv4 / IPv6 | ping0 / Test-IPv6 / IPv6-Test | IPv4/IPv6 双栈与意外 IPv6 暴露 |
| WebRTC / 指纹一致性 | BrowserLeaks / AmIUnique / EFF Cover Your Tracks | WebRTC/STUN、浏览器指纹与出口环境一致性 |

### 它不会做什么

`Prevent_DNS_Leaks Enhanced` **不会**：

```text
修改 dns-server
强制 DoH / DoQ / DoH3
劫持 UDP/TCP 53
封锁所有公共 DNS
替代 Block_HTTPDNS
替代 Loon TUN
替代 disable-stun
自动修复 DNS / IPv6 / WebRTC 泄漏
```

这是有意为之。

公共配置如果直接封锁公共 DoH、DNS IP 或大量网络基础设施域名，可能破坏 App、企业网络、Captive Portal、CDN、用户自定义 DNS 或本地网络兼容性。

因此本插件坚持：

> **负责检测与路由辅助，不擅自改写用户 DNS 架构。**

## 与主配置其它组件的关系

```text
Block_HTTPDNS
→ 降低部分 App 通过自带 HTTPDNS 绕开 Loon DNS 框架的情况

Prevent_DNS_Leaks Enhanced
→ 让检测站走指定代理，并帮助观察当前网络环境

Loon dns-server / TUN / IPv6
→ 决定 DNS 与 IP 栈的实际工作方式

disable-stun / udp-fallback-mode
→ 决定 STUN / UDP 的公开默认行为

代理节点
→ 最终决定公网出口、UDP 支持、IPv4/IPv6 能力与部分 DNS 行为
```

## 推荐检测流程

使用本项目默认配置进行测试时，建议：

```text
1. 开启 Loon
2. 等待当前自动策略稳定选中节点
3. 先检查公网 IP / ASN
4. 再检查 DNS Resolver
5. 再检查 IPv6
6. 最后检查 WebRTC / STUN
7. 至少使用两个不同检测站交叉验证
```

不要只根据一个网站的一次结果判断“泄漏”或“不泄漏”。不同检测站的后端、DNS 探测方式、GeoIP 数据库和缓存都可能不同。

## 如何理解结果

### 公网 IP

检测站显示的公网 IP 应与当前代理节点出口基本一致。

如果出现本地运营商公网 IP，应重点检查：

```text
策略是否实际选中代理节点
→ 对应流量是否被规则匹配
→ 节点是否可用
→ 是否发生 DIRECT / bypass
```

### DNS Resolver

DNS Resolver **不要求必须与代理 IP 完全相同**。

公共 DNS、运营商 DNS、Anycast DNS、系统 DNS 与代理服务商 DNS 都可能显示不同 ASN 或不同城市。

本项目默认：

```ini
dns-server = system
```

因此它的设计目标首先是兼容性，而不是强制实现“DNS Resolver 必须与代理出口同地区”的匿名 DNS 模式。

如果你的需求是更严格的 DNS 隐私，需要另外设计 DoH / DoQ / DNS Hijack 策略，而不是依赖本插件名称。

### IPv6

本项目公开默认版：

```ini
ip-mode = v4-only
ipv6-vif = off
```

因此正常情况下不应主动产生代理 IPv6 出口。

如果检测站仍显示意外的公网 IPv6，应进一步检查系统网络、浏览器行为、本地绕过和 Loon 的实际 IP Stack 设置。

### WebRTC / STUN

本项目默认：

```ini
disable-stun = true
```

目标是降低 STUN / WebRTC 暴露本地网络信息的概率。

但某些语音、视频、实时通信或游戏可能依赖 STUN。出现兼容问题时，应先确认业务需求和节点 UDP 支持，再在本地临时测试，不建议直接修改公开默认版。

## 当前收录原则

插件不是域名越多越好。

维护规则：

```text
明确提供 DNS / IP / IPv6 / WebRTC / 网络隐私检测
→ 可以收录

只是普通 VPN 官网、广告页、下载页
→ 不默认收录

公共 DoH / DNS 基础设施
→ 不默认 REJECT

重复域名
→ 去重

已经失效或用途发生变化的检测站
→ 删除或替换
```

这样可以控制误匹配和维护成本。

## 上游参考

本插件最初的设计思路参考了可莉插件库中的 `Prevent_DNS_Leaks`：

```text
https://kelee.one/Tool/Loon/Lpx/Prevent_DNS_Leaks.lpx
```

Loon Auto Select 版本不是原文件的简单镜像，而是根据本项目的配置哲学重新整理、去重、扩展并重新定义职责边界。

第三方检测站及上游资源仍受各自服务条款和许可约束。
