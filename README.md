# Evan Shadowrocket v1

一份基于 [LingJingMaster/Shadowrocket-Rules](https://github.com/LingJingMaster/Shadowrocket-Rules) 定制的 Shadowrocket 分流配置。

设计目标：已识别的中国大陆域名、目标 IP 和国内 App HTTPDNS 强制直连，AI 使用稳定出口，普通海外服务自动选择节点。

2026-09-14 版本：包含 Telegram 修复及国内强制直连变更。静态检查已通过，更新后的手机导入与实际应用回归仍需验证。

## 直接导入

配置地址：

```text
https://raw.githubusercontent.com/evanwy0223/evan-shadowrocket-config/main/Evan-Shadowrocket-v1.conf
```

在 iPhone 或 iPad 上操作：

1. 复制上面的配置地址。
2. 打开 Shadowrocket，进入底部「配置」。
3. 点击右上角 `+`，粘贴配置地址并下载。
4. 点击 `Evan-Shadowrocket-v1.conf`，使其成为当前配置。
5. 返回首页，把「全局路由」设为「配置」。
6. 打开 Shadowrocket 开关。

也可以直接下载仓库中的 `Evan-Shadowrocket-v1.conf`，通过 iOS「文件」App 分享给 Shadowrocket 导入。

## 配置订阅与自动更新

配置内置以下更新地址，只跟随本仓库：

```text
https://raw.githubusercontent.com/evanwy0223/evan-shadowrocket-config/main/Evan-Shadowrocket-v1.conf
```

首次使用旧版或本地文件版配置时，需要最后一次通过上面的 Raw URL 重新导入，让 Shadowrocket 记录远程更新地址。以后不需要反复下载文件。

手动更新：

1. 打开 Shadowrocket 底部「配置」。
2. 找到当前使用的 `Evan-Shadowrocket-v1.conf`。
3. 进入配置详情并选择「更新配置」。

自动更新：

1. 打开 Shadowrocket「设置 → 订阅 → 自动更新」中的「配置」。
2. 开启「自动后台更新」，更新间隔可设为 1 天。
3. 建议开启「更新提醒」。
4. 在 iOS「设置 → 通用 → 后台 App 刷新」中允许 Shadowrocket。

配置更新会用 GitHub 上的完整文件覆盖手机中的配置内容。更新后请检查美国固定、香港固定、Google 服务和电报消息；节点订阅本身仍由机场订阅单独更新。

## 首次设置

导入配置不会包含机场订阅或私人节点，需要先在 Shadowrocket 中添加自己的节点或订阅。

进入首页并向下拉，打开「代理分组」，然后完成以下设置：

1. `🇺🇸 美国固定`：选择一个长期稳定的具体美国节点。
2. `🇭🇰 香港固定`：如使用香港银行或券商，选择一个长期稳定的具体香港节点。
3. `🚀 节点选择`：通常保持 `♻️ 自动选择`；需要固定出口时可切换为固定节点组。
4. `🔍 谷歌服务`：默认使用 `🇺🇸 美国自动`。本配置在实际使用中发现部分 GAME 日本节点无法访问 Google，因此不再默认选择日本自动。

如果固定分组是空的，说明订阅的节点名称未包含配置能够识别的地区关键词，例如 `美国`、`US`、`香港`、`HK`、`日本` 或 `JP`。

## 默认分流

| 场景 | 默认策略 |
| --- | --- |
| ChatGPT、Claude、Grok、Perplexity | `🇺🇸 美国固定` |
| Gemini、NotebookLM、Google AI Studio | `🇺🇸 美国固定` |
| Google 普通服务 | `🇺🇸 美国自动` |
| X / Twitter 及图片、视频 | `🇺🇸 美国固定` |
| YouTube | `♻️ 自动选择` |
| GitHub、GitLab、Atlassian、GitHub Copilot | `♻️ 自动选择` |
| Telegram 文字、图片、视频 | `🇺🇸 美国固定` |
| 京东及图片/CDN | `DIRECT` |
| 微信、飞书、小红书、抖音、豆包 | `DIRECT` |
| Apple、iCloud | `DIRECT` |
| Apple Push | `♻️ 自动选择` |
| Mail、Apple Mail | `DIRECT`，可手动切代理 |
| 汇丰香港、券商 | `🇭🇰 香港固定` |
| 其他香港银行 | `DIRECT` |
| 普通海外网站 | `♻️ 自动选择` |

固定组使用 `select`，由用户手动选择具体节点，不会自动跳换出口 IP。自动组使用 `url-test`，每 600 秒测试一次，并设置 100 ms 容差，避免只因很小的延迟差异频繁切换。

## 国内核心规则

- China 域名规则与 `GEOIP,CN,DIRECT,no-resolve` 置于海外服务之前；国内业务规则直接指定 `DIRECT`，不依赖策略组保存的节点选择。
- 原 BlockHttpDNS 列表改为 `DIRECT`，不再拦截其中的国内 App 自有 DNS；这是兼容性优先的选择，会放松这部分应用内 DNS 防泄露限制。
- `💬 国内核心`、`🔒 国内服务` 仅保留 `DIRECT`，没有代理或拒绝选项。
- 国内直连域名使用系统 DNS，解析失败不回退代理；General、IPv6、QUIC、Host、URL Rewrite 和 MITM 未修改。
- “国内”指现有规则识别的中国大陆业务域名和大陆 IP，不包括港澳台服务；港股券商、香港银行仍保留原策略。
- 未收录域名、使用海外 IP 的国内业务及共享 CDN 无法仅凭 GeoIP 保证识别，可能仍走海外兜底；必须结合手机连接日志补充精确规则。不要把整个 FINAL 改为 DIRECT。
- 微信使用 blackmatrix7 的 WeChat 远程规则集。
- Mail 和 Apple Mail 使用 blackmatrix7 的 Mail、AppleMail 远程规则集。
- 飞书、小红书、抖音和豆包使用显式域名规则。
- 不整包引用 ByteDance 规则集，避免其中的 TikTok 用户代理规则被错误直连。

## 常见问题

### Google 或 Chrome 无法访问

1. 进入 `🔍 谷歌服务`，选择 `🇺🇸 美国自动`。
2. 点击右上角「测试」，确认美国自动组有正常延迟。
3. 完全退出 Chrome 后重新打开。
4. 仍不正常时，临时选择 `PROXY`，并让首页 Proxy 使用一个确认可用的节点。
5. 最后可把「全局路由」临时改为「代理」做对照测试：全局代理可用而配置模式不可用，说明是策略选择问题；全局代理也不可用，则优先检查节点或订阅。

### AI 服务频繁验证或掉线

进入 `🇺🇸 美国固定`，选择一个具体美国节点并保持使用。不要为了几十毫秒的延迟差异频繁更换出口 IP。

### X / Twitter 图片加载慢

X 使用独立的 `🐦 X 服务`，覆盖 `x.com`、`twitter.com`、`twimg.com`、视频域名和相关 IP，默认走 `🇺🇸 美国固定`。该选择来自实际线路测试：普通海外自动组选择香港节点时图片较慢，切换至美国 DMIT 节点后图片加载明显恢复。

如果以后当前美国固定节点变慢，可在 `🐦 X 服务` 中依次测试 `🇺🇸 美国自动` 或 `PROXY`，无需改变整个 `🌍 非中国` 策略。

### Telegram 图片或视频加载慢

Telegram 使用独立的 `📲 电报消息` 策略，默认走 `🇺🇸 美国固定`。2026-08-31 真机对比发现：全节点自动组选中 `[0.3]香港③` 时图片和视频明显较慢，切换到美国 `DMIT-VLESS-REALITY` 后明显变快。这说明普通 204 延迟最低不等于 Telegram 媒体线路吞吐最好。

如果当前美国固定节点变慢，可在 `📲 电报消息` 中依次测试 `🇺🇸 美国自动`、`PROXY` 或其他地区自动组，不影响普通海外流量。

### 美国固定、香港固定或日本固定为空

固定组通过节点名称中的地区关键词筛选。如果机场使用了不常见命名，需要在配置文件 `[Proxy Group]` 中补充对应的 `policy-regex-filter`。

### 国内 App 异常

确认全局路由为「配置」，而不是「代理」；全局代理模式会绕过本稿的国内直连规则。不要把整个 ByteDance 规则集设置为直连。

国内域名、IP 与 HTTPDNS 已改为强制直连，但这不代表所有 App 断网已修复。如果某个 App 仍断网，先在连接日志确认实际请求使用 `DIRECT`、命中规则及失败原因；未识别的国内业务需补规则，已直连仍失败则检查本地网络或 App 服务，不再盲目调整代理节点。

### 邮件客户端无法收发邮件

默认先使用 `DIRECT`。如果当前网络无法直连海外邮件服务器，可把 `✉️ 邮件服务` 临时切换为 `🚀 节点选择` 或 `PROXY`。

## 更新原则

配置中的 `update-url` 已指向本仓库，不会自动使用 LingJingMaster 的完整配置覆盖本版本。远程 `RULE-SET` 仍会从 LingJingMaster 和 blackmatrix7 的仓库获取规则。

更新本仓库后，在 Shadowrocket 的配置详情中执行「更新配置」即可。远程更新可能会重置部分策略组的手动选择，更新后请检查美国固定、香港固定、Google 服务和电报消息。

## 基础检查

发布前检查内容包括：

- 必需配置区块存在且没有重复。
- 策略组名称与规则引用一致。
- Mail、AppleMail 和 WeChat 远程规则引用存在。
- `update-url` 只指向本仓库的 Raw 配置地址。
- 未引用整个 ByteDance 规则集。

最终兼容性仍以 Shadowrocket 真机导入和实际网络测试为准。

## 来源与说明

- 底版：[LingJingMaster/Shadowrocket-Rules](https://github.com/LingJingMaster/Shadowrocket-Rules)
- 通用规则集：[blackmatrix7/ios_rule_script](https://github.com/blackmatrix7/ios_rule_script)

本仓库只包含分流配置，不包含订阅地址、服务器、密码、证书或其他私人连接信息。
