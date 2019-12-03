## 结构化查询语言
### DQL/DML
- INSERT table_name (列1, 列2,...) VALUES (值1, 值2,....)
- DELETE FROM 表名称 WHERE 列名称 = 值
- UPDATE 表名称 SET 列名称 = 新值 WHERE 列名称 = 某值
- SELECT 列名称 FROM 表名称
- TRUNCATE TABLE 表名称

### DDL/DCL
- CREATE TABLE 表名称 (字段名1 类型1, ……, 字段名n 类型n)
- DROP TABLE 表名称
- ALTER TABLE 表名称 ALTER COLUMN 列名称 数据类型
- GRANT 权限 TO 用户名
- REVOKE 权限 FROM 用户名

### 完整性约束(逻辑限制)
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
- group by … 分组的依据
- having … 分组的限制条件
- order by … desc/asc 降序/升序

### 函数
- length 字符串长度(oracle, mysql, sybase均支持)
- trim 去除首尾空格
- substr 子串
- lower/upper 大小写转换
- avg 平均值
- min/max 最值
- sum 求和
- count 计数
- stddev 求标准差

### 多表关联
- left join 左外连接
- inner join 内连接
- right join 右外连接

### 日常集锦
> Oracle
```sql
/* CodeStyle: FROM/子查询为一个block，进行缩进。*/

--查看所有表
select * from user_tables;

--查看TBHISQRHZ的表结构
select * from user_tab_columns where table_name = 'TBHISQRHZ'

--查询后的虚拟视图tmp, tmp2
with tmp as(
    select 1 rank, 'llf' name from dual union all
    select 2 rank, 'tlj' name from dual union all
    select 3 rank, 'tlj' name from dual
), 
tmp2 as (
    select 't' test from dual
)
select * from tmp, tmp2;

--排序后分页
SELECT * FROM (
    SELECT T2.*, ROWNUM AS RANK FROM (
		--排序语句 
	) T2 WHERE ROWNUM <= 20
) T3 WHERE T3.RANK >= 10;

--数字补零对齐
select LPAD(amount, 5, '0') from TBZ;

--java.lang.String#split
SELECT result FROM (   --匹配分隔符的正则表达式
    SELECT REGEXP_SUBSTR(str, '[^,，;-]+', 1, rownum) result FROM (
        SELECT 'a1， b2, c3; d4-e5' str  --待分割的字符串
    	FROM XMLTABLE('1 to 200')  --循环200次，不易太大。禁用connect by，因为存在内存问题。
    ) 
) outer WHERE result is NOT null; --取非空的截取

--开窗函数(partition by): 更灵活的分组策略。以下语句编译报错，group by限制太死。
--select type, amount, sum(amount) from tbz group by type order by amount;
select type, amount, sum(amount) over(partition by type) from tbz order by amount; --只分组
select type, amount, sum(amount) over(partition by type order by amount) from tbz order by amount; --分组后排序，逐项累加
select type, amount, min(amount) over(partition by type order by amount) AS MIN, max(amount) over(partition by type order by amount) AS MAX from tbz order by amount; --循环求最值
select type, amount, rank() over(partition by type order by amount desc) AS RANK from tbz order by amount desc; --分组求排名
select type, amount, rank() over(order by amount desc) AS RANK from tbz order by amount desc; --只排名不分组

--将同一列的多个值拼接
with temp as(
   select 500 population, 'China' nation ,'Guangzhou' city from dual union all
   select 1500 population, 'China' nation ,'Shanghai' city from dual union all
   select 500 population, 'China' nation ,'Beijing' city from dual union all
   select 1000 population, 'USA' nation ,'New York' city from dual union all
   select 500 population, 'USA' nation ,'Bostom' city from dual union all
   select 500 population, 'Japan' nation ,'Tokyo' city from dual
)
select population,nation,city,
   listagg(upper(city), ',') --被合并的列名，分隔符
   within GROUP (order by city) --组内排序条件
   over(partition by nation) --分组的依据
   AS combine_row
from temp

--根据汉字的拼音排序(支持部首、笔画)
SELECT * FROM (
    select '你好' as chinese from dual union all
    select '还行' as chinese from dual union all
    select '再见' as chinese from dual 
) ORDER BY NLSSORT(chinese,'NLS_SORT = SCHINESE_PINYIN_M')

--当前行是第n行，获取第(n-3)行
select lag(n, 3) over(order by n asc) from (
    select 1 n from dual union all
    select 2 n from dual union all
    select 3 n from dual union all
    select 4 n from dual union all
    select 5 n from dual
)
--当前行是第n行，获取第(n+3)行
select lead(n, 3) over(order by n asc) from (
    select 1 n from dual union all
    select 2 n from dual union all
    select 3 n from dual union all
    select 4 n from dual union all
    select 5 n from dual
)

--生成UUID
select sys_guid() from xmltable('1 to 5')

--排列
select sys_connect_by_path(element, ' ') from (
    select rownum element from xmltable('1 to 5') --总共有m个小球
) where level <= 2 --取出n个小球
connect by nocycle element <> prior element
--组合
select * from (
    select A, B, C from (
        select 'a' A, 'b' B, 'c' C from dual
    ) group by cube (A, B, C)
) where not (A is null and B is null and C is null)

```

