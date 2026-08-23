# Loon Auto Select

一份面向 **Loon** 的长期自动分流配置。

本项目基于 **iKeLee / 可莉 Loon Auto** 配置模板整理和扩展，核心目标不是堆砌大量规则，而是让一份配置能够长期保持：

> **节点自动归类、常用服务自动分流、地区内部自动测速、异常自动容灾，同时保留必要的手动接管能力。**

本配置 **不绑定任何机场品牌**，公开仓库也 **不提供节点、不保存私人订阅 URL / Token、不保存 MitM CA 或证书密码**。

默认版本以兼容性为优先：**IPv6 默认关闭，需要 IPv6 的用户自行在 Loon 中开启。**

---

## 快速开始

### 一键导入

[**点击这里直接添加到 Loon**](https://www.nsloon.com/openloon/import?sub=https%3A%2F%2Fraw.githubusercontent.com%2Fzosb%2FLoon-Auto-Select%2Fmain%2FLoon_Auto_Select.lcf)

导入配置后，在 Loon 中添加你自己的机场 / 节点订阅即可。

### Raw 配置地址

```text
https://raw.githubusercontent.com/zosb/Loon-Auto-Select/main/Loon_Auto_Select.lcf
```

### 快捷操作

- [**开启 Loon**](https://www.nsloon.com/openloon/on)
- [**更新全部订阅资源**](https://www.nsloon.com/openloon/update?sub=all)

部分 GitHub / Raw GitHub 资源在某些网络环境下直连更新可能不稳定。遇到资源更新失败时，可以先开启 Loon，再执行“更新全部订阅资源”。

---

## 仓库文件

| 文件 | 作用 |
| --- | --- |
| `Loon_Auto_Select.lcf` | Loon 主配置文件 |
| `README.md` | 使用说明、策略逻辑和注意事项 |
| `LICENSE` | 本项目及上游模板的许可说明 |
| `.gitignore` | 防止证书、私人配置和临时文件被误提交 |

---

## 默认行为

导入后，这份配置主要完成以下工作：

- 中国大陆常规流量自动 `DIRECT`
- 自动识别香港、澳门、台湾、日本、韩国、新加坡、美国、英国、德国节点
- 节点只按照国家 / 地区归类，不按倍率、实验性质或用途拆池
- 每个地区内部通过 `url-test` 自动选择较合适的节点
- Google、社交媒体、开发服务优先亚洲近邻地区，近邻全部异常时再扩大到主要地区池
- YouTube 使用“地区内测速 → 地区间测速 → 主要地区故障转移”三级结构
- AI 服务固定新加坡，使用 `fallback` 尽量保持稳定出口
- TikTok 固定韩国
- 国际流媒体与 Spotify 固定美国
- Telegram 默认香港优先，并按照后备顺序自动容灾
- 游戏平台根据延迟在港澳台日韩新美之间自动选择，并使用更高容差减少频繁切换
- 主要应用保留手动地区 / 节点接管入口
- 集成广告平台拦截、HTTPDNS 防绕过、DNS 防泄漏和少量国外常用 App 专项去广告
- 默认不绑定任何机场专属 DNS / Host 插件

整体原则是：

> **正常情况下全自动；有解锁、线路或出口要求时再手动接管。**

---

## 节点分类

节点 **只按照国家 / 地区归类**。

下列标签不会让节点被排除出普通地区池：

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

它仍然属于澳门，所以会进入 **澳门节点** 和 **澳门时延优选**。

地区筛选兼容常见中文、繁体、英文、国家代码、旗帜以及空格 / 连字符 / 下划线写法，例如：

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

如果某一家机场采用非常特殊的节点名称，只需要调整 `[Remote Filter]` 中相应地区的 `NameRegex`，不需要修改整套策略架构。

---

## 自动测速与故障检测

为了兼顾自动选路、稳定性和后台开销，配置采用分层周期：

| 类型 | 周期 | 作用 |
| --- | ---: | --- |
| 地区内部 `url-test` | 120 秒 | 在同一地区节点之间重新评估 |
| 近邻 / 主要地区 / YouTube / 游戏上层 `url-test` | 300 秒 | 在多个地区策略之间重新评估 |
| `fallback` | 60 秒 | 检测可用性并及时切换后备策略 |

普通测速使用：

```ini
interval = 120
tolerance = 50
```

游戏上层策略使用：

```ini
interval = 300
tolerance = 80
```

更高的 `tolerance` 用于减少几十毫秒的小幅波动造成的频繁切换。

### 近邻、主要地区与全球节点的区别

为了避免把机场里所有地区的节点都加入后台自动测速，配置把“自动测速范围”和“全部节点手动入口”分开：

```text
近邻时延优选
= 香港 / 澳门 / 台湾 / 日本 / 韩国 / 新加坡

主要地区时延优选
= 香港 / 澳门 / 台湾 / 日本 / 韩国 / 新加坡 / 美国 / 英国 / 德国

全球手动策略
= 全球节点中所有正常节点
```

因此 `主要地区时延优选` 并不代表机场的全部全球节点。加拿大、法国、荷兰、澳大利亚等其它地区如果存在，仍可通过 **全球手动策略** 使用，但不会默认加入周期性自动测速，从而控制后台请求和电量开销。

---

## 主要分流策略

| 服务 | 默认策略 |
| --- | --- |
| ChatGPT / Gemini / Claude / Copilot / Grok | 新加坡稳定出口 |
| TikTok | 韩国 |
| Telegram | 香港优先，按地区顺序自动容灾 |
| YouTube / YouTube Music | 港澳台日韩新美自动测速，异常时进入主要地区池 |
| Google | 亚洲近邻自动测速，异常时进入主要地区池 |
| X / Threads / Instagram / Facebook / WhatsApp / Reddit / Discord / LINE / Pinterest | 亚洲近邻自动测速，异常时进入主要地区池 |
| Netflix / Disney+ / Prime Video / Twitch / Apple TV+ | 美国 |
| Spotify | 美国 |
| GitHub / GitLab / Notion / Docker / NPM / Vercel | 亚洲近邻自动测速，异常时进入主要地区池 |
| Steam / Epic / PlayStation / Xbox | 港澳台日韩新美稳定型自动测速 |
| 其它未匹配流量 | `兜底后备策略` |

### AI

AI 服务固定新加坡：

```text
AI 服务
→ 新加坡节点
→ fallback 保持当前可用出口
```

正常情况下不会自动跨国。

ChatGPT、Gemini、Claude、Copilot、Grok 的策略组同时保留 **新加坡手动策略**，需要时可以指定具体新加坡节点。

### Telegram

默认后备顺序：

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

只要前面的地区可用，就不会主动使用后面的地区。

### YouTube

YouTube 使用三级结构：

```text
地区内部自动测速
→ 港澳台日韩新美之间自动测速
→ 常用地区全部异常时进入主要地区时延优选
```

YouTube 不是固定国家策略，而是优先选择当前更合适的常用地区。

### Google / 社交媒体 / 开发服务

默认近邻池为：

```text
香港 / 澳门 / 台湾 / 日本 / 韩国 / 新加坡
```

近邻池全部不可用时，才扩大到 **主要地区时延优选**，加入美国、英国、德国继续寻找可用线路。

这样可以避免正常情况下因为欧美节点偶尔快几毫秒就主动跨洲。

Pinterest 已纳入 `社交媒体` 远程分流规则，因此它与 X、Instagram、Reddit、LINE 等服务使用同一套近邻优先与故障转移逻辑。

### 国际流媒体与 Spotify

Netflix、Disney+、Amazon Prime Video、Twitch、Apple TV+ 和 USMedia 使用美国节点池。

Spotify 也默认固定美国。

固定地区的目的主要是减少频繁跨区引起的媒体库、内容可用性或账号环境变化。两类策略都保留 **美国手动策略**。

### 游戏平台

Steam、Epic、PlayStation、Xbox 不要求节点名称带有“游戏”标签。

候选地区：

```text
香港 / 澳门 / 台湾 / 日本 / 韩国 / 新加坡 / 美国
```

常用游戏地区全部不可用时，再进入 **主要地区时延优选** 进行故障转移。

---

## 手动接管

主要 App 策略组的第一项始终是默认自动策略。

通常不需要操作；遇到以下情况时再手动选择：

- 某个自动节点无法解锁服务
- 某条线路临时不稳定
- 需要固定某个国家 / 地区
- 需要指定具体节点或出口

恢复策略组第一项后，即重新交给自动逻辑。

---

## IPv6

### 默认关闭

公开默认版使用：

```ini
ip-mode = v4-only
ipv6-vif = off
```

默认行为：

- 只使用 IPv4
- 不主动使用 IPv6
- TUN IPv6 默认关闭

这样更适合作为公开通用配置，避免强制改变不同用户的本地网络栈。

### 需要 IPv6 时

进入：

```text
Loon → 配置 → 高级配置 → IP Stack
```

一般双栈网络可先使用：

```text
IP 查询模式：IPv4 & IPv6
TUN IPv6：自动
```

明确需要 IPv6 优先时：

```text
IP 查询模式：IPv6 优先
```

明确需要强制接管 TUN IPv6 时，再将：

```text
TUN IPv6：开启
```

“仅 IPv6”属于谨慎选项，不建议作为通用默认设置。

主配置已经预留以下 IPv6 本地 / 局域网绕过范围：

```text
::1/128
fc00::/7
fe80::/10
ff00::/8
```

---

## 去广告与插件

默认配置不会把可莉插件库中的所有去广告插件全部预装进来。

这是有意做的取舍：**默认版只保留少量常用国外 App 的专项去广告插件，保证配置精简、Rewrite / Script 范围可控，也降低 App 更新后出现兼容问题的概率。**

整体结构为：

```text
广告平台基础拦截
+ 少量国外常用 App 专项去广告
+ 必要工具插件
```

### 基础层

```text
BlockAdvertisers
Block_HTTPDNS
```

`BlockAdvertisers` 放在插件列表顶部，作为专项去广告插件的基础广告平台拦截层。

### 默认专项去广告 App

默认仅预装以下常用国外 App：

```text
YouTube
Spotify
Reddit
Pinterest
LINE
```

对应插件：

```text
YouTube_remove_ads.lpx
Spotify_remove_ads.lpx
Reddit_remove_ads.lpx
Pinterest_remove_ads.lpx
Line_remove_ads.lpx
```

这些是**默认内置项**，并不代表本配置只能给这些 App 添加去广告功能。

### 需要其它 App 去广告怎么办？

如果你还需要 X、Instagram、Facebook、TikTok，或者其它国内 / 国外 App 的专项去广告，可以直接前往 **可莉插件库** 自行选择并添加：

**可莉插件中心：** https://hub.kelee.one

建议只添加自己实际使用、确实有需求的插件，不建议一次性把插件库全部启用。专项插件越多，Rewrite / Script / MitM 的覆盖范围越大，长期维护和排查兼容问题也会更复杂。

因此本项目默认采用的原则是：

> **基础拦截默认开启；常用专项少量预装；其它 App 按需从可莉插件库自行添加。**

### YouTube

YouTube 专项去广告已默认开启。

网络层去广告会受到 YouTube 客户端和接口更新影响，不能保证任何版本永久有效。如果 YouTube 更新后出现广告恢复、播放异常或插件失效，优先更新插件；仍有问题时再临时关闭该插件排查。

### Spotify

Spotify 专项插件用于处理播放广告和部分界面广告内容。

> **去广告不等于 Premium 解锁。**

本配置不会把免费账号变成 Spotify Premium，也不承诺 Premium 专属功能。

### MitM

部分专项 Rewrite / Script 功能需要 HTTPS 解密。

公开仓库的 `[Mitm]` 始终保持为空：

```ini
hostname =
ca-p12 =
ca-passphrase =
skip-server-cert-verify = false
```

如果需要使用依赖 MitM 的专项功能，请在自己的 Loon 中生成、安装并信任个人 CA。

出现某个 App 异常时，优先关闭该 App 对应的专项插件进行排查，不建议第一时间修改整套分流配置。

---

## 工具插件

默认保留：

```text
QuickSearch
Prevent_DNS_Leaks
Node_detection_tool
BoxJs
Sub-Store
Script-Hub
```

它们与去广告职责分开，主要用于搜索、DNS 防泄漏、节点检测和资源管理。

---

## DNS

默认保持：

```ini
dns-server = system
```

没有强制添加额外 DoH / DoQ，以减少运营商、CDN、IPv4 / IPv6 以及不同网络环境之间的兼容性变量。

同时启用 `Block_HTTPDNS` 与 `Prevent_DNS_Leaks` 作为辅助保护。

为了保持公开配置的机场无关性，默认版**不再内置任何特定机场的 DNS / Host 插件**。如果你的机场明确要求使用专用 DNS、Host 或解析插件，请按照机场自己的说明在 Loon 中单独添加。

---

## 规则顺序

远程规则按照“越具体越靠前”的思路整理：

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

几个重要顺序：

- Gemini / AI 规则位于 Google 大规则之前
- YouTube 位于 Google 大规则之前
- SteamCN 位于 Steam 大规则之前
- Pinterest 与其它社交媒体规则位于同一层级
- `REGION_SPLITTER` 保持远程规则最后

---

## 常见问题

### 某个地区显示 `N/A`

通常代表当前订阅没有任何节点名称匹配到该地区。

现在的地区池不会因为 `2X`、实验、GAME、YouTube、无广等标签主动排除节点。

### 资源更新失败

部分 GitHub / Raw GitHub 资源在某些网络下直连可能不稳定。

可以尝试：

```text
先开启 Loon
→ 再更新全部订阅资源
```

### 某个 App 开启去广告后异常

先关闭对应 App 专项插件测试。

专项插件比基础分流规则更容易受客户端版本变化影响，所以不建议为了一个 App 的 Rewrite 问题去修改整个策略架构。

### 想给其它 App 添加去广告

默认版只预装 YouTube、Spotify、Reddit、Pinterest、LINE 等少量常用专项插件。

其它 App 请前往 **https://hub.kelee.one** 按需选择插件并自行添加，不建议把所有插件一次性启用。

### 我的机场要求专用 DNS / Host 插件

公开默认版不会绑定任何机场专属 DNS / Host 插件。

如果你的机场节点需要额外解析规则，请按照机场提供的说明自行添加；这样不会把某一家服务商的专属配置强加给其它使用者。

### IPv6 节点无法连接

公开默认版 IPv6 是关闭的。

如果节点服务器入口只有 IPv6，请按照上面的 **IPv6** 章节手动开启双栈或 IPv6 优先。

---

## 安全说明

这是公开仓库，请不要提交：

```text
私人机场订阅 URL
订阅 Token
用户名 / 密码
MitM CA
ca-p12
ca-passphrase
其它私人凭据
```

`Loon_Auto_Select.lcf` 中的 `[Remote Proxy]` 和 `[Mitm]` 会始终保持为公开安全模板。

---

## 上游与致谢

本项目基于 iKeLee / 可莉公开的 Loon Auto 配置模板进行整理和扩展。

- ProxyResource: https://github.com/luestr/ProxyResource
- ShuntRules: https://github.com/luestr/ShuntRules
- IconResource: https://github.com/luestr/IconResource
- Qure Icons: https://github.com/Koolson/Qure
- 可莉插件中心: https://hub.kelee.one

原始模板作者：**iKeLee** — https://t.me/iKeLee

本仓库并非 iKeLee / 可莉官方项目，也不代表上述项目对本仓库的背书。

---

## License

本仓库按照上游模板许可要求采用：

**Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International**

即：**CC BY-NC-SA 4.0**。

详情请查看 [LICENSE](./LICENSE)。
