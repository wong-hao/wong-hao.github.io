---
# Course title, summary, and position.
linktitle: 数据库管理系统及其实现
summary: 老课新学
weight: 4

# Page metadata.
title: Overview
date: "2020-10-12T00:00:00Z"
#lastmod: "2018-09-09T00:00:00Z"
draft: false  # Is this a draft? true/false
toc: true  # Show table of contents? true/false
type: docs  # Do not modify.

# Add menu entry to sidebar.
# - name: Declare this menu item as a parent with ID `name`.
# - weight: Position of link in menu.
menu:
  example:
    name: 数据库管理系统及其实现
    weight: 4
---

### 1 课件
#### 1.1 总览


#### 1.2 分章节
[1-Introduction-v2](https://onedrive.gimhoy.com/sharepoint/aHR0cHM6Ly9zZXVlZHVjbjEtbXkuc2hhcmVwb2ludC5jb20vOmI6L2cvcGVyc29uYWwvMjIwMjA0NjAxX3NldV9lZHVfY24vRVRQREduSnZ1STFKaWdiLUQwWUpOd1lCdVU3Z0p5QS1UaTFzU0lUSjdCY1Zidz9lPWZxYldXaQ==.mp3)

[2-关系模型-v2](https://onedrive.gimhoy.com/sharepoint/aHR0cHM6Ly9zZXVlZHVjbjEtbXkuc2hhcmVwb2ludC5jb20vOmI6L2cvcGVyc29uYWwvMjIwMjA0NjAxX3NldV9lZHVfY24vRWVXZDc0M29pRDVGdThMYkZnbmJ5N2NCSnVwamFrcGZ2cTZvcFVYTFB3YU00dz9lPVFmNHp4NQ==.mp3)

[3-查询语言-v2](https://onedrive.gimhoy.com/sharepoint/aHR0cHM6Ly9zZXVlZHVjbjEtbXkuc2hhcmVwb2ludC5jb20vOmI6L2cvcGVyc29uYWwvMjIwMjA0NjAxX3NldV9lZHVfY24vRVphZmo3ZUhFejFCb2UtT3k1MzIzcnNCOFE2cjRtcThpRDIyTFp4Q1pSLTJ2QT9lPUlVc1d3Uw==.mp3)

#### 1.3 教材


### 2 作业
1. 用关系模型去描述你认为最复杂的模型，如**球员转会**
2. 怎么把知识图谱存放在MySql中、怎么查询、这种查询与平常的SQL查询在时间开销上有什么差别

### 3 论文

#### 3.1 论文要求

DDL: 2020.12.08

[阅读](https://onedrive.gimhoy.com/sharepoint/aHR0cHM6Ly9zZXVlZHVjbjEtbXkuc2hhcmVwb2ludC5jb20vOmI6L2cvcGVyc29uYWwvMjIwMjA0NjAxX3NldV9lZHVfY24vRWQ2T2ZGOUxscFpIcGo0UGJmclEwNlFCY2tESlcyU1h3QjNhV3RjRGtUMWlkZz9lPWNPQm51Tg==.mp3)

---
**~~以下考试重点内容作废：~~**

#### 3.2 Data Model
1. Data Structure
2. Data Operation
3. Data Constraint



#### 3.3 SQL Query: Descriptive Language

10':

| Name | Sex | Child |
| ------- | ------- | ------- |
|   Cell  |   Cell  |   Cell  |
|   Cell  |   Cell  |   Cell  |

> Q: Who are married?

A: people who have common children


| Name | Sex | Age | Image |
| ------- | ------- | ------- | ------- |
|   Cell  |   Cell  |   Cell  | Cell    |
|   Cell  |   Cell  |   Cell  | Cell    |

> Q: Who have the same image?

A: *

---

5'： 
    select * from *
    group by * 
    having *


---

10':

> Find all customers who have both an account and a loan at the bank.

```sql
select distinct customer_name
    from borrower
    where customer_name in(select customer_name
                           from depositor)
```

> Find all customers who have a loan at the bank but do not have an account at the bank.

```sql
select distinct customer_name
   from borrower
   where customer_name not in(select customer_name
                              from depositor)
```

10':

> Find the names of all branches that have greater assets than all branches located in Brooklyn.

```sql
select branch_name
    from branch
    where assets>all
        (select assets
         from branch
         where branch_city = 'Brooklyn')
```
---

10':

> Find all cusomers who have an account at all branches located in Brooklyn.

```sql
select distinct S.customer_name
    from depositor as S
    where not exists(
      (select branch_name
      from branch
      where branch_city = 'Brooklyn')
          except
      (select R.branch_name
      from depositor as T, account as R
      where T.account_number - R.account_number and
          S.customer_name - T.customer_name))
```

---

10':

```sql
select SNO
from student S
where not exists(
  select *
  from course C
  where not exists(
    select
    from S_C
    where S_C.SNO = S.SNO and S_C.CNO.CNO = C.CNO
    )
       )
```

#### 3.4 Data Storage
**存储介质**
1. 内存：64B
2. 磁盘：512kB
3. 单位大小不一样

表格放到磁盘上：逻辑->物理
B+ tree
Cloud：P2P->水平/垂直拓展
集群

两张表格放到磁盘上
key: B+ tree
value：指针