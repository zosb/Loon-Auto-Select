# Loon Auto Select

一个基于 **iKeLee / 可莉 Loon Auto** 配置模板定制的长期自动分流配置，面向 Loon 日常使用与 Crush Cloud 节点环境。

## 主要特性

- 中国大陆流量自动 `DIRECT`
- 香港、澳门、台湾、日本、韩国、新加坡、美国、英国、德国节点自动筛选
- 国家/地区内部通过 `url-test` 自动选择较快节点
- AI 固定新加坡，并使用 `fallback` 尽量保持稳定出口 IP
- TikTok 固定韩国，韩国内部自动优选
- 国际流媒体固定美国，美国内部自动优选
- YouTube 专用/无广节点优先
- 游戏、实验、YouTube 专线、高倍率节点与普通流量池隔离
- Crush Cloud 专用 DNS 插件
- HTTPDNS 防绕过与 DNS 防泄漏
- 通用广告拦截 + 常用 App 专项去广告插件
- 国家/地区图标使用 Qure，应用图标使用 IconResource

## 使用方法

1. 下载或复制 `Loon_Auto_Select.lcf`。
2. 在 `[Remote Proxy]` 中填入你自己的机场订阅行。
3. 导入 Loon 后更新远程规则、插件和节点资源。
4. 如需使用依赖 HTTPS Rewrite/Script 的专项去广告插件，请在 Loon 本地生成、安装并信任你自己的 MitM CA。

> **安全提示：** 本仓库不会包含任何私人机场订阅 URL、Token、MitM CA、证书密码或其它凭据。请勿将这些内容提交到公开仓库。

## 策略说明

### AI

ChatGPT、Gemini、Claude、Copilot、Grok 等 AI 服务固定使用新加坡节点池，并通过 `fallback` 尽量保持同一出口节点；仅在当前节点不可用时自动切换。

### TikTok

固定韩国地区，韩国节点池内部使用 `url-test` 自动选择较快节点。

### 国际流媒体

Netflix、Disney+、Amazon Prime Video、Twitch、Apple TV+ 以及 `USMedia` 规则统一使用美国地区，美国节点池内部自动优选。

### YouTube

优先使用机场名称中含 `YouTube` / `油管` 的专用节点；专用节点池不可用时再按地区策略自动容灾。

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
