# 答:渐变维度——在 Hive 中实现 SCD1 和 SCD2

> 原文：<https://dev.to/mrvemuri/answer-slowly-changing-dimensions-scd1-and-scd2-implementation-in-hive-1l6k>

<header>

# ![](img/01c67cd39e9a8e551fcb75e1091225e9.png) [ 回答 re:渐变维度——sc D1 和 SCD2 在 Hive 中的实现](https://stackoverflow.com/questions/37472146/slowly-changing-dimensions-scd1-and-scd2-implementation-in-hive/57718516#57718516)

Aug 29 '19[![](img/e3f0373ec76330150a340eacd410b600.png)0![](img/f7bb704c8c93dfae05d2b57012ed2754.png)](https://stackoverflow.com/questions/37472146/slowly-changing-dimensions-scd1-and-scd2-implementation-in-hive/57718516#57718516) </header>

```
drop table if exists harsha.emp
drop table if exists harsha.emp_tmp1;

drop table if exists harsha.emp_tmp2;

drop table if exists harsha.init_load;

show databases;
use harsha;
show tables;

create table harsha.emp (eid int,ename string,sal int,loc string,dept int,start_date timestamp,end_date timestamp,current_status string)
comment "emp scd implementation"
row format delimited
fields terminated by ','
lines
```

…<button class="ltag__stackexchange--btn" type="button">[Open Full Answer](https://stackoverflow.com/questions/37472146/slowly-changing-dimensions-scd1-and-scd2-implementation-in-hive/57718516#57718516)</button>