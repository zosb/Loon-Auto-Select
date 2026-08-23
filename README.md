# Loon Auto Select

一个基于 **iKeLee / 可莉 Loon Auto** 配置模板定制的长期自动分流配置，适用于常见机场 / 代理订阅节点，并不限定某一家服务商。

## 一键添加到 Loon

[**点击这里直接添加到 Loon**](https://www.nsloon.com/openloon/import?sub=https%3A%2F%2Fraw.githubusercontent.com%2Fzosb%2FLoon-Auto-Select%2Fmain%2FLoon_Auto_Select.lcf)

> 点击后会打开 Loon 并导入本仓库的 `Loon_Auto_Select.lcf`。公开配置不包含任何私人机场订阅，请在导入后自行添加自己的节点订阅。

Raw 配置地址：

`https://raw.githubusercontent.com/zosb/Loon-Auto-Select/main/Loon_Auto_Select.lcf`

## 主要特性

- 中国大陆流量自动 `DIRECT`
- 香港、澳门、台湾、日本、韩国、新加坡、美国、英国、德国节点自动筛选
- **节点只按国家 / 地区归类**，不再按高倍率、实验、测试、游戏、YouTube、无广等标签拆分节点池
- 国家 / 地区内部通过 `url-test` 自动选择较快节点
- IPv6 优先：`ip-mode = ipv6-preferred`，并使用 `ipv6-vif = always`
- AI 固定新加坡，并使用 `fallback` 尽量保持稳定出口 IP
- TikTok 固定韩国，韩国内部自动优选
- 国际流媒体固定美国，美国内部自动优选
- Google / 社交 / 开发共用近邻自动测速池，已纳入韩国
- YouTube 使用两层 `url-test`，地区内和地区间都自动测速
- 游戏平台使用稳定型 `url-test`，以较高 `tolerance` 减少轻微延迟波动导致的切换
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

区域节点通过节点名称进行筛选，例如香港、日本、新加坡、美国等常见中文名、英文名、国家代码及旗帜 Emoji。节点名称中的 `2X`、`实验`、`GAME`、`YouTube`、`无广` 等附加标签不会把它们排除出所属地区节点池。

如果某个机场使用非常特殊的节点命名方式，只需要调整 `[Remote Filter]` 中对应地区的 `NameRegex`，无需修改整套分流架构。

配置中包含的特定节点域名 DNS 插件只处理其对应域名，不会接管全局 DNS，也不会限制其它机场使用。

## 策略说明

### AI

ChatGPT、Gemini、Claude、Copilot、Grok 等 AI 服务固定使用新加坡节点池，并通过 `fallback` 尽量保持同一出口节点；仅在当前节点不可用时自动切换。

### TikTok

固定韩国地区，韩国节点池内部使用 `url-test` 自动选择较快节点。

### 国际流媒体

Netflix、Disney+、Amazon Prime Video、Twitch、Apple TV+ 以及 `USMedia` 规则统一使用美国地区，美国节点池内部自动优选。

### 近邻自动测速

Google、社交媒体、开发服务共用香港、澳门、台湾、日本、韩国、新加坡组成的近邻策略池。每个地区先在本地区自动测速，再由上层 `url-test` 在地区之间选择更优策略。

### YouTube

YouTube 使用两层自动测速：香港、澳门、台湾、日本、韩国、新加坡、美国各自先选择本地区较快节点，再由 `YouTube时延优选` 在这些地区策略之间自动选择较优结果，不再采用固定地区顺序的 `fallback`。

### 游戏

Steam、Epic、PlayStation、Xbox 等游戏平台不依赖“游戏节点”命名，直接在香港、台湾、日本、韩国、新加坡、美国地区策略之间进行 `url-test`。游戏策略使用较高的 `tolerance = 80`，用于减少轻微延迟变化造成的频繁切换。

### 开发服务

开发服务统一走近邻自动测速，覆盖 GitHub、GitLab、Notion、Docker、NPM 与 Vercel。

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
