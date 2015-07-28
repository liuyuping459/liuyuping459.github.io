---
layout: post
title: "sql语句简单手册"
date: 2015-07-28 16:31:00
author: liuyp
categories: sql
tags: sql
---

* content
{:toc}

适用于有一定sql经验，但是又不专注sql，偶尔要用一下，思想清楚，但是有些具体实现记不清的使用者。





###查找排序
select distinct id_, name_
from rbr_metadata
where pid_ = '2'
order by 2, 1 desc;
去重 按name_ 升序，id_降序排

###统计
select count(distinct pid_) pid
from rbr_metadata;
统计去重额pid有多少种，就是有多少种不同的pid，别名pid，查出来的表显示别名
where pid_ <> '2'
pid不等于2  也可以！=

###逻辑操作 where精确条件
select id_ id, name_ name
from rbr_metadata

where manager_name_ is null;    isnull
where id_ between 2 and 6;        between and 包含最大最小值
where id_ in ('2','4');                    in
where name_ like '_前%';            like _代表一个字符，%代表零个一个或多个字符
where exists (select * from rbr_metadata where pid_ < 3);    exists
where pid_ > all (select id_ from rbr_metadata where pid_ < 3);    all集合 要求比pid<3的所有的pid大
where pid_ > any (select id_ from rbr_metadata where pid_ < 3);    any 要求比pid<３的任意一个的pid大  some和any一样用

where pid_ = 1 or pid_ = 2;        and  or 条件操作符
where pid_ = 0 and (id_ = 2 or id_ = 3);

not between  结果不包含边界值
not in
not like 
is not null
not exists    

select id_ id, name_ name
from rbr_metadata
where id_ + pid_ > 10;    加减乘除运算


###汇总函数
count    有多少符合的字段
select count(distinct pid_) pid
from rbr_metadata;    不重复的统计pid_有多少种

sum    字段的总和  只能处理数值型
select sum(pid_) pid
from rbr_metadata

avg    平均值  处理数值型
select avg(pid_) pid
from rbr_metadata

max min 最大最小值


###排序与分组
select pid_ pid, count(*) count
from rbr_metadata
group by pid_;
每个pid的个数
排序也可以用group by实现，但如果不用汇总函数，用group by没有意义。select字段，除了汇总字段之外，其他字段全部都要出现在group by字句里。

rollup    在全部分组的基础上，对其中的一部分进行汇总
cube    对所有字段进行排列组合，并根据每一种组合结果，分别进行统计汇总

having
设置group by子句形成分组的条件，分好之后汇总值的筛选

select
from
where
group by
having
order by    

select pid_ pid, avg(id_) count
from rbr_metadata
where pid_ <> 0
group by pid_;
having avg(id_) < 20
order by 2;
依据 pid分组，除去pid——是0的，平均id_小于20的，依据avg（id_）排序

###调整外观

连接
select concat(name_, level_)
from rbr_metadata
MySQL的，不同数据库不一样

translate(name_, '当', 'qian')    替换
replace    替换
upper    将字符串里的小写转换成大写
lower    转换成小写
substr 获取字符串子串 不同数据库不一样


###表结合
1、等值结合或内部结合
select rbr_rules.id_, rbr_rules_conclusion.conclusion_meta_data_id_
from rbr_rules, rbr_rules_conclusion
where rbr_rules.id_ = rbr_rules_conclusion.rules_id_;

使用表别名
select r.id_,c.conclusion_meta_data_id_
from rbr_rules r, rbr_rules_conclusion c
where r.id_ =c.rules_id_;


用inner jion  表名 on 结合操作符
select rbr_rules.id_, rbr_rules_conclusion.conclusion_meta_data_id_
from rbr_rules
inner join rbr_rules_conclusion on rbr_rules.id_ = rbr_rules_conclusion.rules_id_;
返回结果与上面一样。

2、不等值结合
不等值结合，每条不等的都会结合。

3、外部结合
外部结合返回一个表里的全部记录，即使对应的记录在第二个表里不存在。（+）在查询表里表示外部结合。
具有（+）的表是没有匹配记录的表。（+）表所用都显示，对应表没有对应记录也要显示一行null
left|right|full  jion
jion外部结合
select rbr_rules.id_, rbr_rules_conclusion.conclusion_meta_data_id_
from rbr_rules
left join rbr_rules_conclusion on rbr_rules.id_ <> rbr_rules_conclusion.rules_id_;

4、自结合
返回所用相同的值，自己跟自己比较，像使用两个不同的表一样。
select a.id_, b.id_
from rbr_rules a
inner join rbr_rules b on a.id_ = b.id_ [and   =    用于结合多个主键];

5、基表
要结合两个没有公共字段的表，就必须结合另外一个表，这个表与前两个表都有公共字段，这个表就称为基表。

6、笛卡尔积
没有where子句，结合输出的就是所有被选表里全部数据的结合。

###子查询
where子句里的查询，返回的数据通常在主查询里作为一个条件。
子查询必须位于括号里。
子查询返回的字段应于主查询相对应。
子查询不能用order by 可以用group by。
返回多条记录的子查询，只能与多值操作符，比如in配合使用。not in  and  or
between不能子查询，子查询里内部可以用between。
子查询在主查询之前执行。

###组合多个查询
union 
每个select子句中必须选择同样数量的字段、字段表达式、数据类型和次序。 union不会返回重复的数据。
union all 返回重复的结果
intersect 只返回两个查询里一样的记录
except 返回第一个有第二个没有的记录


###用到的数据表结构
上文中主要用的rbr_metadata表字段：
id_(key)		int
pid_			int
name_		varchar
level_		int
leaf_			bit
rlibility_		int
manager_		varchar
passtime_		date

还有两张比较简单的表
rbr_rules.id_ 和 rbr_rules_conclusion.rules_id_两个字段是外键关联的