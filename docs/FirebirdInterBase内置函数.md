### Firebird/InterBase内置函数使用说明（*\*号为FB2.0加入的函数*)
#### 内置函数列表
##### 1. COUNT, AVG, MAX, MIN, SUM
说明:通用统计函数,不详细介绍了

##### 2. EXTRACT(timestamp_part FROM value)
说明:EXTRACT(YEAR/MONTHE/DAY/WEEKDAY FROM 字段名)
从日期型字段中分离出年,月,日及一个星期的第几天

#####  3. CAST(value AS datatype)
说明:转换数据类型

##### 4. LOWER() *
说明:返回小写值

##### 5. UPPER()
说明:返回大写值

##### 6. TRIM() *
说明:去除字符串两边的空格

##### 7. SUBSTRING(string FROM pos FOR length)
说明:取字符串子串,注意,第一个字符的位置是1

##### 8. BIT_LENGTH *
说明:返回字符串位(bit)数

##### 9. CHAR_LENGTH/CHARACTER_LENGTH *
说明:返回字符串字符数

##### 10. OCTET_LENGTH *
说明:返回字符串字节数

##### 11. CASE
说明:通过执行外来的一组条件取得相应的返回值
举例
i) 简单
SELECT o.ID, o.Description,
CASE o.Status
WHEN 1 THEN 'confirmed'
WHEN 2 THEN 'in production'
WHEN 3 THEN 'ready'
WHEN 4 THEN 'shipped'
ELSE 'unknown status ''' || o.Status || ''''
END
FROM Orders o;
ii) 表达式
SELECT o.ID, o.Description,
CASE
WHEN (o.Status IS NULL) THEN 'new'
WHEN (o.Status = 1) THEN 'confirmed'
WHEN (o.Status = 3) THEN 'in production'
WHEN (o.Status = 4) THEN 'ready'
WHEN (o.Status = 5) THEN 'shipped'
ELSE 'unknown status ''' || o.Status || ''''
END
FROM Orders o;

##### 12. IIF (<search_condition>, <value1>, <value2>)
说明:表达式为真,返回value1否则返回value2
等价于以下语句:
CASE
WHEN <search_condition> THEN <value1>
ELSE <value2>
END

##### 13. NULLIF(V1,V2)
说明:如果V1=V2,返回NULL,否则返回V1
等价于以下语句:
CASE WHEN V1 = V2 THEN NULL ELSE V1 END
举例
UPDATE PRODUCTS
SET STOCK = NULLIF(STOCK,0)

##### 14. COALESCE(V1, V2, …, Vn)
说明:如果V1为Null,返回V2,否则返回V1,
如果 n >= 3, 等于如下case语句:
CASE
WHEN V1 IS NOT NULL THEN V1
ELSE COALESCE (V2,...,Vn)
END
举例:
SELECT
PROJ_NAME AS Projectname,
COALESCE(e.FULL_NAME,'[< not assigned >]') AS Employeename
FROM
PROJECT p
LEFT JOIN EMPLOYEE e
ON (e.EMP_NO = p.TEAM_LEADER);
SELECT
COALESCE(Phone,MobilePhone,'Unknown') AS "Phonenumber"
FROM Relations;

#### 常用语句

##### 1.  获当前日期：
select current_date from rdb$database  
获当前时间：
select current_time from rdb$database

##### 2. Firebird数据库的取值范围 limit m，n
在其它SQL数据库里，有一个 Top n的子句，可以取头n条记录，Firebird不支持这个子句，但是它有更强大的子句：FIREST n SKIP n。
FIRST n表示提取头n条记录,SKIP n 表示从第几条开始提取，比如，我要从学生表里取语文成绩名次在11至15名的学生名单，SQL语句如下：
select first 5 skip 10 SNAME,YUWEN from achieve order by YUWEN desc

#####  3. 分页写法小例:
SELECT FIRST 10 templateid,code,name FROM template ;
SELECT FIRST 10 SKIP 10 templateid,code,name FROM template ;
SELECT * FROM shop ROWS 1 TO 10;   --firebird2.0支持这种写法

##### 4. 显示表名和表结构
SHOW TABLES;
SHOW TABLE tablename;
##### 5. 使用ISQL连接数据库
firebird%92bin>isql -u sysdba -p masterkey
   SQL>CONNECT 'E:companyxmwsoftnewxmwsoftc2dbcts2.fdb';
或
SQL>CONNECT 'E:%92'
CON>USER 'sysdba'
CONT>PASSWORD 'masterkey';

##### 6. 更新字段注释
UPDATE RDB$RELATION_FIELDS
SET RDB$DESCRIPTION = '描述信息'
WHERE (RDB$RELATION_NAME = 'SHOP')
AND (RDB$FIELD_NAME = 'CREDIT_BUY')

##### 7. 显示字段注释
SELECT RDB$FIELD_NAME,RDB$DESCRIPTION
FROM RDB$RELATION_FIELDS
WHERE (RDB$RELATION_NAME = 'SHOP')
AND (DB$FIELD_NAME = 'CREDIT_BUY')

##### 8. 更新表注释
UPDATE RDB$RELATIONS
SET RDB$DESCRIPTION = '描述信息'
WHERE RDB$RELATION_NAME = 'TABLE_NAME';

##### 9. 查询所有的表和视图（包括系统表和系统视图）
SELECT RDB$RELATION_NAME
FROM RDB$RELATIONS;

##### 10. 查询所有的用户表和用户视图*

SELECT RDB$RELATION_NAME
FROM RDB$RELATIONS
WHERE RDB$SYSTEM_FLAG = 0;

##### 11. 查询所有的用户表**
SELECT RDB$RELATION_NAME
FROM RDB$RELATIONS
WHERE RDB$SYSTEM_FLAG = 0
    AND RDB$VIEW_BLR IS NULL;

##### 12. 查所有用户表、用户视图所有字段及相关定义**
  SELECT
   a.RDB$RELATION_NAME,
   b.RDB$FIELD_NAME,
   b.RDB$FIELD_ID,
   d.RDB$TYPE_NAME,
    c.RDB$FIELD_LENGTH,
   c.RDB$FIELD_SCALE
  FROM RDB$RELATIONS a
    INNER JOIN RDB$RELATION_FIELDS b
    ON a.RDB$RELATION_NAME = b.RDB$RELATION_NAME
  INNER JOIN RDB$FIELDS c
   ON b.RDB$FIELD_SOURCE = c.RDB$FIELD_NAME
  INNER JOIN RDB$TYPES d
      ON c.RDB$FIELD_TYPE = d.RDB$TYPE
  WHERE a.RDB$SYSTEM_FLAG = 0
  AND d.RDB$FIELD_NAME = 'RDB$FIELD_TYPE'
  ORDER BY a.RDB$RELATION_NAME, b.RDB$FIELD_ID;

##### 13. 查找某表的所有字段及相关定义**
  SELECT
   A.RDB$FIELD_NAME,
   B.RDB$FIELD_TYPE,
   B.RDB$FIELD_LENGTH,
   B.RDB$FIELD_PRECISION,
   B.RDB$FIELD_SCALE
  FROM RDB$RELATION_FIELDS A, RDB$FIELDS B
  WHERE A.RDB$RELATION_NAME = 'tablename'
  AND A.RDB$FIELD_SOURCE = B.RDB$FIELD_NAME
  ORDER BY A.RDB$FIELD_POSITION;

##### 14. 查找某表的主键定义字段**
  SELECT A.RDB$FIELD_NAME
FROM RDB$INDEX_SEGMENTS A, RDB$RELATION_CONSTRAINTS B
  WHERE B.RDB$CONSTRAINT_TYPE = 'PRIMARY KEY'
  AND B.RDB$RELATION_NAME = 'tablename'
    AND A.RDB$INDEX_NAME = B.RDB$INDEX_NAME
  ORDER BY A.RDB$FIELD_POSITION;

##### 15. 查找某表的外键定义******
  SELECT
   r1.RDB$CONSTRAINT_NAME,
   rind.RDB$FIELD_NAME,
   r2.RDB$RELATION_NAME
  FROM
   RDB$RELATION_CONSTRAINTS r1,
   RDB$RELATION_CONSTRAINTS r2,
    RDB$REF_CONSTRAINTS ref,
   RDB$INDEX_SEGMENTS rind
  WHERE r1.RDB$RELATION_NAME = 'tablename'
   AND r1.RDB$CONSTRAINT_TYPE = 'FOREIGN KEY'
    AND r1.RDB$CONSTRAINT_NAME = ref.RDB$CONSTRAINT_NAME
  AND ref.RDB$CONST_NAME_UQ = r2.RDB$CONSTRAINT_NAME
    AND r1.RDB$INDEX_NAME = rind.RDB$INDEX_NAME;