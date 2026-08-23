# Loon Auto Select

一份面向 **Loon** 的长期自动分流配置。

它基于 **iKeLee / 可莉 Loon Auto** 配置模板进行整理和扩展，目标很明确：

> **添加自己的节点订阅后，尽量不需要频繁手动切换节点，让常用服务按照预设地区自动分流、自动测速，并在节点异常时自动容灾。**

本配置 **不绑定任何机场品牌**。只要节点名称能够识别出国家 / 地区，就可以参与对应地区的自动测速和策略选择。

公开仓库中 **不会保存私人订阅 URL、Token、MitM 证书或密码**。

---

## 快速开始

### 一键导入到 Loon

[**点击这里直接添加到 Loon**](https://www.nsloon.com/openloon/import?sub=https%3A%2F%2Fraw.githubusercontent.com%2Fzosb%2FLoon-Auto-Select%2Fmain%2FLoon_Auto_Select.lcf)

导入后，再在 Loon 中添加你自己的机场 / 节点订阅即可。

### Raw 配置地址

```text
https://raw.githubusercontent.com/zosb/Loon-Auto-Select/main/Loon_Auto_Select.lcf
```

### 快捷操作

- [**开启 Loon**](https://www.nsloon.com/openloon/on)
- [**更新全部订阅资源**](https://www.nsloon.com/openloon/update?sub=all)

如果部分 GitHub / Raw GitHub 资源在当前网络下直连更新失败，可以先开启 Loon，再执行“更新全部订阅资源”。

---

## 这个配置做了什么

配置会自动完成以下工作：

- 中国大陆常规流量自动 `DIRECT`
- 自动识别香港、澳门、台湾、日本、韩国、新加坡、美国、英国、德国节点
- 每个地区内部自动测速，优先使用延迟更合适的节点
- Google、社交媒体、开发服务优先使用亚洲近邻节点，异常时再扩大到全球节点
- YouTube 使用地区内测速、地区间测速和全球故障转移
- AI 服务固定新加坡，尽量保持稳定出口
- TikTok 固定韩国
- 国际流媒体和 Spotify 固定美国
- Telegram 默认香港优先，并按顺序自动容灾
- 游戏平台根据延迟自动选择亚洲 / 美国地区，同时降低频繁切换
- 主要应用保留手动接管入口，自动策略异常时可以临时指定地区或节点
- 集成 HTTPDNS 防绕过、DNS 防泄漏和常用 App 去广告插件

整体设计原则是：

> **正常情况下自动运行；出现特殊需求或节点异常时，仍然允许手动接管。**

---

## 节点分类方式

节点 **只按照国家 / 地区归类**。

不会因为节点名称中出现以下标签就被排除：

```text
2X
高倍率
实验
测试
GAME
游戏
YouTube
无广
```

例如：

```text
🇲🇴 澳门 V6-01 Youtube 无广 2X
```

只要它属于澳门，就会进入 **澳门节点池**。

地区筛选兼容常见中文、繁体、英文、国家代码、旗帜以及常见命名格式，例如：

```text
香港
Hong Kong
HongKong
HK-01
HK_01

South Korea
KR-01

United States
United_States
USA-02
```

因此这份配置并不针对某一家机场设计。

---

## 自动测速与故障检测

为了兼顾自动选路、稳定性和后台开销，测速采用分层周期：

| 类型 | 周期 | 作用 |
| --- | ---: | --- |
| 地区内部 `url-test` | 120 秒 | 在同一地区内选择较合适的节点 |
| 近邻 / 全球 / YouTube / 游戏上层 `url-test` | 300 秒 | 在多个地区策略之间重新评估 |
| `fallback` | 60 秒 | 检测故障并及时切换到后备策略 |

普通地区测速使用：

```ini
interval = 120
tolerance = 50
```

游戏策略使用更高的：

```ini
tolerance = 80
```

目的是避免几十毫秒的小幅波动导致游戏期间频繁切换。

---

## 主要策略说明

| 服务 | 默认策略 |
| --- | --- |
| ChatGPT / Gemini / Claude / Copilot / Grok | 新加坡稳定出口 |
| TikTok | 韩国 |
| Telegram | 香港优先，自动容灾 |
| YouTube / YouTube Music | 港澳台日韩新美自动测速，异常时全球容灾 |
| Google | 亚洲近邻自动测速，异常时全球容灾 |
| X / Threads / Instagram / Facebook / WhatsApp / Reddit / Discord / LINE | 亚洲近邻自动测速，异常时全球容灾 |
| Netflix / Disney+ / Prime Video / Twitch / Apple TV+ | 美国 |
| Spotify | 美国 |
| GitHub / GitLab / Notion / Docker / NPM / Vercel | 亚洲近邻自动测速，异常时全球容灾 |
| Steam / Epic / PlayStation / Xbox | 港澳台日韩新美稳定型自动测速 |
| 其它未匹配流量 | 地区后备策略自动容灾 |

### AI

AI 服务固定使用新加坡节点池：

```text
AI 服务
→ 新加坡节点池
→ fallback 保持稳定出口
```

正常情况下不会自动跨国切换。

如果自动选择的节点有问题，可以进入对应 AI 策略，使用 **新加坡手动策略** 指定具体节点。

### Telegram

自动后备顺序：

```text
香港
→ 澳门
→ 台湾
→ 日本
→ 新加坡
→ 韩国
→ 美国
→ 英国
→ 德国
```

只要前面的地区可用，就不会主动切到后面的地区。

### YouTube

YouTube 使用三层结构：

```text
地区内部测速
→ 港澳台日韩新美之间测速
→ 常用地区全部异常时进入全球故障转移
```

因此不是固定某一个国家，而是优先选择当前更合适的常用地区。

### Google / 社交 / 开发服务

默认使用亚洲近邻池：

```text
香港 / 澳门 / 台湾 / 日本 / 韩国 / 新加坡
```

这些地区全部不可用时，才扩大到：

```text
美国 / 英国 / 德国等全球节点
```

正常情况下不会因为欧美节点偶尔快几毫秒就主动跨洲。

### 国际流媒体

Netflix、Disney+、Amazon Prime Video、Twitch、Apple TV+ 和 USMedia 统一使用美国节点池。

这样做是为了避免频繁跨地区导致媒体库发生变化。

自动策略只在美国节点内部测速，同时保留 **美国手动策略** 方便处理个别节点解锁问题。

### 游戏平台

Steam、Epic、PlayStation、Xbox 不依赖节点名称是否带有“游戏”标签。

候选地区为：

```text
香港 / 澳门 / 台湾 / 日本 / 韩国 / 新加坡 / 美国
```

使用较高 `tolerance`，优先减少频繁切换，而不是追求每一次测速的最低数字。

---

## 手动接管

主要 App 策略组的第一项都是自动策略。

正常使用时无需调整；如果遇到以下情况：

- 某个节点无法解锁服务
- 某条线路临时不稳定
- 想固定某个国家 / 地区
- 想临时指定具体节点

可以直接进入对应 App 策略组选择手动策略。

恢复第一项后，即可重新交给自动逻辑。

---

## IPv6

### 默认关闭

为了让公开配置对更多网络环境和机场保持兼容，默认使用：

```ini
ip-mode = v4-only
ipv6-vif = off
```

也就是说：

- 默认仅使用 IPv4
- 不主动使用 IPv6
- TUN IPv6 默认关闭

如果你的节点入口、机场或本地网络需要 IPv6，请自行在 Loon 中开启。

### 如何开启

进入：

```text
Loon → 配置 → 高级配置 → IP Stack
```

根据自己的网络环境选择：

**一般双栈使用：**

```text
IP 查询模式：IPv4 & IPv6
TUN IPv6：自动
```

**明确需要 IPv6 优先：**

```text
IP 查询模式：IPv6 优先
```

**明确需要强制处理 TUN IPv6：**

```text
TUN IPv6：开启
```

“仅 IPv6”属于谨慎选项，不建议普通用户作为默认设置。

配置已经预留以下 IPv6 本地 / 局域网绕过范围：

```text
::1/128
fc00::/7
fe80::/10
ff00::/8
```

因此手动开启 IPv6 后，一般不需要再修改这些本地网络范围。

---

## 规则顺序

规则按照“越具体越优先”的思路排列，大致顺序为：

```text
LAN
→ AI 专项
→ AI Catch-All
→ YouTube
→ Telegram / TikTok
→ 社交媒体
→ 国际流媒体 / Spotify
→ 开发服务
→ 游戏平台
→ Google
→ Speedtest
→ 中国大陆 REGION
→ FINAL
```

其中：

- Gemini / AI 规则位于 Google 大规则之前
- YouTube 位于 Google 大规则之前
- SteamCN 位于 Steam 之前
- 中国大陆 `REGION_SPLITTER` 保持在远程规则最后

---

## 广告拦截与插件

配置启用了基础广告拦截、HTTPDNS 防绕过以及常用 App 专项插件，例如：

```text
微信
微博
小红书
哔哩哔哩
知乎
淘宝
京东
拼多多
闲鱼
菜鸟
高德地图
滴滴
12306
百度网盘
阿里云盘
夸克
腾讯视频
爱奇艺
优酷
快手
喜马拉雅
网易云音乐
QQ 音乐
Reddit
Spotify
```

同时包含：

```text
QuickSearch
Prevent_DNS_Leaks
Node_detection_tool
BoxJs
Sub-Store
Script-Hub
```

### 关于 MitM

部分 Rewrite / Script 去广告功能需要 HTTPS 解密。

公开仓库不会保存任何 MitM CA 或证书密码，因此默认 `[Mitm]` 保持空白。

如需使用相关功能，请在自己的 Loon 中生成、安装并信任个人 CA。

如果某个 App 出现异常，建议优先关闭对应 App 的专项插件进行排查。

---

## DNS

默认仍使用：

```ini
dns-server = system
```

没有强制加入额外 DoH / DoQ，以减少不同运营商、CDN、IPv4 / IPv6 环境下的兼容性变量。

配置中的特定节点 DNS 插件只处理它自己的目标域名，不会接管全局 DNS，也不会限制其它机场使用。

---

## 使用注意

1. 本仓库不提供节点，也不包含任何机场订阅。
2. 导入配置后，请自行在 Loon 中添加自己的节点订阅。
3. 如果某个地区显示 `N/A`，通常表示当前订阅没有匹配到该地区节点。
4. 如果节点名称非常特殊，可以修改 `[Remote Filter]` 中对应地区的 `NameRegex`。
5. 部分 GitHub / Raw GitHub 资源在某些网络下可能无法直连更新，可以先开启 Loon 再更新全部资源。
6. 专项去广告插件可能随 App 更新失效或产生兼容问题，出现异常时优先停用对应插件。

---

## 安全说明

本仓库是公开仓库，请不要提交以下内容：

```text
私人机场订阅 URL
订阅 Token
用户名 / 密码
MitM CA
ca-p12
ca-passphrase
其它私人凭据
```

`Loon_Auto_Select.lcf` 中的 `[Remote Proxy]` 和 `[Mitm]` 会保持为公开安全模板。

---

## 上游与致谢

本项目基于 iKeLee / 可莉公开的 Loon Auto 配置模板进行修改与扩展。

- ProxyResource: https://github.com/luestr/ProxyResource
- ShuntRules: https://github.com/luestr/ShuntRules
- IconResource: https://github.com/luestr/IconResource
- Qure Icons: https://github.com/Koolson/Qure

原始模板作者：**iKeLee** — https://t.me/iKeLee

本仓库并非 iKeLee / 可莉官方项目，也不代表上述项目对本仓库的背书。

---

## License

本仓库按照上游模板许可要求采用：

**Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International**

即：**CC BY-NC-SA 4.0**。

详情请查看 [LICENSE](./LICENSE)。
