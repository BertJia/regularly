

### Firebird语法整理

#### 1.从table2查询字段赋值给table1
```sql
update table1 a set cname=(select cname from table2 b where b where a.id=b.id) 
```
#### 2.无表示使用函数
```
select ‘我们开始使用InterBase/FireBird数据库了！’ as result from RDB$DATABASE
```
#### 3.sql语句把一列中的数据根据条件拆分成两列
1. 第一种方法
```
SELECT DCCP.WORKSHOP_NAME
    , DCCP.SECOND_BETWEEN AS STOP_TIME    , 0 AS PROD_TIMEFROM PS_DEV_CONN_CFG_PROD DCCPWHERE DCCP.STA_NAME = '停机'UNION ALLSELECT DCCP.WORKSHOP_NAME    , 0    , DCCP.SECOND_BETWEEN AS PROD_TIMEFROM PS_DEV_CONN_CFG_PROD DCCPWHERE DCCP.STA_NAME = '生产'
```
#### 2.第二种方法
```
SELECT DCCP.WORKSHOP_NAME,
      CASE        WHEN (DCCP.STA_NAME = '生产') THEN (DCCP.SECOND_BETWEEN)        ELSE 0      END AS PROD_TIME,      CASE        WHEN (DCCP.STA_NAME = '停机') THEN (DCCP.SECOND_BETWEEN)        ELSE 0      END AS STOP_TIME,      (DCCP.COUNTCHANGE) AS PROD_QTYFROM PS_DEV_CONN_CFG_PROD DCCPWHERE DCCP.STA_DATE = :STA_DATE
```
#### 3.Firebird的limit m，n
在其它SQL数据库里，有一个 Top n的子句，可以取头n条记录，Firebird不支持这个子句，但是它有更强大的子句：**FIREST n SKIP n**。
FIRST n表示提取头n条记录,SKIP n 表示从第几条开始提取，比如，我要从学生表里取语文成绩名次在11至15名的学生名单，
SQL语句如下：
`select first 5 skip 10 SNAME,YUWEN from achieve order by YUWEN desc`
#### 4.提取年月日时分秒
``` SQL
SELECT EXTRACT(YEAR FROM CURRENT_TIMESTAMP)
|| '-' || (IIF(CHAR_LENGTH(EXTRACT(MONTH FROM CURRENT_TIMESTAMP)) = 1, '0' || EXTRACT(MONTH FROM CURRENT_TIMESTAMP), EXTRACT(MONTH FROM CURRENT_TIMESTAMP)))
|| '-' || (IIF(CHAR_LENGTH(EXTRACT(DAY FROM CURRENT_TIMESTAMP)) = 1, '0' || EXTRACT(DAY FROM CURRENT_TIMESTAMP), EXTRACT(DAY FROM CURRENT_TIMESTAMP)))
|| ' ' || (IIF(CHAR_LENGTH(EXTRACT(HOUR FROM CURRENT_TIMESTAMP)) = 1, '0' || EXTRACT(HOUR FROM CURRENT_TIMESTAMP), EXTRACT(HOUR FROM CURRENT_TIMESTAMP)))
|| ':' || (IIF(CHAR_LENGTH(EXTRACT(MINUTE FROM CURRENT_TIMESTAMP)) = 1, '0' || EXTRACT(MINUTE FROM CURRENT_TIMESTAMP), EXTRACT(MINUTE FROM CURRENT_TIMESTAMP)))
|| ':' || (IIF(CHAR_LENGTH(EXTRACT(SECOND FROM CURRENT_TIMESTAMP)) = 1, '0' || ROUND(EXTRACT(SECOND FROM CURRENT_TIMESTAMP), 0), ROUND(EXTRACT(SECOND FROM CURRENT_TIMESTAMP), 0)))
|| ' ' || (CASE EXTRACT(WEEKDAY FROM CURRENT_TIMESTAMP)
WHEN 1 THEN '星期一'
WHEN 2 THEN '星期二'
WHEN 3 THEN '星期三'
WHEN 4 THEN '星期四'
WHEN 5 THEN '星期五'
WHEN 6 THEN '星期六'
WHEN 0 THEN '星期日'
		END
	)
	FROM BAS_VER

```

#### 5. 联合查询 UNION ORDER BY
firebird 的联合查询的子查询中不支持order by,所以如果需要order by, 则需要用 select * from 包装一下;  例如:
`select * from (select ... from tableA order by field desc ) union all select * from tableB` 