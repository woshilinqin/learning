* # mysql中字段唯一索引unique

### 1.增加

```
alter table trans_push_task add unique key(orderNo);
```

### 2.删除

增加过后查看索引，新建查询。

```
show INDEX from trans_push_task;
或者
show keys FROM trans_push_task；
```

###### 查询结果\(只列出几个重要的\)：

| Table | Non\_unique | Key\_name | Column\_name |
| :--- | :--- | :--- | :--- |
| trans\_push\_task | 0 | PRIMARY | id |
| trans\_push\_task | 0 | orderNo | orderNo |

* Table: 表的名称。

* Key\_name: 索引的名称。

* Column\_name: 对应列名称。

* Non\_unique: 是否唯一，0是，1否。



