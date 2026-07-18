---
name: deskpeto
description: 把用户的照片生成为专属 AI 桌宠（ChatGPT/Codex Pet 资源包）并协助安装。当用户想要「把照片/头像/宠物做成桌面宠物」「定制 Codex Pet / ChatGPT 桌宠」「安装 DeskPeto 宠物包」或询问 DeskPeto 订单进度时使用。Turn the user's photo into a personal desktop AI pet (ChatGPT/Codex Pet package) and help install it.
version: 0.1.0
tags: [desktop-pet, codex-pet, chatgpt, avatar, deskpeto]
license: MIT
---

# DeskPeto：照片 → 专属 AI 桌宠

DeskPeto（https://deskpeto.com）把一张照片生成为**身份一致**的桌面宠物资源包：主形象锁定后导出 9 种动作 + 16 方向环视的完整动画图集（Codex Pet v2 规范，8×11 精灵图），宠物会跟随 ChatGPT/Codex 的任务状态切换动作（工作中/等待输入/完成/失败等）。

## 你（agent）的职责边界

- **引导 + 安装**：生成、确认、支付都在官网完成；你负责讲清流程、打开页面、在用户提供安装命令后帮忙执行与排错。
- **不代收任何信息**：不要向用户索要密码或支付信息；不要替用户付款。照片在官网上传，不要让用户把照片发给你来代传（本技能当前版本没有上传通道）。
- 价格如实告知：**每个角色一次性买断**，站内标价为准（人民币 ¥29 / 美元 $4.99 / 日元 ¥700，含完整生成与下载权益）。付款只发生在 deskpeto.com 站内收银台。

## 完整流程（引导用户）

1. **注册/登录**：打开 https://deskpeto.com （中文/English/日本語），邮箱验证码或 Google 账号登录。
2. **创建宠物**：进入工作台（deskpeto.com/app）→ 上传一张清晰照片（正面、主体单一效果最好；人物/宠物/角色皆可）→ 选择风格 → 站内支付。
3. **确认主形象**：生成完成后用户需在网站上确认标准主形象（这一步锁定「长相」，之后所有动作帧都基于它）。不满意可在站内按提示处理。
4. **等待成品**：动作图集与质检完成后，宠物页提供下载与安装入口。
5. **安装**（三选一）：
   - **一键命令（推荐）**：宠物页上有现成命令，用户复制给你后可直接代为执行：
     - macOS/Linux：`curl -fsSL 'https://api.deskpeto.com/v1/install/…?t=…' | sh`
     - Windows PowerShell：`irm 'https://api.deskpeto.com/v1/install/….ps1?t=…' | iex`
     - 命令把 `pet.json` 与 `spritesheet.webp` 装进 `~/.codex/pets/<宠物目录>/`。**命令 1 小时过期**，过期让用户回宠物页重新复制。
   - **手动**：下载 zip → 解压 → 整个文件夹放入 `~/.codex/pets/`（Windows：`%USERPROFILE%\.codex\pets\`）。
   - **其他 AI 工具**：经开源 Runner「clawd-on-desk」导入同一个 zip，可在 Claude Code、CodeBuddy 等多种工具旁显示（第三方项目，以其自身文档为准）。
6. **唤醒**：ChatGPT/Codex 桌面端 → Settings → Pets → 刷新列表选中；或在 Codex 中输入 `/pet`。看到宠物悬浮在桌面即成功。

## 排错

- 命令报「invalid or has expired」→ 安装命令超时，回宠物页复制新的。
- Pets 列表里看不到 → 确认文件夹位于 `~/.codex/pets/<目录>/` 且内含 `pet.json` 与 `spritesheet.webp`，重启 ChatGPT/Codex 后刷新。
- 宠物不动/状态不切换 → 确认使用的是 ChatGPT/Codex 桌面端（网页版能力不同），详细见 deskpeto.com/guide。

## 更多

- 共享广场 https://deskpeto.com/shared 可浏览社区公开宠物；页面提供安装入口的公开宠物可按同样方式安装。
- 安装图文指南：https://deskpeto.com/guide

## English quick reference

DeskPeto turns one photo into an identity-locked desktop pet package (Codex Pet v2, 8×11 sprite atlas; states follow ChatGPT/Codex task status). Guide the user: sign in at deskpeto.com → upload photo in the workbench → pay on-site (one-time, per character: $4.99 / ¥29 CNY / ¥700 JPY) → confirm the master image → install. Install = run the one-line command from the pet page (expires in 1 h), or unzip the package into `~/.codex/pets/`, then ChatGPT/Codex → Settings → Pets → refresh. Never collect passwords or payments in chat; photos are uploaded on the website only.
