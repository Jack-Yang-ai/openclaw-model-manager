---
name: model-manager
description: OpenClaw 模型管理工具 — 零 token 消耗的本地 CLI，快速添加、删除、查看模型和 Provider，自动同步 allowlist 并重启 Gateway
---

# Model Manager Skill

## When to activate
当用户需要添加、删除、查看 OpenClaw 模型或 Provider 时激活。

## How to use
本 skill 提供 `add-model` CLI 工具，安装后链接到 PATH：

```bash
ln -sf ~/.openclaw/workspace/skills/model-manager/scripts/add-model ~/.local/bin/add-model
```

### 快速上手

```bash
# 添加模型
add-model gemini-3-flash-preview "Gemini 3 Flash" --image

# 删除模型
add-model remove claude-haiku-4-5-20251001

# 列出所有
add-model list --all

# 管理 Provider
add-model providers
add-model add-provider moonshot --base-url https://api.moonshot.ai/v1 --api-key sk-xxx
```

详见 README.md。
