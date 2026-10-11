# Apple TV F1 分流规则

基于 2026-10-11 在 macOS TV app 中触发 F1 直播和回放时观察到的五个域名。播放时出现国家或地区不可用提示，但未解密 HTTPS，因此这些是播放相关候选域名，尚未确认具体地区检测 URL，也未验证分流后能否播放。

- `Apple-TV-F1.list`：Loon 远程规则；支持 `classical` + `text` 的 Clash/Mihomo 也可使用。
- `Apple-TV-F1.yaml`：Clash/Mihomo 的 `classical` YAML 规则集，内容与 `.list` 相同。

规则文件不带策略列。导入端应绑定到独立的手选策略组，由使用者选择默认代理、美国代理或美国自动等已有策略，并将 F1 规则置于 Apple、Direct、其他媒体规则和最终兜底规则之前。全局直连模式会绕过规则，应在需要分流时使用规则模式。

`uts-api.itunes.apple.com` 和 `upp.itunes.apple.com` 是共享 Apple TV 端点；将它们分流会同时影响其他 Apple TV 内容。规则使用精确域名，不匹配全部 apple.com。未添加 HTTPS 解密、重写或证书配置。

官方格式参考：[Mihomo 规则集](https://wiki.metacubex.one/config/rule-providers/)、[Loon 订阅规则](https://nsloon.app/en/docs/Rule/sub_rule/)。

## 订阅链接与接入示例

[Loon / Clash 文本规则](https://raw.githubusercontent.com/kikandychan/clash/main/Apple-TV-F1.list)

[Clash YAML 规则](https://raw.githubusercontent.com/kikandychan/clash/main/Apple-TV-F1.yaml)

### Loon

将下面这行加入 `[Remote Rule]`，放在 Apple 等其他订阅之前：

```ini
[Proxy Group]
Apple TV F1 = select,🇺🇸 美国节点,🚀 策略选择,美国节点-自动

[Remote Rule]
https://raw.githubusercontent.com/kikandychan/clash/main/Apple-TV-F1.list, policy=Apple TV F1, tag=Apple-TV-F1, enabled=true
```

在 Loon 的策略页面选择 `Apple TV F1`，可切换“🇺🇸 美国节点”“🚀 策略选择”（默认代理）和“美国节点-自动”。这些选项必须在原配置中存在。

### Clash / Mihomo

合并到已有配置中，不要重复创建顶层 `rule-providers` 或 `rules`。`RULE-SET` 放在 Apple、Direct 和最终兜底规则之前：

```yaml
proxy-groups:
  - name: Apple TV F1
    type: select
    proxies:
      - 美国策略
      - 默认代理
      - 美国自动
      - 美国-自动
      - 美国均衡-散列
      - 美国均衡-轮询

rule-providers:
  Apple-TV-F1:
    type: http
    behavior: classical
    format: yaml
    url: https://raw.githubusercontent.com/kikandychan/clash/main/Apple-TV-F1.yaml
    path: ./ruleset/Apple-TV-F1.yaml
    interval: 86400

rules:
  - RULE-SET,Apple-TV-F1,Apple TV F1
```

在 Clash 客户端选择 `Apple TV F1` 后，单选上述已有策略。默认首选美国策略，也可手动改用默认代理或其他美国策略。选项必须在原配置中存在；其他配置应替换为对应策略名。默认代理也可能使用非美国出口，是否能播放取决于最终出口及 Apple 的地区校验。仅使用 `.list` 时将 `format` 改为 `text`，并将 URL 和缓存路径扩展名改为 `.list`。
