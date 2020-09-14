# Oracle

## 函数

### case when

```sql
-- 用法一：简单case函数 依次分类
case  field 
when 'value' then  'caseOne'
when 'value' then  'caseTwo'
else  'caseOthers' end

--用法二：case搜索函数 归类
-- 一定要注意Case函数只返回第一个符合条件的值，剩下的Case部分将会被自动忽略
case when field = 'value' then 'caseOne'
when field = 'value' then 'caseTwo'
else 'caseOthers' end

--例如： 得到的结果
SELECT 
case when field in ('value1','value2') then  'caseOne'
-- 此种情况被忽略，按照第一种情况处理
when field in ('value2') then  'caseTwo'
else  'caseOthers' end
FROM tableName
```

### SYS_GUID()

```plsql
--生成全球唯一的id
select SYS_GUID() id,c.* from `TABLENAME` c
```

### vm_concat()

```plsql
--多列数据拼接
```

```plsql
--先以sys用户授权：
grant execute on dbms_lob to xxx;

CREATE OR REPLACE TYPE typ_concat_clob AS OBJECT
(
  concat_str CLOB,
  splitstr CLOB,
  --ODCIAggregateInitialize做一些初始化操作
  STATIC FUNCTION ODCIAggregateInitialize(cs_ctx In Out typ_concat_clob) return number,
  --ODCIAggregateIterate是主要的处理逻辑所在，这里定义一个迭代操作
  member function ODCIAggregateIterate(self In Out typ_concat_clob,srcvalue in VARCHAR2) return number,
  -- ODCIAggregateMerge是一个合并函数，如果在使用时指定了partition enabled，就必须定义此函数，用来把并行处理的结果进行合并
  member function ODCIAggregateMerge(self In Out typ_concat_clob,ctx2 In Out typ_concat_clob) return number,
  --ODCIAggregateTerminate是一个终止函数，顾名思义，在这个函数中对结果做最后处理并返回
  member function ODCIAggregateTerminate(self In Out typ_concat_clob,returnValue Out CLOB,flags IN NUMBER) return number
)

CREATE OR REPLACE TYPE BODY typ_concat_clob IS
  STATIC FUNCTION ODCIAggregateInitialize(cs_ctx In Out typ_concat_clob) return NUMBER
  IS
  BEGIN
    cs_ctx := typ_concat_clob(to_clob(','),to_clob(','));
    RETURN ODCICONST.SUCCESS;
  END;

  member function ODCIAggregateIterate(self In Out typ_concat_clob,srcvalue IN VARCHAR2) return NUMBER
  IS
  BEGIN
    dbms_lob.append(self.concat_str,to_clob(srcvalue));
    dbms_lob.append(self.concat_str,self.splitstr);
    RETURN ODCICONST.SUCCESS;
  END;

  member function ODCIAggregateMerge(self In Out typ_concat_clob,ctx2 In Out typ_concat_clob) return NUMBER
  IS
  BEGIN
    IF ctx2 IS NOT NULL THEN
      dbms_lob.append(self.concat_str,ctx2.concat_str);
    END IF;
    RETURN ODCICONST.SUCCESS;
  END;

  member function ODCIAggregateTerminate(self In Out typ_concat_clob,returnValue Out CLOB,flags IN NUMBER) return NUMBER
  IS
  BEGIN
    dbms_lob.trim(self.concat_str,dbms_lob.getlength(self.concat_str)-1);
    self.concat_str := dbms_lob.substr(self.concat_str,dbms_lob.getlength(self.concat_str)-1,2);
    returnValue := self.concat_str;
    RETURN ODCICONST.SUCCESS;
  END;
END;

CREATE OR REPLACE FUNCTION fun_concat_clob(in_str VARCHAR2) RETURN CLOB
  AGGREGATE USING typ_concat_clob;
```

### XMLELEMENT()

解决vm_concat()字符串超过4000 以及超出系统限制问题

```plsql
MERGE INTO xxx A
USING (SELECT BOND_ID,'"consignGroupInfo":[' ||rtrim(xmlagg(XMLELEMENT(e,xxx,',').EXTRACT('//text()') ).GetClobVal(), ',')  || ']' xxx
       FROM 
(SELECT BOND_ID,
              TO_CLOB(
                      TO_CLOB('{"xxxx":"' ||
                                        bb.xxxx
                                        ||
                                        '","xxxx":"' ||
                                        bb.xxxx
                                        ||
                                         '"}')) AS xxx
         FROM (



​               
SELECT 
​    d.*
  from table D
  join (select T.xx, T.xxxx, T.xxxxx
  from table2 t
 WHERE t.xxxx like '兴业银行股份有限公司%'
   AND t.xxx IN (1, 2)) cc
   on cc.xx = d.xx --where cc.xx = ''
  ) bb 
  ) v GROUP BY v.xx ) B
ON (A.xx = B.xx)  
WHEN MATCHED THEN
 UPDATE SET A.xxx = B.xxx;

--替换特殊字符
update  xxx t set t.xxx=replace(t.xxx,'&'||'quot'||chr(59),'"');
```

## 获取时间问题

```plsql
--常用
(select to_char(trunc(sysdate,'MM'),'yyyy-mm-dd') from dual)
(select to_char(last_day(sysdate),'yyyy-mm-dd') from dual)
--格式化
(select to_char(trunc(sysdate,'MM'),'yyyymmdd') from dual)
(select to_char(last_day(sysdate),'yyyymmdd') from dual)
--最后一天
select last_day(sysdate) from dual;


select trunc(sysdate) from dual  --2011-3-18  今天的日期为2011-3-18
select trunc(sysdate, 'mm')   from   dual  --2011-3-1    返回当月第一天.
select trunc(sysdate,'yy') from dual  --2011-1-1       返回当年第一天
select trunc(sysdate,'dd') from dual  --2011-3-18    返回当前年月日
select trunc(sysdate,'yyyy') from dual  --2011-1-1   返回当年第一天
select trunc(sysdate,'d') from dual  --2011-3-13 (星期天)返回当前星期的第一天
select trunc(sysdate, 'hh') from dual   --2011-3-18 14:00:00   当前时间为14:41 
select trunc(sysdate, 'mi') from dual  --2011-3-18 14:41:00   TRUNC()函数没有秒的精确
```

## 字符串转多列（包含with as）

```plsql
select m.mc, trunc(dbms_random.value(100, 1000)) zbz
  from (select substr(t, 1, instr(t, '、', 1) - 1) mc
          from (select substr(s, instr(s, '、', 1, rownum) + 1) || ',' as t,
                       rownum as d,
                       instr(s, ',', 1, rownum) + 1
                  from (select '、宾阳县、上林县、横县、隆安县、马山县、武鸣区、邕宁区、青秀区、西乡塘区、兴宁区、江南区、' as s
                          from dual)
                connect by instr(s, '、', '1', rownum) > 1)) m
 where m.mc is not null;
 
 
 SELECT REGEXP_SUBSTR('1,2', '[^,]+', 1, LEVEL)
          FROM DUAL
        CONNECT BY REGEXP_SUBSTR('1,2', '[^,]+', 1, LEVEL) IS NOT NULL
        
        
 with tmp as (SELECT REGEXP_SUBSTR('1,2,3', '[^,]+', 1, LEVEL) as text
          FROM DUAL
        CONNECT BY REGEXP_SUBSTR('1,2,3', '[^,]+', 1, LEVEL) IS NOT NULL)
        select cc.text,dd.text from (SELECT REGEXP_SUBSTR('1,2,3,4', '[^,]+', 1, LEVEL) as text
          FROM DUAL
        CONNECT BY REGEXP_SUBSTR('1,2,3,4', '[^,]+', 1, LEVEL) IS NOT NULL)  cc
        inner join tmp dd
        on dd.text = cc.text
```



##  查询当前用户下所有表结构信息

```plsql
select *
  from (select k1.TABLE_NAME 表名称,
               k3.COMMENTS 表描述,
               k1.COLUMN_NAME 字段名,
               k2.COMMENTS 字段名称,
               case
                 when k1.DATA_TYPE = 'VARCHAR2' then
                  k1.DATA_TYPE || '(' || k1.DATA_LENGTH || ')'
                 when k1.DATA_TYPE = 'NUMBER' and k1.DATA_PRECISION is null and
                      k1.DATA_SCALE is null then
                  k1.DATA_TYPE
                 when k1.DATA_TYPE = 'NUMBER' and
                      k1.DATA_PRECISION is not null and
                      (k1.DATA_SCALE is null or k1.DATA_SCALE = 0) then
                  k1.DATA_TYPE || '(' || k1.DATA_PRECISION || ')'
                 when k1.DATA_TYPE = 'NUMBER' and
                      k1.DATA_PRECISION is not null and
                      k1.DATA_SCALE is not null and k1.DATA_SCALE <> 0 then
                  k1.DATA_TYPE || '(' || k1.DATA_PRECISION || ',' ||
                  k1.DATA_SCALE || ')'
                 else
                  k1.DATA_TYPE
               end 类型,
               decode(k1.NULLABLE, 'N', 'Not Null', 'Y', 'Null', k1.NULLABLE) 是否空
          from all_tab_columns k1, all_col_comments k2, all_tab_comments k3
         where k1.owner = '用户名'
           and k2.owner = '用户名'
           and k3.owner = '用户名'
           and k1.TABLE_NAME = k2.TABLE_NAME
           and k1.TABLE_NAME = k3.TABLE_NAME
           and k1.COLUMN_NAME = k2.COLUMN_NAME) 

       --where 表名称='ZQBA_FXSBAXX_TUPLE_FUJIAN' 
```



# PLSQL导出数据

```plsql
--PL/SQL 
--选择tool 
--export table
--选择所需要的table
--选择第二项 insert sql
--选择文件路径
--设置名字
```

##  数据导出导入

使用PL/SQL导入导出全量数据（demo）

```plsql
/**
label:
	From: test
	To:local
	include: user tablespace table
**/
--select TABLESPACE
--SELECT dbms_lob.substr(DBMS_METADATA.GET_DDL('TABLESPACE', TS.tablespace_name)) FROM DBA_TABLESPACES TS;

--Create the TABLESPACE
CREATE TABLESPACE "tablespace_name" DATAFILE 
  'path\tablespace_name.DBF' SIZE 52428800
  AUTOEXTEND ON NEXT 52428800 MAXSIZE 32767M
  LOGGING ONLINE PERMANENT BLOCKSIZE 8192
  EXTENT MANAGEMENT LOCAL AUTOALLOCATE DEFAULT NOCOMPRESS  SEGMENT SPACE MANAGEMENT AUTO
   ALTER DATABASE DATAFILE 
  'path\tablespace_name.DBF' RESIZE 262144000

-- Create the user 
create user username
  default tablespace tablespace_name
  temporary tablespace TEMP
  profile DEFAULT
  quota unlimited on tablespace_name
-- Grant/Revoke system privileges 
grant alter session to username;
grant create any procedure to username;
grant create sequence to username;
grant create session to username;
grant create table to username;
grant create view to username;
grant execute any procedure to username;
grant select any dictionary to username;

--Tools -> Export the UserObjects
/**
select the user INFODISC(All) -> export
**/

--Tools -> Export the Tables 
/**
SQL inserts 
select tables(All) -> export
**/
```




error
1.ora-00054资源正忙,但指定以nowait方式
#解决方案
在system登录的情况下执行如下操作：

1.查询被锁的会话ID：
select session_id from v$locked_object;
查询结果result：SESSION_ID-------48

2.查询上面会话的详细信息：
SELECT sid, serial#, username, osuser FROM v$session where sid = 48;
序号    SID   SEARIAL#   USERNAME        OSUSER   
1	48	67	INFODISC	LAPTOP-IC0IKSOR\lenovo

3.将上面锁定的会话关闭：
ALTER SYSTEM KILL SESSION '48,67';