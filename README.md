# deskpeto-skill

把你的照片生成为专属 AI 桌宠的智能体技能 · An agent skill that turns your photo into a personal desktop AI pet.

- 产品：[DeskPeto](https://deskpeto.com) 最初是 ChatGPT/Codex 生态里的桌宠平台——一张照片生成身份一致的资源包（Codex Pet v2，8×11 动作图集），宠物随任务状态切换动作。这部分 ChatGPT/Codex 用户可以直接在官网和客户端自助完成，不需要本仓库。
- 本仓库：把同一份桌宠能力接入 **ChatGPT/Codex 之外**的 AI 工作台/智能体（WorkBuddy、OpenClaw、Claude 系等）。技能本体在 [`skills/deskpeto/SKILL.md`](skills/deskpeto/SKILL.md)，ClawHub 页面：https://clawhub.ai/juntook/skills/deskpeto

## 安装这个技能

| 环境 | 方式 |
| --- | --- |
| WorkBuddy | 技能市场搜索 `deskpeto` 一键安装；或把 `skills/deskpeto/` 文件夹放入 `~/.workbuddy/skills/` |
| OpenClaw | `openclaw skills install @juntook/deskpeto`；或把 `skills/deskpeto/` 放入 OpenClaw 的 skills 目录 |
| 其他兼容 SKILL.md 的智能体 | 复制 `skills/deskpeto/` 到对应 skills 目录 |

技能安装后，对你的 AI 说「把我的照片做成桌面宠物」即可开始。

## 这个技能做什么 / 不做什么

- ✅ 讲清生成流程、打开对应页面、代跑宠物页给出的一键安装命令、安装排错。
- ❌ 不代收照片、密码或付款——上传与支付都只在 deskpeto.com 官网完成。

## Roadmap

- v0.1：官网流程引导 + 安装协助（当前版本）
- v0.2：接入 DeskPeto MCP 服务（`mcp.deskpeto.com`），支持在对话内发起生成、查询进度、获取交付
- 后续：本仓库将补充 `.claude-plugin/` 清单，同一仓库同时作为 Claude Code / CodeBuddy CLI 插件分发

## License

MIT（仅指本仓库的技能文本；DeskPeto 服务及其生成资产的条款见官网）。
