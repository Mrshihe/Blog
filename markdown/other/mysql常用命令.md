### mysql常用命令（命令后的;必须写/linux下命令区分大小写，windows下不区分）
- create database [数据库名];  新增数据库
- show databases; 查看所有数据库
- use [数据库名]; 选择某个数据库
- status; 查看当前数据库信息
- drop database [数据库名]; 删除数据库
- show tables; 查看数据库内数据表
- show create table [数据表名]; 查看数据表创建语句
- desc [数据表信息]; 查看数据表信息
- show create table [数据表名] \G; 查看数据表信息 含字符集等信息 \G 可选, 按照字段竖着排列
- drop table [数据库名]; 删除数据表
- alter table [数据表名] rename [新数据表名]; 修改数据表名
- alter table [数据表名] modify [字段名] [修改结构] [?first|after 字段名]; 修改数据表结构
- alter table [数据表名] add column [字段名] [字段类型] [?first|after 字段名]; 添加数据表字段
- alter table [数据表名] drop column [字段名]; 删除数据表字段
```
[?first|after 字段名] 可选参数, 决定位置最前 | 指定字段后
```
- alter table [数据表名] change [旧字段名] [新字段名] [字段类型]; 修改数据表字段名称

#### DML操作(指对数据库中表记录的操作: 插入 insert、更新 update、删除 delete、查询 select )
- insert into [数据表名] (field1,...fieldn) values (value1,..valuen),(value1,...valuen); 向数据表中插入数据,可同时插入多个 字段名(field)可忽略但values顺序应和字段排列顺序一致
- update [数据表名] set field1=value1, ...fieldn=valuen where [查询条件]; 根据条件更新数据表中字段
- update [数据表名1] [?别名1], [数据表名2] [?别名2] set [数据表1].[字段名1]=[值], [数据表2][字段名1]=[值] where [查询条件];
更新多个数据表字段
- delete from [数据表名] where [查询条件]; 删除指定记录
- select * from [数据表名]; 查询表中所有内容
- select * from [数据表名] where [查询条件] and where [查询条件]; 同时满足多个查询条件
- select * from [数据表名] where [查询条件] or where [查询条件]; 匹配其中一个查询条件即可 
- select * from [数据表名] where [查询条件] in (值1, 值2, 值3); 匹配相应值的集合 用法和 or 基本一致 in 可以包含其他select语句
```
同时包含and 和 or 查询条件时, and优先级最高, 如需要可以使用圆括号明确分组相应的操作符
```
- select * from [数据表名] where [查询字段] not [字段值]; 查询除指定条件的数据
- select field1,...fieldn from [数据表名]; 查询指定字段
- select distinct [字段名] from [数据表名]; 查询指定字段并且去重，distinct不能用于部分字段名
```
查询常用条件
order by 排序 desc 降序排列 asc 升序排列 默认 acs;
select * from [数据表名] order by [字段名] [?desc|acs]
select * from [数据表名] order by [字段名1] , [字段名2] desc;
排序中存在多个字段,仅在字段1存在相同值时,才对字段2进行排序

limit [offset_start, row_count] 显示部分数据, offset_start 起始位置 默认为0 row_count 要显示行数
以下3句话效果一样：
select * from [数据表名] where [查询条件] limit 3;
select * from [数据表名] where [查询条件] limit 0, 3;
select * from [数据表名] where [查询条件] limit 3 offset 0;
```
```
where 常用 条件操作符
= 等于
<> 不等于
!= 不等于
< 小于
<= 小于等于
> 大于
>= 大于等于
between 指定两个值之间  between 5 and 10 包含指定的开始值和结束值

```
- select * from [数据表名] where [字段名] is null; 是否为空值