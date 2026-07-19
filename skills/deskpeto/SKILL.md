---
name: deskpeto
description: 让 ChatGPT/Codex 之外的 AI 工具（WorkBuddy、Claude Code、CodeBuddy、OpenClaw 等）接入 DeskPeto 桌宠：在对话里召唤会动的桌宠卡片、用一条命令让桌宠常驻桌面并跟随任务状态、协助连接 MCP 与安装；也支持引导在官网把照片生成为专属桌宠、免费安装共享广场的社区公开桌宠。当用户说「召唤桌宠」「显示我的宠物」「把照片/头像做成桌面宠物」「在桌面上放一只桌宠」「免费试用一只桌宠」「安装 DeskPeto 宠物包」或询问订单进度时使用。Bring DeskPeto's desktop pet to AI tools other than ChatGPT/Codex: summon an animated pet card in-chat, put a pet on the desktop with one command that follows task status, help connect the MCP and install, or guide photo-to-pet generation and free shared-gallery installs.
version: 0.5.0
tags: [desktop-pet, codex-pet, mcp, avatar, deskpeto]
license: MIT
---

# DeskPeto：给 ChatGPT/Codex 之外的 AI 工具装上桌宠

DeskPeto（https://deskpeto.com）最初是 ChatGPT/Codex 生态里的桌宠平台：一张照片生成**身份一致**的桌面宠物——9 种动作、16 方向环视，会随任务状态自动切换（工作中 / 等待输入 / 完成 / 失败）。**这一整套在 ChatGPT/Codex 里是原生自带能力，那部分用户不需要这个 skill**——生成、安装都在官网和客户端内自助完成，见文末〈ChatGPT/Codex 原生用户〉一节。

这个 skill（连同 DeskPeto MCP `mcp.deskpeto.com` 和桌面 runner「DeskPeto Desk」）存在的意义，是把同一份桌宠能力**搬到 ChatGPT/Codex 之外**的 AI 工具里——WorkBuddy、Claude Code、CodeBuddy、OpenClaw 等。如果你（agent）现在所在的宿主是这些工具之一，下面才是你的主场景。

## 你（agent）的职责边界

- **引导 + 安装**：生成、确认、支付都在官网完成；你负责讲清流程、打开页面、在用户提供安装命令或令牌后帮忙执行与排错。
- **不代收任何信息**：不要向用户索要密码或支付信息；不要替用户付款。照片在官网上传，不要让用户把照片发给你来代传（本技能当前版本没有上传通道）。
- 价格如实告知：**每个角色一次性买断**，站内标价为准（人民币 ¥29 / 美元 $4.99 / 日元 ¥700，含完整生成与下载权益）。付款只发生在 deskpeto.com 站内收银台。

## 桌面常驻桌宠：一条命令装好（DeskPeto Desk，新用户无需 key）

这是当前宿主（WorkBuddy / Claude Code / CodeBuddy 等）拿到桌宠的**主要方式**：宠物**一直待在桌面上、跟着你在 AI 工具里的工作动**（区别于装进 ChatGPT/Codex 的 Pets，也区别于下一节「对话里的卡片」）。它是一个隐形后台 runner（无窗口 / 无 Dock 图标 / 无托盘），文件都在独立的 `~/.deskpeto/` 目录，绝不写入 `~/.codex`；安装时自动接线你已装的 AI 工具。**新用户无需注册、无需令牌**——首次启动会从共享广场自动取一只默认宠物。

**安装前先向用户说明并征得同意再执行**（这条命令会下载并运行一个程序、并修改 AI 工具的 `settings.json`）：

- macOS / Linux：`curl -fsSL https://api.deskpeto.com/v1/runner/install | sh`
- Windows PowerShell：`irm https://api.deskpeto.com/v1/runner/install.ps1 | iex`

命令做三件事：把对应平台的 runner 下到 `~/.deskpeto/bin/` → 自动接线 hooks → 启动。装好后让用户**完全退出并重开** AI 工具（多数宿主仅启动时加载 hooks），桌宠即随任务切换动作（工作中 / 等待输入 / 完成 / 失败）。

**右键宠物**：切换宠物、尺寸、选择响应哪个工具、从共享广场获取、登录 DeskPeto 账号（同步你的专属宠物）、生成专属宠物、退场。

**登录与专属宠物是纯加分**：默认宠物开箱即用；用户要自己的形象时，按下文「生成专属宠物」在官网生成，再用右键菜单「同步我的宠物」拉到桌面。**卸载**：`~/.deskpeto/bin/deskpeto-desk --connect --remove` 撤下 hooks，或直接删除 `~/.deskpeto/` 目录。

**前提**：AI 工具的**桌面端**（hooks 只在桌面端触发）。runner 支持 macOS 与 Windows，Linux 尽力兼容。（早期第三方 Runner「clawd-on-desk」也能手动导入单个宠物 zip；DeskPeto Desk 是现在官方推荐的免密钥一体化方案，装一个就够。）

## 召唤桌宠到对话里（无需安装任何软件）

用户说「召唤桌宠 / 显示我的宠物 / summon my pet」时，按以下流程执行（不要自己发挥画一只——用户要的是他在 DeskPeto 的真实宠物）：

1. **前提**：已连接 DeskPeto MCP（见下节）。调用 `show_pet` 工具（可传 `state` 参数：idle/waving/jumping/running/waiting/review/failed）。
2. **如果你的宿主渲染了交互卡片**（MCP Apps）：宠物已经出现在气泡里，无需更多操作。
3. **如果没有渲染卡片**（如 WorkBuddy 桌面端）：从工具返回的 `structuredContent` 里取 `name` 与 `spritesheetUrl`，复制本技能自带的模板 `references/pet-page.html`，把 `__PET_NAME__` 和 `__SPRITESHEET_URL__` 替换为真实值，用宿主的文件展示能力（如 present_files）打开这个页面——它会用真实精灵图逐帧播放动画，并带状态切换按钮。
4. **注意**：`spritesheetUrl` 带 1 小时时效签名，过期后重新调用 `show_pet` 取新链接，不要复用旧 URL；不要修改模板里的帧时长表（那是与官方桌宠一致的动画节奏）。

## 连接 DeskPeto MCP（解锁查询/召唤/一键安装）

**首选：设备码授权（用户零复制粘贴）**——前提是宿主已能访问 `https://mcp.deskpeto.com/mcp`（哪怕未带令牌）：

1. 调用 `start_device_link` 工具（`label` 传宿主名，如 "WorkBuddy"），得到 `userCode`、`verificationUrl`、`deviceCode`。
2. 把授权码告诉用户，并帮用户打开 `verificationUrl`（浏览器）。提醒：登录 deskpeto.com 后回到该页点「允许」，核对页面上的码与你告知的一致。
3. 每 3 秒调用 `poll_device_link`（传 `deviceCode`）直到返回 `token`（只返回一次，注意保存）。超时 10 分钟需重新发起。
4. 把令牌写入宿主 MCP 配置（agent 可代写，如 `~/.workbuddy/mcp.json`）：

```json
{ "mcpServers": { "deskpeto": {
  "url": "https://mcp.deskpeto.com/mcp",
  "headers": { "Authorization": "Bearer <令牌>" } } } }
```

5. 不支持自定义请求头的客户端改用 `https://mcp.deskpeto.com/mcp?key=<令牌>`。写完提醒用户**完全重启宿主应用**（多数宿主启动时才加载 MCP 配置）。

**备用：手动令牌**——用户登录后打开 https://mcp.deskpeto.com/mcp/connect 点「生成」，把令牌贴回对话，你代写配置。

未连接令牌时，`browse_shared_pets` / `how_to_create_pet` / `show_pet`（公开宠物）/ 设备码两件套仍可用；个人宠物与安装命令需要令牌。

## 免费试用：共享广场

- 共享广场 https://deskpeto.com/shared 上的社区公开宠物**免费安装**（登录后进入宠物页取安装命令或下载包，安装方式与「DeskPeto Desk」一节相同）。
- 适合先免费体验桌宠效果，再付费生成自己的专属形象。

## 生成专属宠物（在官网完成，任何宿主通用）

不管最终用哪种方式把宠物接到桌面或对话里，专属形象都得先在 deskpeto.com 生成：

1. **注册/登录**：打开 https://deskpeto.com （中文/English/日本語），邮箱验证码或 Google 账号登录。
2. **创建宠物**：进入工作台（deskpeto.com/app）→ 上传一张清晰照片（正面、主体单一效果最好；人物/宠物/角色皆可）→ 选择风格 → 站内支付。
3. **确认主形象**：生成完成后用户需在网站上确认标准主形象（这一步锁定「长相」，之后所有动作帧都基于它）。不满意可在站内按提示处理。
4. **等待成品**：动作图集与质检完成后，宠物页提供下载与安装入口。

生成完成后，回到上面「桌面常驻桌宠」或「召唤桌宠到对话里」把它用起来。

## ChatGPT/Codex 原生用户（不需要这个 skill）

如果用户用的就是 ChatGPT/Codex 桌面端，整套生成 + 安装在官网和客户端内自助完成，不需要 agent 协助：生成完在宠物页复制一条安装命令（`curl -fsSL 'https://api.deskpeto.com/v1/install/…?t=…' | sh`，Windows 用对应 `.ps1`，1 小时内有效）或手动下载 zip 解压进 `~/.codex/pets/`，然后 ChatGPT/Codex → Settings → Pets 刷新列表选中，或输入 `/pet`。图文指南：https://deskpeto.com/guide。只有当用户明确说「我想同时在 Claude Code / WorkBuddy 等这里也弄一个」时，才回到上面的章节。

## 排错

- **DeskPeto Desk 没反应/桌宠不出现**：确认已完全退出并重开 AI 工具（多数宿主只在启动时加载 hooks）；确认 runner 已启动（`~/.deskpeto/bin/` 下有对应可执行文件）。
- **MCP 连不上/设备码一直轮询不到 token**：确认用户真的在 `verificationUrl` 页面点了「允许」、页面码与告知的码一致；超过 10 分钟需用 `start_device_link` 重新发起。
- **`show_pet` 召唤出的图不动/链接打不开**：`spritesheetUrl` 1 小时过期，重新调用 `show_pet` 取新链接，不要复用旧的。
- **ChatGPT/Codex 原生安装报错**（用户额外在 ChatGPT/Codex 里装了一份时）：命令提示「invalid or has expired」→ 回宠物页复制新命令；Pets 列表里看不到 → 确认文件夹在 `~/.codex/pets/<目录>/` 且含 `pet.json`、`spritesheet.webp`，重启 ChatGPT/Codex 后刷新。这条路径不需要本 skill 参与，出问题按 deskpeto.com/guide 处理即可。

## 更多

- 安装图文指南：https://deskpeto.com/guide

## English quick reference

DeskPeto (deskpeto.com) is a desktop-pet platform originally built into the ChatGPT/Codex ecosystem — a photo becomes an identity-locked pet with 9 actions and 16-direction look-around that reacts to task status (working / waiting / done / failed). Those ChatGPT/Codex users don't need this skill at all; they generate and install natively on-site and in the ChatGPT/Codex client (see the ChatGPT/Codex-native section above). **This skill exists to bring the same pet to AI tools other than ChatGPT/Codex** (WorkBuddy, Claude Code, CodeBuddy, OpenClaw, …), via two paths: **DeskPeto Desk**, a one-command background runner that puts an invisible, always-on pet on the desktop reacting to the current host's task status (no account needed — a default pet loads from the shared gallery); and the **DeskPeto MCP** (`mcp.deskpeto.com`), which lets the agent summon an animated pet card in-chat (`show_pet`) and drive device-link login. A personal pet is still generated on deskpeto.com (sign in → upload a photo → pay on-site, one-time per character: $4.99 / ¥29 CNY / ¥700 JPY → confirm the master image), then synced down via either path above. Never collect passwords or payments in chat; photos are uploaded on the website only.
