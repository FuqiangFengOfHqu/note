数据库索引其实就是为了使查询数据效率快。
 
聚集索引（主键索引）：在数据库里面，所有行数都会按照主键索引进行排序。
非聚集索引：就是给普通字段加上索引。
联合索引：就是好几个字段组成的索引，称为联合索引。
联合索引遵从最左前缀原则
A:select * from student where age = 16 and name = '小张'
B:select * from student where name = '小张' and sex = '男'
C:select * from student where name = '小张' and sex = '男' and age = 18
D:select * from student where age > 20 and name = '小张'
E:select * from student where age != 15 and name = '小张'
F:select * from student where age = 15 and name != '小张'
 
 A遵从最左匹配原则，age是在最左边，所以A走索引；
 B直接从name开始，没有遵从最左匹配原则，所以不走索引；
 C虽然从name开始，但是有索引最左边的age，mysql内部会自动转成where age = '18' and name = '小张'  and sex = '男' 这种，所以还是遵从最左匹配原则；
 D这个是因为age>20是范围，范围字段会结束索引对范围后面索引字段的使用，所以只有走了age这个索引；
 E这个虽然遵循最左匹配原则，但是不走索引，因为!= 不走索引；
 F这个只走age索引，不走name索引，原因如上；
 
三、有哪些列子不走索引呢？
 表student中两个字段age,name加了索引
 
key 'idx_age' ('age'),
key 'idx_name' ('name')
 1.Like这种就是%在前面的不走索引，在后面的走索引
 

A:select * from student where 'name' like '王%'
B:select * from student where 'name' like '%小'
 A走索引，B不走索引
 2.用索引列进行计算的，不走索引
 

A:select * from student where age = 10+8
B:select * from student where age + 8 = 18
 
 A走索引，B不走索引
 3.对索引列用函数了，不走索引
 
A:select * from student where  concat('name','哈') ='王哈哈';
B:select * from student where name = concat('王哈','哈');
 
 A不走索引，B走索引
 4. 索引列用了!= 不走索引,如下：
 
1
select * from student where age != 18
 
1.聚集索引在磁盘中的存储
 聚集索引叶子结点存储是表里面的所有行数据；
 每个数据页在不同的磁盘上面；
如果要查找id=5的数据，那么先把磁盘0读入内存，然后用二分法查找id=5的数在3和6之间，然后通过指针p1查找到磁盘2的地址，然后将磁盘2读入内存中，用二分查找方式查找到id=5的数据。
2.非聚集索引在磁盘中的存储

叶子结点存储的是聚集索引键，而不存储表里面所有的行数据，所以在查找的时候，只能查找到聚集索引键，再通过聚集索引去表里面查找到数据。
 
 
设计原则：
1.不是越多越好，索引数量多占磁盘空间，影响更新SQL的性能，因为更新时，索引也会修改。
2.避免对经常更新的表进行过多的索引，索引中的列要尽可能少。对经常用于查询的字段应该创建索引，但要避免添加不必要的字段。
3.数据量小的表最好不要使用索引，数据量小，查询时间可能比遍历索引的时间还短。
4.列不同值较多的列上建立索引，不同值很少的列上不要建立索引。比如性别字段：只有 男、女两个不同的值，无须建立。
5.当唯一性时某种数据本身的特征时，指定唯一索引。使用唯一索引需能确保定义的列的数据完整性，已提高查询速度。
6.在频繁排序或分组的列上建立索引，如果待排序的列有多个，可以在这些列上建立组合索引。
 
数据库索引其实就是为了使查询数据效率快。
聚集索引（主键索引）：在数据库里面，所有行数都会按照主键索引进行排序。

非聚集索引：就是给普通字段加上索引。

联合索引：就是好几个字段组成的索引，称为联合索引。

联合索引遵从最左前缀原则

A:select * from student where age = 16 and name = '小张'

B:select * from student where name = '小张' and sex = '男'

C:select * from student where name = '小张' and sex = '男' and age = 18

D:select * from student where age > 20 and name = '小张'

E:select * from student where age != 15 and name = '小张'

F:select * from student where age = 15 and name != '小张'

A 遵从最左匹配原则，age 是在最左边，所以 A 走索引；

B 直接从 name 开始，没有遵从最左匹配原则，所以不走索引；

C 虽然从 name 开始，但是有索引最左边的 age，mysql 内部会自动转成 where age = '18' and name = '小张' and sex = '男' 这种，所以还是遵从最左匹配原则；

D 这个是因为 age>20 是范围，范围字段会结束索引对范围后面索引字段的使用，所以只有走了 age 这个索引；

E 这个虽然遵循最左匹配原则，但是不走索引，因为!= 不走索引；

F 这个只走 age 索引，不走 name 索引，原因如上；

三、有哪些列子不走索引呢？

表 student 中两个字段 age,name 加了索引



key 'idx_age' ('age'),

key 'idx_name' ('name')

1.Like 这种就是%在前面的不走索引，在后面的走索引



A:select * from student where 'name' like '王%'

B:select * from student where 'name' like '%小'

A 走索引，B 不走索引

2.用索引列进行计算的，不走索引



A:select * from student where age = 10+8

B:select * from student where age + 8 = 18

A 走索引，B 不走索引

3.对索引列用函数了，不走索引



A:select * from student where concat('name','哈') ='王哈哈';

B:select * from student where name = concat('王哈','哈');

A 不走索引，B 走索引

4. 索引列用了!= 不走索引,如下：



select * from student where age != 18

1.聚集索引在磁盘中的存储

)

聚集索引叶子结点存储是表里面的所有行数据；

每个数据页在不同的磁盘上面；

如果要查找 id=5 的数据，那么先把磁盘 0 读入内存，然后用二分法查找 id=5 的数在 3 和 6 之间，然后通过指针 p1 查找到磁盘 2 的地址，然后将磁盘 2 读入内存中，用二分查找方式查找到 id=5 的数据。

2.非聚集索引在磁盘中的存储



叶子结点存储的是聚集索引键，而不存储表里面所有的行数据，所以在查找的时候，只能查找到聚集索引键，再通过聚集索引去表里面查找到数据。

设计原则：

1.不是越多越好，索引数量多占磁盘空间，影响更新 SQL 的性能，因为更新时，索引也会修改。

2.避免对经常更新的表进行过多的索引，索引中的列要尽可能少。对经常用于查询的字段应该创建索引，但要避免添加不必要的字段。

3.数据量小的表最好不要使用索引，数据量小，查询时间可能比遍历索引的时间还短。

4.列不同值较多的列上建立索引，不同值很少的列上不要建立索引。比如性别字段：只有 男、女两个不同的值，无须建立。

5.当唯一性时某种数据本身的特征时，指定唯一索引。使用唯一索引需能确保定义的列的数据完整性，已提高查询速度。

6.在频繁排序或分组的列上建立索引，如果待排序的列有多个，可以在这些列上建立组合索引。
