<div align="center">

# AirTag.skill

> *「东西放哪了？问 AI 就行。」*

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![Skills.sh](https://img.shields.io/badge/skills.sh-Compatible-green)](https://skills.sh)
[![lark-cli](https://img.shields.io/badge/lark--cli-Required-blue)](https://github.com/nicepkg/lark-cli)

<br>

**让任意 AI 编程助手帮你记录物品位置，数据存在飞书多维表格里，随时问随时答。**

<br>

[看效果](#效果示例) · [安装](#安装) · [工作原理](#工作原理) · [配置](#配置)

</div>

---

## 效果示例

```
用户      ❯ 护照放在书房抽屉里

AirTag   ❯ ✅ 已记录：护照 → 书房抽屉
```

```
用户      ❯ 护照在哪？

AirTag   ❯ 📍 护照 — 书房抽屉
           记录时间：2026-04-09 10:55
```

```
用户      ❯ 护照换地方了，放到卧室床头柜

AirTag   ❯ ✅ 已更新：护照 → 卧室床头柜
```

```
用户      ❯ 列出所有物品

AirTag   ❯ 📋 物品清单：
           | 物品   | 位置         | 记录时间          |
           |--------|-------------|------------------|
           | 护照   | 卧室床头柜   | 2026-04-09 11:02 |
           | 钥匙   | 玄关鞋柜     | 2026-04-08 20:30 |
           | 充电器 | 客厅沙发旁   | 2026-04-07 14:15 |
```

这不是笔记本。你说一句话，AI 解析物品和位置，写入飞书多维表格。下次忘了东西在哪，问一句就行。

---

## 安装

```bash
npx skills add simmzl/airtag-skill
```

或手动安装：

```bash
# Claude Code
mkdir -p ~/.claude/skills/airtag
curl -o ~/.claude/skills/airtag/SKILL.md https://raw.githubusercontent.com/simmzl/airtag-skill/main/SKILL.md

# Cursor
mkdir -p ~/.cursor/skills/airtag
curl -o ~/.cursor/skills/airtag/SKILL.md https://raw.githubusercontent.com/simmzl/airtag-skill/main/SKILL.md
```

也可以直接将 `SKILL.md` 内容作为项目级 Rule 使用。

### 前置条件

- [lark-cli](https://github.com/nicepkg/lark-cli) 已安装并完成认证（`lark-cli auth login`）

---

## 工作原理

```
首次使用                          日常使用
┌─────────────────┐              ┌─────────────────┐
│  有飞书表？       │              │ "钥匙在玄关"     │
│  ├─ 有 → 发链接  │              │       ↓          │
│  └─ 没有 → 自动建│              │  解析物品+位置    │
│       ↓          │              │       ↓          │
│  保存配置到本地   │              │  写入飞书表格     │
└─────────────────┘              └─────────────────┘
```

**1. 初始化** — 首次使用时询问你是否已有飞书多维表格。有就绑定，没有就自动创建，表结构如下：

| 字段 | 类型 | 说明 |
|------|------|------|
| 物品名称 | 文本 | 物品名 |
| 位置 | 文本 | 存放位置 |
| 备注 | 文本 | 补充信息 |
| 记录时间 | 日期 | 记录/更新时间 |

**2. 记录** — 说"护照放在书房抽屉里"，AI 自动解析并写入表格。

**3. 查找** — 问"护照在哪"，AI 从表格中检索最近记录。

**4. 更新** — 说"护照换到卧室了"，AI 找到原记录并更新位置。

**5. 列表** — 说"列出所有物品"，AI 以表格形式展示全部记录。

---

## 配置

初始化后会在当前目录生成 `.airtag-config.json`：

```json
{
  "base_token": "your_base_token",
  "table_id": "your_table_id"
}
```

如需重新绑定表格，删除该文件即可。

---

## 仓库结构

```
airtag-skill/
├── SKILL.md                    # Skill 本体（提示词）
├── .airtag-config.example.json # 配置文件示例
├── .gitignore                  # 忽略真实配置
└── README.md                   # 你正在看的这个
```

---

## 许可证

MIT — 随便用，随便改。

---

<div align="center">

**记东西放哪，不该占用你的脑子。**<br>
**说一句话，AI 帮你记住。**

<br>

MIT License © [simmzl](https://github.com/simmzl)

</div>
