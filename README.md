# Model Manager

OpenClaw 模型管理工具 — 零 token 消耗的本地 CLI，快速添加、删除、查看模型和 Provider。

## 功能

- **添加模型** — 一行命令往任意 provider 追加模型，自动同步 allowlist
- **删除模型** — 移除模型并自动清理 allowlist + fallbacks
- **查看模型** — 列出单个/所有 provider 的模型清单
- **Provider 管理** — 创建/删除自定义 Provider（OpenAI/Anthropic 兼容）
- **自动重启** — 每次变更自动重启 Gateway 生效

## 安装

skill 安装后，将脚本链接到 PATH：

```bash
ln -sf ~/.openclaw/workspace/skills/model-manager/scripts/add-model ~/.local/bin/add-model
```

确保 `~/.local/bin` 在 PATH 中。

## 用法

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

# Provider 管理
add-model add-provider moonshot \
  --base-url https://api.moonshot.ai/v1 \
  --api-key sk-xxx \
  --api openai-completions

add-model remove-provider moonshot
```

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
| `--all` | 列出所有 provider | false |
| `--base-url` | Provider API 地址 | - |
| `--api-key` | Provider API Key | - |
| `--api` | API 兼容模式 | openai-completions |

## 特性

- **零 token 消耗** — 纯本地 Python 脚本，不调用任何 LLM API
- **自动同步 allowlist** — 添加/删除模型时自动维护 `agents.defaults.models`
- **自动清理 fallbacks** — 删除模型时自动清理 fallback 列表
- **模糊匹配提示** — 删除时找不到精确匹配会提示相近的模型名
- **支持 provider/model-id 写法** — `add-model remove modelsproxy/xxx` 自动拆分

## 要求

- OpenClaw 已安装并配置
- Python 3.9+
- `~/.openclaw/openclaw.json` 存在
