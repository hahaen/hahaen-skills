---
name: database-schema-documentation
description: 按公共审计字段和固定模板创建或修改 MySQL 业务表结构 Markdown 文档；适用于表清单、表关系、主表及关联表设计，不用于生成迁移脚本或脱离模板的数据库教程。
---

# 数据库表结构文档

用于把业务对象、表用途和大概字段整理为可直接评审的 MySQL 表结构 Markdown 文档。始终使用中文回答；本 Skill 中的规则是设计约束，不要把规则说明、推理过程或本 Skill 的章节原样追加到最终表结构文档中。

## 设计原则

- 不改变原有业务含义。结合查询、状态流转、唯一性、关联、幂等和追溯需要，合理调整字段类型、长度、命名和顺序。
- 只补充有明确业务用途或确有必要的字段，不为了“完整”添加没有用途的字段。
- 每个补充或调整后的字段，都要在“说明”中写清业务含义、取值范围、关联关系和默认值；不确定时先保留不确定性，不擅自编造业务规则。
- 先应用本 Skill 的公共审计字段要求，再补充业务字段和表关系。

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

主业务表中的标准 SQL 定义如下，除非业务需求明确要求不同，否则保持不变：

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

关联表必须增加 `deleted` 字段：`TINYINT(1)`、必填、`0` 表示未删除、`1` 表示已删除、默认值为 `0`。不要把主业务表的整组审计字段无依据地复制到关联表。

## 输出流程

1. 识别业务对象、表用途、主表/关联表类型，以及表之间的一对一、一对多或多对多关系。
2. 按“主键、业务字段、状态字段、标识字段、业务时间字段、公共审计字段”的顺序排列主业务表字段；关联表按关联所需字段并补充 `deleted`。
3. 设计唯一性、关联和幂等所需的字段时，只保留能从业务用途推导出的内容，并在字段说明中写清楚。
4. 生成最终 Markdown 后，检查所有主业务表是否包含完整且顺序正确的审计字段，关联表是否包含 `deleted`，以及关系图和表清单是否与实际表一致。

## 最终文档模板

最终表结构文档严格只允许包含：表清单、表关系、各表用途和字段。不得输出额外的章节、规则说明、约束、验证、迁移、总结、索引建议或其他内容。

### 表清单

按以下格式输出，并使用 `## 2. 表清单` 标题：

```markdown
## 2. 表清单

| 表名 | 类型 | 用途 |
|---|---|---|
| `模块_主表` | 主表 | 保存…… |
| `模块_关联表` | 关联表 | 保存……之间的关系 |
```

### 表关系

主表和关联表超过一张时，使用 `text` 代码块表达关系，并明确一对一、一对多或多对多；例如：

```text
主表 A
    1 ─── N 关联表
                    N ─── 1 主表 B
```

只有一张表时不强行添加关系图。

### 单张主表

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

公共审计字段直接追加到主业务表字段表中，并保持本 Skill 规定的顺序。关联表追加 `deleted` 字段。

## 输出边界

- 需要设计或修改表结构时，直接给出符合上述模板的最终内容；不要先输出一份脱离模板的分析报告。
- 用户只要求修改现有表结构文档时，保持未涉及的业务含义和文档结构，修复受影响的表清单、关系图、用途和字段表。
- 不把本 Skill 的规则表、标准 SQL、设计流程或校验结果混入最终表结构文档；它们只用于指导生成。
