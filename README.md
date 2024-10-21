<!--
 * @Author: lirenjie
 * @Date: 2024-10-21 10:12:46
 * @LastEditors: lirenjie
 * @LastEditTime: 2024-10-21 10:20:45
 * @FilePath: /stockAnalysis/README.md
 * @Description: 
 * 
 * Copyright (c) 2024 by lirenjie, All Rights Reserved. 
-->
### 数据库设计方案

#### 1. **股票基本信息表 (stock_info)**

用于存储每只股票的基本信息（如股票代码、名称等），这些信息在整个两年内不会频繁变化。

| 字段名         | 数据类型         | 描述                 |
| -------------- | ---------------- | -------------------- |
| `stock_id`     | INT (主键)        | 自增ID               |
| `stock_code`   | VARCHAR(10)       | 股票代码             |
| `stock_name`   | VARCHAR(100)      | 股票名称             |
| `market`       | VARCHAR(10)       | 市场（如沪市/深市）   |
| `industry`     | VARCHAR(100)      | 所属行业             |
| `list_date`    | DATE              | 上市日期             |
| `status`       | VARCHAR(10)       | 股票状态（如正常/暂停）|

#### 2. **股票历史数据表 (stock_history)**

存储每只股票在特定日期的交易信息。这个表会很大，因为它包含每只股票的每日交易数据。

| 字段名         | 数据类型         | 描述                       |
| -------------- | ---------------- | -------------------------- |
| `id`           | INT (主键)        | 自增ID                     |
| `stock_code`   | VARCHAR(10)       | 股票代码（外键，关联stock_info） |
| `trade_date`   | DATE              | 交易日期                   |
| `open_price`   | DECIMAL(10, 2)    | 开盘价                     |
| `close_price`  | DECIMAL(10, 2)    | 收盘价                     |
| `high_price`   | DECIMAL(10, 2)    | 最高价                     |
| `low_price`    | DECIMAL(10, 2)    | 最低价                     |
| `volume`       | BIGINT            | 成交量                     |
| `amount`       | DECIMAL(15, 2)    | 成交金额                   |

#### 3. **指数数据表 (index_info)**（可选）

如果你需要存储沪深300等指数的信息，可以单独创建一个表用于存储指数数据。

| 字段名         | 数据类型         | 描述                       |
| -------------- | ---------------- | -------------------------- |
| `index_id`     | INT (主键)        | 自增ID                     |
| `index_code`   | VARCHAR(10)       | 指数代码                   |
| `index_name`   | VARCHAR(100)      | 指数名称                   |
| `market`       | VARCHAR(10)       | 市场（如沪市/深市）          |
| `list_date`    | DATE              | 指数建立日期               |

#### 4. **指数历史数据表 (index_history)**（可选）

存储指数的每日历史数据。

| 字段名         | 数据类型         | 描述                       |
| -------------- | ---------------- | -------------------------- |
| `id`           | INT (主键)        | 自增ID                     |
| `index_code`   | VARCHAR(10)       | 指数代码（外键，关联index_info）|
| `trade_date`   | DATE              | 交易日期                   |
| `open_price`   | DECIMAL(10, 2)    | 开盘价                     |
| `close_price`  | DECIMAL(10, 2)    | 收盘价                     |
| `high_price`   | DECIMAL(10, 2)    | 最高价                     |
| `low_price`    | DECIMAL(10, 2)    | 最低价                     |
| `volume`       | BIGINT            | 成交量                     |
| `amount`       | DECIMAL(15, 2)    | 成交金额                   |

### 设计说明

1. **表的关系**：
   - `stock_info` 表是 `stock_history` 的外键关系，用于存储每只股票的基本信息。`stock_code` 是关联两个表的主要字段。
   - `index_info` 和 `index_history` 类似，用于存储指数数据。

2. **历史数据表的设计**：
   - `stock_history` 表按日期和股票代码记录每日的交易数据。
   - 每天的交易数据包括开盘价、收盘价、最高价、最低价、成交量等，使用 `DECIMAL` 类型来精确存储这些价格信息。
   - `volume`（成交量）和 `amount`（成交金额）通常是很大的数字，使用 `BIGINT` 和 `DECIMAL` 来存储。

3. **日期范围**：
   - 数据需要覆盖沪深A股的两年数据，因此可以在 `stock_history` 表中添加一个日期范围的索引来提高查询效率。

4. **索引**：
   - 建议在 `stock_code` 和 `trade_date` 上添加索引，以加速查询特定股票或日期范围的数据。

### 未来扩展

如果需要扩展数据内容，比如增加分红信息、配股信息，或者其他财务指标，可以在此设计的基础上添加新的表。例如，可以增加一个专门的财务报表表，存储股票的财务信息。

在设计时要注意数据库的性能优化，比如对 `stock_code` 和 `trade_date` 进行适当的索引优化。