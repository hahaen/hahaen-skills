---
name: database-schema-documentation
description: 按《公共审计字段.md》和《表结构设计参考规范.md》创建或修改 MySQL 业务表结构 Markdown 文档，严格遵循规定的章节、顺序和字段表格式。
---

# 数据库表结构文档

始终使用中文完成数据库表结构设计。将《公共审计字段.md》作为字段规范，将《表结构设计参考规范.md》作为最终文档格式规范；两份文档是设计依据，不是最终文档中需要重复输出的内容。

## 内部执行规则

- 保持原有业务含义。根据业务对象、表用途、查询、状态流转、唯一性、关联、幂等和追溯需要，合理优化字段类型、长度、命名和顺序。
- 只补充有明确业务用途的必要字段。每个补充或调整的字段，都在字段表的“说明”中写清业务含义、取值范围、关联关系和默认值。
- 先应用公共审计字段，再补充业务字段和表关系。
- 最终响应直接从 `## 2. 表清单` 开始，只输出生成的 Markdown 正文，不添加开场白、分析过程、规则说明、校验结果、免责声明或结尾说明。
- 不用代码围栏包裹整份最终文档；最终文档只在表关系处按模板使用 `text` 代码块。
- 下列内部规则只用于生成，不得转写到最终文档中；最终文档不得出现“不得输出”“不允许生成”“本规范要求”“生成说明”等元指令文字。

## 公共审计字段

主业务表必须包含以下字段，并建议按此顺序放在业务字段之后：

| 字段 | 类型 | 必填 | 说明 | 默认值 |
|---|---|---:|---|---|
| `created_at` | `DATETIME(6)` | 是 | 创建时间，UTC | `CURRENT_TIMESTAMP(6)` |
| `created_by` | `BIGINT UNSIGNED` | 否 | 创建人 ID；未提供操作人时为空 | `NULL` |
| `created_name` | `VARCHAR(64)` | 否 | 创建人名称快照；未提供操作人时为空 | `NULL` |
| `updated_at` | `DATETIME(6)` | 否 | 更新时间，UTC | `NULL`；更新时自动写入当前时间 |
| `updated_by` | `BIGINT UNSIGNED` | 否 | 更新人 ID；未提供操作人时为空 | `NULL` |
| `update_name` | `VARCHAR(64)` | 否 | 更新人名称快照；未提供操作人时为空 | `NULL` |
| `deleted_at` | `DATETIME(6)` | 否 | 逻辑删除时间，UTC | `NULL` |
| `deleted_by` | `BIGINT UNSIGNED` | 否 | 删除人 ID | `NULL` |
| `deleted_name` | `VARCHAR(64)` | 否 | 删除人名称快照 | `NULL` |
| `deleted` | `TINYINT(1)` | 是 | 删除标识：`0` 未删除、`1` 已删除 | `0` |

标准 SQL 定义仅用于生成字段表，不得单独出现在最终文档中：

```sql
created_at DATETIME(6) NOT NULL DEFAULT CURRENT_TIMESTAMP(6)
created_by BIGINT UNSIGNED NULL DEFAULT NULL
created_name VARCHAR(64) NULL DEFAULT NULL
updated_at DATETIME(6) NULL DEFAULT NULL ON UPDATE CURRENT_TIMESTAMP(6)
updated_by BIGINT UNSIGNED NULL DEFAULT NULL
update_name VARCHAR(64) NULL DEFAULT NULL
deleted_at DATETIME(6) NULL
deleted_by BIGINT UNSIGNED NULL
deleted_name VARCHAR(64) NULL
deleted TINYINT(1) NOT NULL DEFAULT 0
```

关联表必须增加 `deleted` 字段：`TINYINT(1)`，必填，`0` 表示未删除，`1` 表示已删除，默认值为 `0`。关联表不因该要求自动复制主业务表的其他审计字段。

## 最终输出结构

最终文档严格按照以下结构生成：表清单、多表时的表关系、每张表的用途、每张表的字段。最终文档只允许出现表名、表类型、表用途、关系图、字段表及字段说明。公共审计字段规范表、标准 SQL 定义和本 Skill 的内部规则不属于最终文档内容。

### 1. 表清单

按以下格式输出，并使用 `## 2. 表清单` 标题：

```markdown
## 2. 表清单

| 表名 | 类型 | 用途 |
|---|---|---|
| `模块_主表` | 主表 | 保存…… |
| `模块_关联表` | 关联表 | 保存……之间的关系 |
```

### 2. 表关系

主表和关联表超过一张时，使用 `text` 代码块表达关系，并明确一对一、一对多或多对多；除该关系图外，不在最终文档中增加 SQL 代码块或说明代码块。例如：

```text
主表 A
    1 ─── N 关联表
                    N ─── 1 主表 B
```

只有一张表时不强行添加关系图。

### 3. 单张主表

每张表只保留以下两部分：

#### 用途

说明该表保存什么业务对象，以及不负责保存什么内容。

#### 字段

使用以下字段表格式：

```markdown
| 字段 | 类型 | 必填 | 说明 | 默认值 |
|---|---|---:|---|---|
| `id` | `BIGINT UNSIGNED` | 是 | …… ID，主键，自增 | `AUTO_INCREMENT` |
| `business_code` | `VARCHAR(64)` | 是 | 稳定业务编码，唯一 | 无（业务层赋值） |
| `name` | `VARCHAR(128)` | 是 | ……名称 | 无（业务层赋值） |
| `description` | `VARCHAR(500)` | 否 | ……说明 | `NULL` |
| `status` | `VARCHAR(16)` | 是 | 状态：`ENABLED`、`DISABLED` | `ENABLED` |
| `sort_order` | `INT` | 是 | 排序值，数值越小越靠前 | `0` |
```

字段顺序固定为：主键、业务字段、状态字段、标识字段、业务时间字段、公共审计字段。主业务表在业务字段之后追加完整公共审计字段；关联表追加 `deleted` 字段。

## 生成前的内部检查

生成前仅在内部检查以下事项：表清单与实际表一致；多表关系与表类型一致；主业务表包含完整审计字段且顺序正确；关联表包含 `deleted`；每个字段都有完整说明和默认值；标题、顺序和字段表格式符合参考模板。检查结果不输出到最终响应。
