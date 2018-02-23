# mysql中字段唯一索引unique

### 1.增加

```
alter table trans_push_task add unique key(orderNo);
```

### 2.删除

增加过后查看索引，新建查询。

```
show INDEX from trans_push_task
```

###### 查询结果\(只列出几个重要的\)：

| Table | Key\_name | Column\_name |
| :--- | :--- | :--- |
| trans\_push\_task | PRIMARY | id |
| trans\_push\_task | orderNo | orderNo |





