## 字段操作
- 添加字段

  `alter table table_name add column_name varchar(10) not Null defualt;` 

### 字段类型

| 类型    | 大小(字节) | 范围（有符号）                  | 范围（无符号）     | 用途       |      |
| ------- | ---------- | ------------------------------- | ------------------ | ---------- | ---- |
| TINYINT | 1          | (-128，127)                     | (0，255)           | 小整数值   |      |
| INT     | 4          | (-2 147 483 648，2 147 483 647) | (0，4 294 967 295) | 大整数值   |      |
| CHAR    | 0-255      |                                 |                    | 定长字符串 |      |
| VARCHAR | 0-65535    |                                 |                    | 变长字符串 |      |
| TEXT    | 65535      |                                 |                    | 定长字符串 |      |

### 日期相关

- 设置字段为日期格式，并在修改时自动更新：

  `alter table table_name add column_name TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP`

- 日期查询

  - 今天

    `select * from table_name where to_days(column_name) = to_days(now());  `

  - 昨天

    `SELECT * FROM table_name WHERE TO_DAYS( NOW( ) ) - TO_DAYS(column_name) <= 1 `

  - 7天

    `SELECT * FROM table_name where DATE_SUB(CURDATE(), INTERVAL 7 DAY) <= date(column_name)  `
