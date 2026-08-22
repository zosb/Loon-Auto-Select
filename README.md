# Loon Auto Select

一个基于 **iKeLee / 可莉 Loon Auto** 配置模板定制的长期自动分流配置，适用于常见机场 / 代理订阅节点，并不限定某一家服务商。

## 一键添加到 Loon

[**点击这里直接添加到 Loon**](https://www.nsloon.com/openloon/import?sub=https%3A%2F%2Fraw.githubusercontent.com%2Fzosb%2FLoon-Auto-Select%2Fmain%2FLoon_Auto_Select.lcf)

> 该按钮使用 Loon 官方远程配置导入 URL Scheme。点击后会打开 Loon 并导入本仓库的 `Loon_Auto_Select.lcf`。配置本身不包含任何私人机场订阅，请在导入后自行添加自己的节点订阅。

Raw 配置地址：

`https://raw.githubusercontent.com/zosb/Loon-Auto-Select/main/Loon_Auto_Select.lcf`

## 主要特性

- 中国大陆流量自动 `DIRECT`
- 香港、澳门、台湾、日本、韩国、新加坡、美国、英国、德国节点自动筛选
- 国家 / 地区内部通过 `url-test` 自动选择较快节点
- AI 固定新加坡，并使用 `fallback` 尽量保持稳定出口 IP
- TikTok 固定韩国，韩国内部自动优选
- 国际流媒体固定美国，美国内部自动优选
- YouTube 专用 / 无广节点优先
- 游戏、实验、YouTube 专线、高倍率节点与普通流量池隔离
- 支持机场自定义 DNS；当前配置额外包含 Crush Cloud 节点域名的专用 DNS 插件，该插件不会限制其它机场使用
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
3. 导入 Loon 后更新远程规则、插件和节点资源。
4. 如需使用依赖 HTTPS Rewrite / Script 的专项去广告插件，请在 Loon 本地生成、安装并信任你自己的 MitM CA。

> **安全提示：** 本仓库不会包含任何私人机场订阅 URL、Token、MitM CA、证书密码或其它凭据。请勿将这些内容提交到公开仓库。

## 机场兼容性

本配置**不绑定 Crush Cloud，也不限定任何机场品牌**。

区域节点通过节点名称进行筛选，例如香港、日本、新加坡、美国等常见中文名、英文名、国家代码及旗帜 Emoji。绝大多数采用常见命名方式的机场订阅都可以直接使用。

如果某个机场使用非常特殊的节点命名方式，只需要调整 `[Remote Filter]` 中对应地区的 `NameRegex`，无需修改整套分流架构。

Crush Cloud DNS 插件只针对其特定节点域名提供解析，不会接管全局 DNS，也不会影响其它机场节点正常使用。

## 策略说明

### AI

ChatGPT、Gemini、Claude、Copilot、Grok 等 AI 服务固定使用新加坡节点池，并通过 `fallback` 尽量保持同一出口节点；仅在当前节点不可用时自动切换。

### TikTok

固定韩国地区，韩国节点池内部使用 `url-test` 自动选择较快节点。

### 国际流媒体

Netflix、Disney+、Amazon Prime Video、Twitch、Apple TV+ 以及 `USMedia` 规则统一使用美国地区，美国节点池内部自动优选。

### YouTube

优先使用节点名称中含 `YouTube` / `油管` 的专用节点；专用节点池不可用时再按地区策略自动容灾。

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
