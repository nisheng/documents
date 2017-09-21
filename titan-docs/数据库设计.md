## Titan，前端发布系统数据库设计方案

### groups（项目组）

| 字段名           | 数据类型         | 备注               |
| ------------- | ------------ | ---------------- |
| id            | int(11)      | 自增长  primary key |
| name          | varchar(30)  | 组名               |
| administrator | varchar(30)  | 管理员名称，数组         |
| remark        | varchar(120) | 备注               |
| host          | varchar(120) | 主域               |
| ip            | varchar(30)  | 服务器ip地址          |
| port          | smallint(5)  | 端口号              |
| gitaddress    | varchar(120) | git组地址           |
| created_at    | dateTime     | 创建时间             |



### objects 任务

| 字段名        | 数据类型         | 备注               |
| ---------- | ------------ | ---------------- |
| id         | int(11)      | 自增长  primary key |
| group_id   | Int(11)      | 项目id             |
| name       | varchar(30)  | 任务名称，根据git地址来    |
| developer  | varchar(60)  | 开发者              |
| tester     | varchar(60)  | 测试者              |
| remark     | varchar(120) | 备注               |
| gitaddress | varchar(120) | git地址            |
| created_at | dateTime     | 创建时间             |



### tasks

| 字段名        | 数据类型        | 备注                      |
| ---------- | ----------- | ----------------------- |
| id         | int(11)     | 自增长  primary key        |
| version    | varchar(30) | 版本号                     |
| commit     | varchar(30) | git commit              |
| env        | tinyint(1)  | 类型(1:日常，2:预发，3:线上)      |
| status     | tinyint(1)  | 类型(1:发布中，2:发布成功，3:发布失败) |
| errormsg   | text        | 发布失败反馈信息                |
| operator   | varchar(30) | 发布者                     |
| created_at | dateTime    | 创建时间                    |

