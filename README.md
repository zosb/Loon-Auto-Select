# Loon Auto Select

一个基于 **iKeLee / 可莉 Loon Auto** 配置模板定制的长期自动分流配置，适用于常见机场 / 代理订阅节点，并不限定某一家服务商。

## 一键添加到 Loon

[**点击这里直接添加到 Loon**](https://www.nsloon.com/openloon/import?sub=https%3A%2F%2Fraw.githubusercontent.com%2Fzosb%2FLoon-Auto-Select%2Fmain%2FLoon_Auto_Select.lcf)

> 点击后会打开 Loon 并导入本仓库的 `Loon_Auto_Select.lcf`。公开配置不包含任何私人机场订阅，请在导入后自行添加自己的节点订阅。

Raw 配置地址：

`https://raw.githubusercontent.com/zosb/Loon-Auto-Select/main/Loon_Auto_Select.lcf`

## 快捷操作

- [**开启 Loon**](https://www.nsloon.com/openloon/on)
- [**更新全部订阅资源**](https://www.nsloon.com/openloon/update?sub=all)

> 以上使用 Loon 官方 Universal Link。部分 GitHub / Raw GitHub 资源在某些网络环境下直连可能不稳定，可以先开启 Loon，再执行“更新全部订阅资源”。

## 主要特性

- 中国大陆流量自动 `DIRECT`
- 香港、澳门、台湾、日本、韩国、新加坡、美国、英国、德国节点自动筛选
- **节点只按国家 / 地区归类**，不再按高倍率、实验、测试、游戏、YouTube、无广等标签拆分节点池
- 地区筛选兼容常见中文 / 繁体 / 英文 / 国家代码 / 旗帜，以及 `HK-01`、`HK_01`、`US-02` 等常见命名
- 国家 / 地区内部通过 `url-test` 自动选择较快节点
- 地区内部测速周期为 120 秒，跨地区测速周期为 300 秒；`fallback` 保持 60 秒故障检测，减少不必要的后台测速和电量开销
- **IPv6 默认关闭**：公开默认版使用 `ip-mode = v4-only`、`ipv6-vif = off`；需要 IPv6 的用户请在 Loon 中自行手动开启
- IPv6 本地 / 局域网绕过范围已预留，手动启用 IPv6 后可直接使用
- AI 固定新加坡，并使用 `fallback` 尽量保持稳定出口 IP
- TikTok 固定韩国，韩国内部自动优选
- 国际流媒体固定美国，美国内部自动优选
- Google / 社交 / 开发使用“近邻测速 + 全球故障转移”双层容灾
- YouTube 使用“地区内测速 + 地区间测速 + 全球故障转移”三级结构
- 游戏平台覆盖香港、澳门、台湾、日本、韩国、新加坡、美国，并使用较高 `tolerance` 降低频繁切换
- Telegram 保持香港优先，并补全韩国、美国、英国、德国后备链
- 主要 App 策略默认全自动，同时保留手动地区 / 节点接管入口
- 开发服务覆盖 GitHub、GitLab、Notion、Docker、NPM、Vercel
- HTTPDNS 防绕过与 DNS 防泄漏
- 通用广告拦截 + 常用 App 专项去广告插件
- 国家 / 地区图标使用 Qure，应用图标使用 IconResource

## 使用方法

### 方法一：一键导入

直接点击上面的 **“点击这里直接添加到 Loon”**，即可调用 Loon 导入远程配置。

导入后，在 Loon 中添加你自己的机场 / 节点订阅即可。只要节点名称能够匹配配置中的国家 / 地区筛选规则，就可以参与自动分流和测速优选。

### 方法二：手动导入

1. 下载或复制 `Loon_Auto_Select.lcf`。
2. 在 `[Remote Proxy]` 中填入你自己的机场订阅，或直接在 Loon 中添加节点订阅。
3. 开启 Loon 后更新远程规则、插件和节点资源；部分 GitHub / Raw GitHub 资源在某些网络环境下直连可能不稳定。
4. 如需使用依赖 HTTPS Rewrite / Script 的专项去广告插件，请在 Loon 本地生成、安装并信任你自己的 MitM CA。

> **安全提示：** 本仓库不会包含任何私人机场订阅 URL、Token、MitM CA、证书密码或其它凭据。请勿将这些内容提交到公开仓库。

## 机场兼容性

本配置不绑定任何机场品牌。

区域节点通过节点名称进行筛选，例如香港、日本、新加坡、美国等常见中文名、英文名、国家代码及旗帜 Emoji。当前筛选同时兼容常见空格、连字符和下划线命名，例如 `Hong Kong`、`HongKong`、`HK-01`、`HK_01`、`South Korea`、`United_States`、`USA-02` 等。

节点名称中的 `2X`、`实验`、`GAME`、`YouTube`、`无广` 等附加标签不会把它们排除出所属地区节点池。

如果某个机场使用非常特殊的节点命名方式，只需要调整 `[Remote Filter]` 中对应地区的 `NameRegex`，无需修改整套分流架构。

配置中包含的特定节点域名 DNS 插件只处理其对应域名，不会接管全局 DNS，也不会限制其它机场使用。

## IPv6（默认关闭）

为了让公开版本对更多网络环境和机场订阅保持兼容，默认配置**不主动开启 IPv6**：

```ini
ip-mode = v4-only
ipv6-vif = off
```

也就是说，默认只使用 IPv4，不主动查询 AAAA，并关闭 TUN 的 IPv6 流量处理。**如果你的机场节点、服务器入口或实际网络环境需要 IPv6，请自行在 Loon 中手动开启。**

进入 **Loon → 配置 → IP Stack** 后，可根据自己的环境选择：

- 一般双栈需求：将“IP 查询模式”改为 **IPv4 & IPv6**，TUN IPv6 可先选择 **自动**；
- 明确需要 IPv6 优先：将“IP 查询模式”改为 **IPv6 优先**；
- 明确需要强制接管 TUN IPv6：再把“TUN IPv6 配置”改为 **开启**；
- **仅 IPv6** 属于谨慎选项，不建议普通用户默认使用。

修改 IP Stack 后 Loon 会重启 VPN。配置中已经预留 `::1/128`、`fc00::/7`、`fe80::/10`、`ff00::/8` 等 IPv6 本地 / 局域网绕过范围，因此开启 IPv6 后通常无需再额外修改这些范围。

> 公开版保持 IPv6 默认关闭的原则：**需要的人自己开启，不需要的人不被强制改变网络栈。**

## 测速与故障检测

为了在自动选路和后台开销之间取得更好的平衡，当前使用分层周期：

- 地区内部 `url-test`：`interval = 120`
- 近邻 / 全球 / YouTube / 游戏上层 `url-test`：`interval = 300`
- `fallback` 故障检测：继续保持 `interval = 60`

`tolerance` 仍用于减少轻微延迟变化造成的频繁切换；游戏策略使用更高的 `tolerance = 80`。

## 策略说明

### AI

ChatGPT、Gemini、Claude、Copilot、Grok 等 AI 服务固定使用新加坡节点池，并通过 `fallback` 尽量保持同一出口节点；仅在当前节点不可用时自动切换。App 策略同时提供 `新加坡手动策略`，方便在需要时手动指定具体新加坡节点，但不会自动跨国。

### TikTok

固定韩国地区，韩国节点池内部使用 `url-test` 自动选择较快节点，同时保留 `韩国手动策略` 作为人工接管入口。

### 国际流媒体

Netflix、Disney+、Amazon Prime Video、Twitch、Apple TV+ 以及 `USMedia` 规则统一使用美国地区，美国节点池内部自动优选。为避免媒体库跨区变化，自动策略不会故障转移到其它国家，只提供 `美国手动策略`。

### 近邻自动测速与故障转移

Google、社交媒体、开发服务共用香港、澳门、台湾、日本、韩国、新加坡组成的近邻策略池。每个地区先在本地区自动测速，再由 `近邻时延优选` 在地区之间选择更优策略。

在整个近邻池不可用时，`近邻故障转移` 才会自动扩大到 `全球时延优选`，从美国、英国、德国等地区继续寻找可用策略。正常情况下不会因为欧美节点偶尔延迟更低而主动跨洲。

### YouTube

YouTube 使用三级结构：

1. 每个地区内部通过 `url-test` 选择较快节点；
2. `YouTube时延优选` 在香港、澳门、台湾、日本、韩国、新加坡、美国之间选择更优地区；
3. 上述常用地区全部不可用时，`YouTube故障转移` 才扩大到全球池。

同时保留各地区手动策略，可以在解锁、线路或特殊节点需求下临时接管。

### Telegram

Telegram 默认仍然香港优先，自动后备顺序为：香港 → 澳门 → 台湾 → 日本 → 新加坡 → 韩国 → 美国 → 英国 → 德国。前面的地区正常时不会切到后面的地区，同时提供各地区手动策略与全球手动策略。

### 游戏

Steam、Epic、PlayStation、Xbox 等游戏平台不依赖“游戏节点”命名。`游戏时延优选` 覆盖香港、澳门、台湾、日本、韩国、新加坡、美国，并使用 `tolerance = 80` 减少轻微延迟变化造成的频繁切换。常用游戏地区全部不可用时，再由 `游戏故障转移` 扩大到全球池。

### 手动接管

主要 App 策略组的第一项始终是自动策略，所以正常使用无需人工干预。需要处理节点解锁异常、某条线路临时不稳定或指定出口时，可以进入对应 App 策略组临时选择地区手动策略；恢复第一项即可重新交给自动逻辑。

### 开发服务

开发服务统一走“近邻优选 + 全球故障转移”，覆盖 GitHub、GitLab、Notion、Docker、NPM 与 Vercel。

## 上游与致谢

本项目基于 iKeLee / 可莉公开的 Loon Auto 配置模板进行修改与扩展：

- ProxyResource: https://github.com/luestr/ProxyResource
- ShuntRules: https://github.com/luestr/ShuntRules
- IconResource: https://github.com/luestr/IconResource
- Qure Icons: https://github.com/Koolson/Qure

原始模板作者：**iKeLee** — https://t.me/iKeLee

本仓库并非 iKeLee / 可莉官方项目，也不代表上述项目对本仓库的背书。

## License

基于上游模板的许可要求，本仓库采用 **Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International (CC BY-NC-SA 4.0)**。

详见 [LICENSE](./LICENSE)。