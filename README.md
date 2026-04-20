# Clash Party With Smart Config Kit

基于 Mihomo Smart 内核思路整理的一套 Clash 覆写配置包，目标是把机场订阅快速整理成更可用的地区组和业务组，并补齐 DNS、Sniffer、GeoX 等客户端侧 Mixin 配置。

这个仓库适合两类人：

- 想在 `Mihomo Party` / `Clash Verge Rev` 上快速导入一套可工作的 Smart 配置。
- 已经有自己的机场订阅，但希望按地区和业务场景重新分组，而不是直接使用机场原始分组。

当前仓库内容是一个可直接落地的公开版本，也保留了继续按个人需求二次修改的空间。

## 适用客户端

- `Mihomo Party`：推荐，原生支持 JavaScript 覆写。
- `Clash Verge Rev`：支持 JS 覆写和内核 Mixin。
- `Clash Nyanpasu`
- 其他支持 Mihomo JavaScript 覆写引擎的客户端

## 仓库内容

| 文件                                                                      | 作用                                                  |
| ------------------------------------------------------------------------- | ----------------------------------------------------- |
| `clash-smart-v5.2.2.js`                                                   | 主覆写脚本，负责重写 `proxies / proxy-groups / rules` |
| `CONFIG_STEPS.md`                                                         | 完整使用说明，包括导入、Mixin 配置、验证、FAQ         |
| `Step-1.png` `Step-2.jpg` `pic_1.jpg` `pic_2.jpg` `pic_3.jpg` `pic_4.jpg` | 操作截图和配置参考                                    |

## 配置能力概览

这套配置的核心思路是：

- 支持单机场订阅，也更适合 `Sub-Store` 多机场聚合后的 Mihomo 订阅。
- 自动剔除信息类节点和高倍率节点。
- 自动按地区、城市、IATA、ISO 等关键词分类。
- 生成 `9` 个 Smart 区域组和 `28` 个业务策略组。
- 配套 `373+ rule-providers` 的规则体系。

😏 按 `CONFIG_STEPS.md` 的说明，配置完成后你应该能看到：

- 9 个区域组：全球、香港、台湾、日韩、亚太、美国、欧洲、美洲、非洲
- 28 个业务组：AI 服务、加密货币、Netflix、Disney+、YouTube、Telegram 等

## 快速开始

### 1. 安装客户端

优先推荐 `Mihomo Party`。

- Mihomo Party: `https://github.com/mihomo-party-org/mihomo-party/releases`
- Clash Verge Rev: `https://github.com/clash-verge-rev/clash-verge-rev/releases`

如果你使用 `Clash Verge Rev`，需要确认内核切到 `Mihomo Alpha`。

### 2. 准备订阅

你可以直接使用单机场订阅，但更推荐：

1. 使用 `Sub-Store` 聚合 2 个及以上机场。
2. 输出一个 `Clash (Mihomo)` 格式订阅链接。
3. 把这个聚合订阅添加到客户端。

这样脚本的自动分组能力才能发挥得更完整。

### 3. 导入 JS 覆写脚本

主脚本文件：

```text
clash-smart-v5.2.2.js
```

`Mihomo Party` 大致流程：

1. 打开 `覆写 / Override`
2. 新建一个 `JavaScript (.js)` 覆写
3. 名称可设为 `Clash Smart v5.2.2`
4. 打开 `Global`
5. 把 `clash-smart-v5.2.2.js` 全部内容粘贴进去
6. 保存后，将这个覆写绑定到你的订阅
7. 切换到该订阅并连接

`Clash Verge Rev` 大致流程：

1. 新建本地脚本
2. 粘贴 `clash-smart-v5.2.2.js`
3. 在订阅扩展管理里启用该脚本
4. 重启内核

完整带截图的步骤请看 [CONFIG_STEPS.md](./CONFIG_STEPS.md)。

## 还需要粘贴的 Mixin 配置

JavaScript 覆写主要处理：

- `proxies`
- `proxy-groups`
- `rules`

但以下内容仍需你在客户端侧额外粘贴到 `Mixin / 覆盖字段`：

- `GeoX URL`
- `DNS`
- `Sniffer`

这些完整内容已经整理在 [CONFIG_STEPS.md](./CONFIG_STEPS.md) 里，可直接复制粘贴。

如果你只想知道结论，至少要确保客户端里补齐以下三部分：

```yaml
geox-url:
  geoip: https://fastly.jsdelivr.net/gh/Loyalsoldier/geoip@release/geoip.dat
  mmdb: https://fastly.jsdelivr.net/gh/Loyalsoldier/geoip@release/Country.mmdb
  asn: https://fastly.jsdelivr.net/gh/Loyalsoldier/geoip@release/GeoLite2-ASN.mmdb
  geosite: https://fastly.jsdelivr.net/gh/MetaCubeX/meta-rules-dat@release/geosite.dat
geo-auto-update: true
```

```yaml
dns:
  use-hosts: false
  use-system-hosts: false
  respect-rules: true
```

```yaml
sniffer:
  enable: true
  parse-pure-ip: true
  force-dns-mapping: true
  override-destination: true
```

## 使用后的验证方法

配置生效后，可以这样检查：

1. 在 `Proxies` 页面确认是否出现 9 个区域组和 28 个业务组。
2. 在 `Connections` 页面测试典型站点。
3. 在 `Rules` 页面确认规则数量大于等于 `963`。
4. 在 `Logs` 页面确认没有明显的 `parse error`、`list not found` 或持续 DNS 失败。

推荐验证样例：

- `chat.openai.com` 应命中 `AI 服务`
- `www.netflix.com` 应命中 `Netflix`
- `www.bilibili.com` 应命中 `国内流媒体 / DIRECT`

## 推荐的业务组选项

首次导入后，建议为关键业务组指定一个默认上游：

| 业务组               | 推荐                                    |
| -------------------- | --------------------------------------- |
| `🤖 AI 服务`         | `🇺🇸 美国节点`                           |
| `💰 加密货币`        | `🇭🇰 香港节点`                           |
| `🏦 金融支付`        | `DIRECT`                                |
| `📧 邮件服务`        | `🇯🇵 日韩节点`                           |
| `💬 即时通讯`        | `🇭🇰 香港 / 🇯🇵 日韩`                     |
| `📺 国内流媒体`      | `DIRECT`                                |
| `🇺🇸 美国流媒体`      | `🇺🇸 美国节点`                           |
| `☁️ 云与 CDN`        | `🌍 全球节点`                           |
| `🚫 受限网站（GFW）` | 中国境内使用代理，海外环境可切 `DIRECT` |

## 注意事项

- 首次连接时需要下载大量规则文件，速度慢是正常现象。
- 不建议同时叠加多个会重写 `proxy-groups` 和 `rules` 的脚本。
- 节点名如果缺少地区关键词，自动分类效果会变差。
- 这份脚本原本就是可继续修改的，你可以按自己的使用习惯重做分组逻辑。

## 完整文档

更完整的内容都在这里：

- [CONFIG_STEPS.md](./CONFIG_STEPS.md)

包括：

- Mihomo Party / Clash Verge Rev 详细导入步骤
- Mixin 粘贴内容
- 验证方法
- 版本亮点
- FAQ

## 致谢

- [MetaCubeX/mihomo](https://github.com/MetaCubeX/mihomo)
- [mihomo-party-org/mihomo-party](https://github.com/mihomo-party-org/mihomo-party)
- [clash-verge-rev/clash-verge-rev](https://github.com/clash-verge-rev/clash-verge-rev)
- [sub-store-org/Sub-Store](https://github.com/sub-store-org/Sub-Store)
- 原始思路参考：[IvanSolis1989/Smart-Config-Kit](https://github.com/IvanSolis1989/Smart-Config-Kit/tree/main)
