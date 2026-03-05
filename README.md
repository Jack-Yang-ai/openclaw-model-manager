# Model Manager

OpenClaw 模型管理工具 — 零 token 消耗的本地 CLI，快速添加、删除、查看模型和 Provider。

## 功能

- **添加/删除模型** — 一行命令，自动同步 allowlist + fallbacks
- **Provider 管理** — 创建/删除自定义 Provider（OpenAI/Anthropic 兼容）
- **预设导入** — 一键导入预配置的模型包（含 provider + 多个模型）
- **预设导出** — 把当前配置导出为 JSON 分享给别人
- **自动重启** — 每次变更自动重启 Gateway 生效

## 安装

```bash
# 方式一：从 OpenClawMP 安装
openclawmp install skill/@u-xxx/model-manager

# 方式二：从 GitHub clone
git clone https://github.com/Jack-Yang-ai/openclaw-model-manager.git ~/.openclaw/workspace/skills/model-manager

# 链接到 PATH
ln -sf ~/.openclaw/workspace/skills/model-manager/scripts/add-model ~/.local/bin/add-model
```

确保 `~/.local/bin` 在 PATH 中。

## 快速开始

### 一键导入预设（推荐）

```bash
# 查看可用预设
add-model presets

# 一键导入 modelsproxy 全部模型（5个模型 + 自动创建 provider）
add-model import modelsproxy --api-key YOUR_API_KEY
```

这一条命令会：
1. 自动创建 `modelsproxy` provider（如不存在）
2. 导入 5 个模型：Step 3.5 Flash、Kimi K2.5、Claude Sonnet 4.5、Claude Opus 4.6、Gemini 3 Flash
3. 自动同步 allowlist
4. 自动重启 Gateway

### 单独管理模型

```bash
# 添加模型（默认 provider: openrouter）
add-model gemini-3-flash-preview "Gemini 3 Flash"
add-model gpt-4o-vision "GPT-4o Vision" --image
add-model deepseek-r1 "DeepSeek R1" --reasoning --ctx 128000 --cost-in 2 --cost-out 8

# 指定 provider
add-model kimi-k2.5 "Kimi K2.5" --image --provider modelsproxy

# 删除模型
add-model remove claude-haiku-4-5-20251001
add-model remove modelsproxy/claude-haiku-4-5-20251001  # provider/id 写法

# 列出模型
add-model list                          # 默认 provider
add-model list --all                    # 所有 provider
add-model list --provider volc-coding   # 指定 provider
add-model providers                     # 列出所有 provider
```

### Provider 管理

```bash
# 创建新 provider
add-model add-provider moonshot \
  --base-url https://api.moonshot.ai/v1 \
  --api-key sk-xxx \
  --api openai-completions

# 删除 provider（连带清理 allowlist）
add-model remove-provider moonshot
```

### 导出/分享预设

```bash
# 导出当前 provider 的模型配置
add-model export --provider modelsproxy -o my-models.json

# 别人导入你的预设
add-model import my-models.json --api-key THEIR_KEY
```

## 预设格式

预设是一个 JSON 文件，放在 `presets/` 目录下：

```json
{
  "name": "My Models",
  "description": "描述",
  "provider": {
    "id": "provider-name",
    "baseUrl": "https://api.example.com/v1",
    "api": "openai-completions"
  },
  "models": [
    {
      "id": "model-id",
      "name": "显示名",
      "input": ["text", "image"],
      "reasoning": false,
      "contextWindow": 200000,
      "maxTokens": 8192,
      "cost": { "input": 0, "output": 0, "cacheRead": 0, "cacheWrite": 0 }
    }
  ]
}
```

注意：预设中**不包含 API Key**，用户导入时通过 `--api-key` 提供。

## 参数说明

| 参数 | 说明 | 默认值 |
|------|------|--------|
| `--provider` | 目标 provider | openrouter |
| `--image` | 支持图片输入 | false |
| `--reasoning` | 推理模型 | false |
| `--ctx` | 上下文窗口 | 200000 |
| `--max-tokens` | 最大输出 tokens | 8192 |
| `--cost-in` | 输入成本 $/M tokens | 0 |
| `--cost-out` | 输出成本 $/M tokens | 0 |
| `--api-key` | Provider API Key | - |
| `--base-url` | Provider API 地址 | - |
| `--api` | API 兼容模式 | openai-completions |
| `--all` | 列出所有 provider | false |
| `-o, --output` | 导出文件路径 | - |

## 特性

- **零 token 消耗** — 纯本地 Python 脚本，不调用任何 LLM API
- **自动同步 allowlist** — 添加/删除时自动维护 `agents.defaults.models`
- **自动清理 fallbacks** — 删除时自动清理 fallback 列表
- **预设不含密钥** — API Key 不会泄露到预设文件中
- **模糊匹配提示** — 删除时找不到精确匹配会提示相近的模型名
- **支持 provider/model-id 写法** — `add-model remove modelsproxy/xxx` 自动拆分

## 要求

- OpenClaw 已安装并配置
- Python 3.9+
- `~/.openclaw/openclaw.json` 存在
