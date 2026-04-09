---
name: airtag
version: 1.0.0
description: "Use when user wants to record where things are located, track item locations, or manage a personal item registry via Lark/Feishu Bitable. 当用户需要记录物品位置、查找东西在哪、管理物品清单时使用。适用于任意支持 Skill 的 AI 编程助手。"
metadata:
  requires:
    bins: ["lark-cli"]
---

# AirTag - 物品位置记录助手

> **前置条件：** 需要已安装 [lark-cli](https://github.com/nicepkg/lark-cli) 并完成认证（`lark-cli auth login`）。如果环境中有 lark-shared 或 lark-base 相关的 Skill/Rule，可先阅读以了解认证和权限处理。

## Overview

帮用户记录"什么东西放在哪里"，数据存储在飞书多维表格中。

## 交互流程

### 1. 首次使用：初始化表格

检查项目根目录是否存在 `.airtag-config.json`：

- **存在且有效**：读取 `base_token` 和 `table_id`，跳到步骤 2
- **不存在**：进入初始化流程：

#### 询问用户

> "你已经有一个飞书多维表格用来存物品位置数据吗？如果有，请发链接给我；如果没有，我帮你创建一个。"

#### 选项 A：用户提供已有表链接

1. 从链接中提取 `base_token`（格式：`https://xxx.feishu.cn/base/BASE_TOKEN` 或 `https://xxx.feishu.cn/wiki/WIKI_TOKEN`）
2. 如果是 wiki 链接，先用 `lark-cli wiki spaces get_node` 解析真实 token
3. 用 `lark-cli base +table-list --base-token <token> --as user` 列出表，让用户选择或确认目标表
4. 用 `lark-cli base +field-list --base-token <token> --table-id <id> --as user` 检查字段是否匹配
5. 如果字段不完整，补建缺失字段

#### 选项 B：自动创建新表

```bash
# Step 1: 创建 Base
lark-cli base +base-create --name "AirTag 物品位置" --as user

# Step 2: 从返回结果中获取 base_token，创建数据表和字段
lark-cli base +table-create \
  --base-token <base_token> \
  --name "物品位置" \
  --fields '[{"name":"物品名称","type":"text"},{"name":"位置","type":"text"},{"name":"备注","type":"text"},{"name":"记录时间","type":"date"}]' \
  --as user
```

#### 保存配置

将 `base_token` 和 `table_id` 写入项目根目录的 `.airtag-config.json`：

```json
{
  "base_token": "app_xxx",
  "table_id": "tbl_xxx"
}
```

### 2. 日常使用：记录物品位置

用户说出物品和位置后，用以下命令写入记录：

```bash
lark-cli base +record-upsert \
  --base-token <base_token> \
  --table-id <table_id> \
  --json '{"物品名称":"<物品>","位置":"<位置>","备注":"<备注>","记录时间":"<YYYY-MM-DD HH:mm:ss>"}' \
  --as user
```

**解析用户输入的规则：**
- "护照在书房抽屉里" → 物品名称=护照, 位置=书房抽屉
- "把钥匙放到玄关了" → 物品名称=钥匙, 位置=玄关
- 如果用户没提到备注，备注字段留空字符串
- 记录时间使用当前时间

### 3. 查找物品

用户问"XX在哪"时：

```bash
lark-cli base +record-list \
  --base-token <base_token> \
  --table-id <table_id> \
  --as user
```

从返回结果中搜索匹配的物品，返回最近一条位置记录给用户。

### 4. 更新物品位置

如果用户说物品换了位置，先查找该物品最近的 record_id，然后更新：

```bash
lark-cli base +record-upsert \
  --base-token <base_token> \
  --table-id <table_id> \
  --record-id <record_id> \
  --json '{"位置":"<新位置>","记录时间":"<当前时间>"}' \
  --as user
```

### 5. 列出所有物品

```bash
lark-cli base +record-list \
  --base-token <base_token> \
  --table-id <table_id> \
  --as user
```

以表格形式展示所有物品及其位置。

## 常见错误处理

| 错误 | 处理 |
|------|------|
| Permission denied | 参考 lark-shared 的权限处理流程 |
| base_token 无效 | 删除 `.airtag-config.json`，重新初始化 |
| 字段名不匹配 | 用 `+field-list` 检查实际字段名，适配 |

## 权限

| 操作 | 所需 scope |
|------|-----------|
| 创建 Base | `bitable:app` |
| 读写记录 | `bitable:app` |
