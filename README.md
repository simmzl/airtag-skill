# AirTag - 物品位置记录助手

一个 [Claude Code](https://claude.ai/code) Skill，帮你记录"什么东西放在哪里"，数据存储在飞书多维表格中。

## 功能

- **记录物品位置** — 告诉 AI "护照放在书房抽屉里"，自动写入飞书表格
- **查找物品** — 问 AI "护照在哪"，立即返回位置
- **更新位置** — 物品换了地方？直接说，自动更新记录
- **列出所有物品** — 一键查看所有物品及其位置

## 前置条件

- [Claude Code](https://claude.ai/code)
- [lark-cli](https://github.com/nicepkg/lark-cli) 已安装并完成认证（`lark-cli auth login`）

## 安装

将 `SKILL.md` 复制到 Claude Code 的 skills 目录：

```bash
mkdir -p ~/.claude/skills/airtag
cp SKILL.md ~/.claude/skills/airtag/SKILL.md
```

## 使用

首次使用时，Skill 会询问你是否已有飞书多维表格：

- **已有表格** — 发送链接，自动绑定
- **没有表格** — 自动创建一个，包含以下字段：

| 字段 | 类型 | 说明 |
|------|------|------|
| 物品名称 | 文本 | 物品名 |
| 位置 | 文本 | 存放位置 |
| 备注 | 文本 | 补充信息 |
| 记录时间 | 日期 | 记录/更新时间 |

配置完成后，直接对话即可：

```
> 护照放在书房抽屉里
> 钥匙在玄关鞋柜上
> 护照在哪？
> 列出所有物品
```

## 配置

初始化后会在项目根目录生成 `.airtag-config.json`，存储表格绑定信息：

```json
{
  "base_token": "your_base_token",
  "table_id": "your_table_id"
}
```

如需重新绑定，删除该文件即可。

## 许可证

MIT
