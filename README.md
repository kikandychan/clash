# Apple TV F1 分流规则

基于 2026-10-11 在 macOS TV app 中触发 F1 直播和回放时观察到的五个域名。播放时出现国家或地区不可用提示，但未解密 HTTPS，因此这些是播放相关候选域名，尚未确认具体地区检测 URL，也未验证分流后能否播放。

- `Apple-TV-F1.list`：Loon 远程规则；支持 `classical` + `text` 的 Clash/Mihomo 也可使用。
- `Apple-TV-F1.yaml`：Clash/Mihomo 的 `classical` YAML 规则集，内容与 `.list` 相同。

规则文件不带策略列。导入端应绑定到有美国出口的节点或策略组，并将 F1 规则置于 Apple、Direct、其他媒体规则和最终兜底规则之前。全局直连模式会绕过规则，应在需要分流时使用规则模式。

`uts-api.itunes.apple.com` 和 `upp.itunes.apple.com` 是共享 Apple TV 端点；将它们分流会同时影响其他 Apple TV 内容。规则使用精确域名，不匹配全部 apple.com。未添加 HTTPS 解密、重写或证书配置。

官方格式参考：[Mihomo 规则集](https://wiki.metacubex.one/config/rule-providers/)、[Loon 订阅规则](https://nsloon.app/en/docs/Rule/sub_rule/)。

## 订阅链接与接入示例

[Loon / Clash 文本规则](https://raw.githubusercontent.com/kikandychan/clash/main/Apple-TV-F1.list)

[Clash YAML 规则](https://raw.githubusercontent.com/kikandychan/clash/main/Apple-TV-F1.yaml)

### Loon

将下面这行加入 `[Remote Rule]`，放在 Apple 等其他订阅之前：

```ini
[Remote Rule]
https://raw.githubusercontent.com/kikandychan/clash/main/Apple-TV-F1.list, policy=🇺🇸 美国节点, tag=Apple-TV-F1, enabled=true
```

`🇺🇸 美国节点` 必须是配置中已存在、实际使用美国出口的策略。其他配置请替换为自己的美国策略名。

### Clash / Mihomo

合并到已有配置中，不要重复创建顶层 `rule-providers` 或 `rules`。`RULE-SET` 放在 Apple、Direct 和最终兜底规则之前：

```yaml
rule-providers:
  Apple-TV-F1:
    type: http
    behavior: classical
    format: yaml
    url: https://raw.githubusercontent.com/kikandychan/clash/main/Apple-TV-F1.yaml
    path: ./ruleset/Apple-TV-F1.yaml
    interval: 86400

rules:
  - RULE-SET,Apple-TV-F1,美国-自动
```

`美国-自动` 必须是配置中已存在的美国出口策略。仅使用 `.list` 时将 `format` 改为 `text`，并将 URL 和缓存路径扩展名改为 `.list`。
