## 基本的增删查表

对数据集合的操作

### 切换和创建数据库

- use xxx

### 查看数据库

- show dbs

### 显示当前的数据集合

- show collections

### 删除集合，删除指定的集合 删除表

- db.user.drop()

### 删除库

- db.dropDatabase();

### 新增一条记录

- db.user.insert({"name":"zhangsan"，"age":20});

### 查询所有记录

- db.user.find();

### 查询去掉后的当前聚集集合中的某列的重复数据   会过滤掉 name 中的相同数据

- db.user.distinct("name");

### 查询 age = 22 的记录

- db.user.find({"age": 22});

### 查询 age > 22 的记录

- db.user.find({age: {$gt: 22}});

### 查询 age < 22 的记录

- db.user.find({age: {$lt: 22}});

### 查询 age >= 25 的记录

- db.user.find({age: {$gte: 25}});

### 查询 age <= 25 的记录

- db.user.find({age: {$lte: 25}});

### 查询 age >= 23 并且 age <= 26  注意书写格式

- db.user.find({age: {$gte: 23, $lte: 26}}); 

### 查询 name 中包含 mongo 的数据  模糊查询用于搜索

- db.user.find({name: /mongo/});

### 查询 name 中以 mongo 开头的 

- db.user.find({name: /^mongo/});

### 查询指定列 name、age 数据  

- db.user.find({}, {name: 1, age: 1});

当然 name 也可以用 true 或 false,当用 ture 的情况下河 name:1 效果一样，如果用 false 就

是排除 name，显示 name 以外的列信息。

### 查询指定列 name、age 数据, age > 25

- db.user.find({age: {$gt: 25}}, {name: 1, age: 1});

### 按照年龄排序 1 升序 -1 降序

- db.user.find().sort({age: 1});
- db.user.find().sort({age: -1});

### 查询 name = zhangsan, age = 22 的数据

- db.user.find({name: 'zhangsan', age: 22});

### 查询前 5 条数据

- db.user.find().limit(5);

### 查询在 5-10 之间的数据

- db.user.find().limit(10).skip(5);

可用于分页，limit 是 pageSize，skip 是(page-1)*pageSize

### or 与 查询

- db.user.find({$or: [{age: 22}, {age: 25}]});

### findOne 查询第一条数据

- db.user.findOne();

### 查询某个结果集的记录条数 统计数量

- db.user.find({age: {$gte: 25}}).count();

如果要返回限制之后的记录数量，要使用 count(true)或者 count(非 0)

- db.users.find().skip(10).limit(5).count(true);

### 查找名字叫做小明的，把年龄更改为 16 岁:

- db.student.update({"name":"小明"},{$set:{"age":16}});

### 查找数学成绩是 70，把年龄更改为 33 岁:

- db.student.update({"score.shuxue":70},{$set:{"age":33}});

### 更改所有男用户的的年纪

- db.student.update({"sex":"男"},{$set:{"age":33}},{multi: true});

### 完整替换，不出现$set关键字

- db.student.update({"name":"小明"},{"name":"大明","age":16});

### 操作加

- db.users.update({name: 'Lisi'}, {$inc: {age: 50}}, false, true); 

相当于:update users set age = age + 50 where name = ‘Lisi’;

- db.users.update({name: 'Lisi'}, {$inc: {age: 50}, $set: false, true);

相当于:update users set age = age + 50, name = ‘hoho’ where name = ‘Lisi’;

### 删除数据

- db.collectionsNames.remove( { "borough": "Manhattan" } )
-  db.users.remove({age: 132});

- db.restaurants.remove( { "borough": "Queens" }, { justOne: true } ) 默认删除一条



## 索引基础

索引是对数据库表中一列或多列的值进行排序的一种结构，可以让我们查询数据库变得 更快。

### 创建索引的命令

- db.user.ensureIndex({"userame":1})

### 获取当前集合的索引

- db.user.getIndexes()

### 删除索引的命令是

- db.user.dropIndex({"username":1})

### 在 MongoDB 中，我们同样可以创建复合索引 

如:数字 1 表示 username 键的索引按升序存储，-1 表示 age 键的索引按照降序方式存储。

- db.user.ensureIndex({"username":1, "age":-1})

该索引被创建后，基于 username 和 age 的查询将会用到该索引，或者是基于 username 的查询也会用到该索引，但是只是基于 age 的查询将不会用到该复合索引。因此可以说， 如果想用到复合索引，必须在查询条件中包含复合索引中的前 N 个索引列。然而如果查询 条件中的键值顺序和复合索引中的创建顺序不一致的话，MongoDB 可以智能的帮助我们调 整该顺序，以便使复合索引可以为查询所用。

### 下面的命令可以在创建索引时为其指定索引名

- db.user.ensureIndex({"username":1},{"name":"userindex"})

随着集合的增长，需要针对查询中大量的排序做索引。如果没有对索引的键调用 sort， MongoDB 需要将所有数据提取到内存并排序。因此在做无索引排序时，如果数据量过大以 致无法在内存中进行排序，此时 MongoDB 将会报错。

### 唯一索引

- db.user.ensureIndex({"userid":1},{"unique":true})



![img](https://cdn.nlark.com/yuque/0/2021/png/2479971/1624777268494-238af6ec-741b-4f81-8145-56859c6011c7.png)

### 使用 explain  explain 会返回查询使用的索引情况，耗时和扫描文档数的统计信息。

- db.tablename.find().explain( "executionStats" )

关注输出的如下数值:explain.executionStats.executionTimeMillis

## Mongodb 账户权限配置

### 第一步创建超级管理用户

```javascript
use admin 
db.createUser({
user:'admin',
pwd:'123456', roles:[{role:'root',db:'admin'}]
})
```

### 第二步修改 Mongodb 数据库配置文件

```javascript
# 路径: ~/MongoDB\Server\4.0\bin\mongod.cfg 配置:
security:
			authorization: enabled
```

### 第三步重启 mongodb 服务

...

### 第四步用超级管理员账户连接数据库

```javascript
mongo admin -u 用户名 -p 密码
mongo 192.168.1.200:27017/test -u user -p password
```

### 第五步给 eggcms 数据库创建一个用户 只能访问 eggcms 不能访问其他数据库

```javascript
use eggcms
db.createUser( {
user: "eggadmin",
pwd: "123456",
roles: [ { role: "dbOwner", db: "eggcms" } ]
} )
```

### Mongodb 账户权限配置中常用的命令

- show users;  #查看当前库下的用户
- db.dropUser("eggadmin") #删除用户

- db.updateUser( "admin",{pwd:"password"}); #修改用户密码
- db.auth("admin","password");  #密码认证

### Mongodb 数据库角色

1.数据库用户角色:read、readWrite; 

2.数据库管理角色:dbAdmin、dbOwner、userAdmin; 

3.集群管理角色:clusterAdmin、clusterManager、clusterMonitor、hostManager; 

4.备份恢复角色:backup、restore; 

5.所有数据库角色:readAnyDatabase、readWriteAnyDatabase、userAdminAnyDatabase、 dbAdminAnyDatabase

6.超级用户角色:root

### 连接数据库的时候需要配置账户密码

const url = 'mongodb://admin:123456@localhost:27017/';



## MongoDB 聚合管道

使用聚合管道可以对集合中的文档进行变换和组合。表关联查询、数据的统计。



![img](https://cdn.nlark.com/yuque/0/2021/png/2479971/1624783966849-d19c6a3a-2e9f-434f-8174-7d61db6547d3.png)

### MongoDB Aggregation 管道操作符与表达式

| 管道操作 | Description                                 |
| -------- | ------------------------------------------- |
| $project | 增加、删除、重命名字段                      |
| $match   | 条件匹配。只满足条件的文档才能进入下 一阶段 |
| $limit   | 限制结果的数量                              |
| $skip    | 跳过文档的数量                              |
| $sort    | 条件排序。                                  |
| $group   | 条件组合结果 统计                           |
| $lookup  | 用以引入其它集合的数 $lookup 据(表关联查询) |

### 管道表达式:

管道操作符作为“键”,所对应的“值”叫做管道表达式。

例如{$match:{status:"A"}}，$match 称为管道操作符，而 status:"A"称为管道表达式， 是管道操作符的操作数(Operand)。



| 常用表达式操作符 | Description            |
| ---------------- | ---------------------- |
| $addToSet        | 将文档指定字段的值去重 |
| $max             | 文档指定字段的最大值   |
| $min             | 文档指定字段的最小值   |
| $sum             | 文档指定字段求和       |
| $avg             | 文档指定字段求平均     |
| $gt              | 大于给定值             |
| $lt              | 小于给定值             |
| $eq              | 等于给定值             |

### 数据模拟

```javascript
db.order.insert({"order_id":"1","uid":10,"trade_no":"111","all_price":100,"all_num":2}) db.order.insert({"order_id":"2","uid":7,"trade_no":"222","all_price":90,"all_num":2}) db.order.insert({"order_id":"3","uid":9,"trade_no":"333","all_price":20,"all_num":6})
db.order_item.insert({"order_id":"1","title":"商品鼠标 1","price":50,num:1}) db.order_item.insert({"order_id":"1","title":"商品键盘 2","price":50,num:1}) db.order_item.insert({"order_id":"1","title":"商品键盘 3","price":0,num:1})
db.order_item.insert({"order_id":"2","title":"牛奶","price":50,num:1}) db.order_item.insert({"order_id":"2","title":"酸奶","price":40,num:1})
db.order_item.insert({"order_id":"3","title":"矿泉水","price":2,num:5}) db.order_item.insert({"order_id":"3","title":"毛巾","price":10,num:1})
```



- $project 

修改文档的结构，可以用来重命名、增加或删除文档中的字段。

要求查找 order 只返回文档中 trade_no 和 all_price 字段

```javascript
db.order.aggregate([ {
$project:{ trade_no:1, all_price:1 } }
])
```

- match

用于过滤文档。用法类似于 find() 方法中的参数。

```javascript
db.order.aggregate([ {
$project:{ trade_no:1, all_price:1 } },
{
$match:{"all_price":{$gte:90}}
} ])
```

- $group

将集合中的文档进行分组，可用于统计结果。

统计每个订单的订单数量，按照订单号分组

```javascript
db.order_item.aggregate( [
{
$group: {_id: "$order_id", total: {$sum: "$num"}}
} ]
)
```

- $sort

将集合中的文档进行排序。

```javascript
db.order.aggregate([ {
$project:{ trade_no:1, all_price:1 } },
{
$match:{"all_price":{$gte:90}}
}, {
$sort:{"all_price":-1} }
])
```

- $limit

```javascript
db.order.aggregate([ {
$project:{ trade_no:1, all_price:1 } },
{
$match:{"all_price":{$gte:90}}
}, {
$sort:{"all_price":-1} },
{
$limit:1
} ])
```

- $skip

```javascript
db.order.aggregate([ 
{$project:{ trade_no:1, all_price:1 } },
{$match:{"all_price":{$gte:90}}}, 
{$sort:{"all_price":-1} },
{$skip:1}])
```

- $lookup 表关联

```javascript
db.order.aggregate([ {
$lookup: {
from: "order_item",
localField: "order_id", foreignField: "order_id", as: "items"
} }
])
```