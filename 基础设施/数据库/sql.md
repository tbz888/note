## 结构化查询语言
- INSERT table_name (列1, 列2,...) VALUES (值1, 值2,....)
- DELETE FROM 表名称 AS 别名 WHERE 列名称 = 值
- UPDATE 表名称 SET 列名称 = 新值 WHERE 列名称 = 某值
- SELECT 列名称 FROM 表名称

### DDL
DROP TABLE 表名称
ALTER TABLE 表名称 ALTER COLUMN 列名称 数据类型

### 约束(逻辑限制)
- NOT NULL 非空
- UNIQUE 唯一
- PRIMARY KEY 主键
- FOREIGN KEY 外键
- CHECK 范围限制
- DEFAULT 默认

### 过滤条件
WHERE 列 运算符 值  (支持NOT、AND和OR的逻辑运算)
- LIKE 模式匹配
- IN 匹配值的列表
- NOT EXISTS 不匹配的条件 (禁用NOT IN)
- IS 类型判断
- BETWEEN 范围匹配
### 分组、排序
group by … 分组的依据
having … 分组的限制条件
order by … desc/asc 降序/升序

### 函数
- length 字符串长度(oracle, mysql, sybase均支持)
- trim 去除首尾空格
- substr 子串
- lower/upper 大小写转换
- avg 平均值
- min/max 最值
- sum 求和
- count 计数

### 关联
- left join 左外连接
- inner join 内连接
- right join 右外连接

### 示例
> Oracle
```sql
--查看所有表
select * from user_tables;

--查看TBHISQRHZ的表结构
select * from user_tab_columns where table_name = 'TBHISQRHZ'

--查询后的虚拟视图tmp, tmp2
with tmp as(
     select 1 rank, 'llf' name from dual union all
     select 2 rank, 'tlj' name from dual union all
     select 3 rank, 'tlj' name from dual
), tmp2 as (select 't' test from dual)
select * from tmp, tmp2;

--排序后分页
SELECT * FROM (SELECT T2.*, ROWNUM AS RANK FROM (
	--排序语句 
)T2 WHERE ROWNUM <= 20) T3 WHERE T3.RANK >= 10;
```