# Chat Translator · 预设目录

本仓库是 **Minecraft 聊天翻译模组（Chat Translator）** 的权威预设目录：语言选项、外接 AI 调用网址、以及各站点可选模型清单。模组每次启动会异步拉取本文件，因此新增语言或模型只需改这里，**无需重新编译或发布模组 jar**。

## 文件

| 文件 | 用途 |
| --- | --- |
| `presets.json` | 机器可读的预设数据（模组读取这个文件） |
| `README.md` | 维护说明（本文件） |

## 数据结构

```jsonc
{
  "schema": 1,                    // 结构版本，升级时 +1
  "updated": "YYYY-MM-DD",        // 最后更新日期
  "repository": "https://...",    // 本仓库地址，展示在模组设置页 / Mod Menu
  "target_languages": [           // 「目标语言」下拉选项（译文语言）
    { "code": "zh-CN", "name": "简体中文", "name_en": "Chinese (Simplified)" }
  ],
  "source_languages": [           // 「翻译语言」下拉选项（原文语言，含 auto）
    { "code": "auto", "name": "自动检测", "name_en": "Auto detect" }
  ],
  "sites": [                      // 「外接 AI 站点」下拉选项
    {
      "id": "openai",             // 稳定标识，模组据此记录当前站点
      "name": "OpenAI",           // 英文名
      "name_zh": "OpenAI",        // 中文名
      "endpoint": "https://...",  // 选中后自动填入的调用网址
      "default_model": "...",     // 选中后自动填入的默认模型
      "docs": "https://...",      // 可选，服务商文档
      "models": [                 // 该站点下可选模型（同一网址多个模型）
        { "id": "gpt-4o-mini", "name": "GPT-4o mini" }
      ]
    }
  ]
}
```

最后一个 `id: "custom"` 的站点 `endpoint` 与 `default_model` 留空，表示「保留玩家手填的网址与模型」，模组不会覆盖它。

## 维护流程

1. 编辑 `presets.json`（保持合法 JSON、UTF-8、无 BOM）。
2. 更新顶层 `updated`；若字段结构变化，把 `schema` 加 1。
3. 提交到 `main` 分支。模组下次启动即可读到；设置页里也有「刷新预设」动作。
4. 模组内置了一份**同内容快照**作为离线回退，建议发版时同步更新一次，但日常维护只需改本仓库。

### 约定

- `code` 使用 BCP-47 风格；`auto` 仅作为源语言的「自动检测」值。
- `id` 一旦发布就不要改：模组会把它保存在玩家配置里。
- 模型 `id` 必须是服务商 API 真正接受的字符串，不要写显示名。
- 只收录 **OpenAI Chat Completions 兼容**的端点；其它协议请走模组的本地/纯翻译模式。
- `endpoint` 不要带 API Key、查询参数或任何凭据；Key 由玩家在模组设置页输入并加密保存在本机。

## 模组读取顺序

1. `raw.githubusercontent.com/.../main/presets.json`
2. 失败则 `api.github.com/repos/.../contents/presets.json?ref=main`
3. 再失败则用本地缓存 `config/chattrans/presets.cache.json`
4. 最后回退到 jar 内置快照

全程异步、带短超时，拉取失败绝不影响游戏或翻译功能；运行日志不记录任何凭据或聊天内容。

## 许可

MIT。预设数据为公开事实性信息，不含任何密钥或个人信息。
