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

* Null: 如果列含有NULL，则含有YES。如果没有，则该列含有NO。

列表中的第二栏就是唯一索引，把它删除即可。

```
#按索引名字删除索引
DROP INDEX index_name ON talbe_name
或者
ALTER TABLE table_name DROP INDEX index_name

#删除主键
ALTER TABLE table_name DROP PRIMARY KEY
```



