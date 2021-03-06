## 数据库命令

| 命令 | 说明 |
| --- | --- |
| db | 显示当前数据库名称 |
| db.stats\(\) | 查看当前数据库信息 |
| show dbs | 显示所有数据库 |
| use `DBname` | 切换数据库，如果没有则创建 |
| db.dropDatabase\(\) | 删除当前数据库 |

## 集合操作\(表操作\)

| 命令 | 说明 |
| --- | --- |
| db.createCollection\(`Tname`, {capped: true, size: 10}\) | 创建集合, 并限制文档数量为10 |
| db.`Tname`.drop\(\). | 删除集合 |
| show collections | 查看当前数据库集合 |

## 数据类型

| 类型 | 说明 |
| --- | --- |
| Object ID | 文档ID |
| String | 字符串，必须是有效的UTF-8 |
| Boolean | 布尔值，true或false |
| Integer | 可以使32位或64位，取决于服务器 |
| Double | 浮点值 |
| Arrays | 数组或列表 |
| Object | 用于嵌入式的文档，即一个值为文档 |
| Null | 存储Null值 |
| Timestamp | 时间戳 |
| Date | 存储当期那时间或时间的UNIX时间格式 |

### object id

* 每个文档都有一个属性，为\_id，保证每个文档的唯一性
* 可以自己去设置\_id插入文档
* 如果没有提供，那么MongoDB为每个文档提供了一个独特的\_id，类型为objectID
* objectID是一个12字节的十六进制数
* 前4个字节为当前时间戳
* 接下来3个字节的机器ID
* 接下来的2个字节中MongoDB的服务进程id
* 最后3个字节是简单的增量值

## 文档操作

### 插入

db.`Tname`.insert\(`doc`\)

### 更新

sb.stu.update\(`query`, `update`,{multi:`boolean`}\)

* 更新一条数据
* db.stu.update\({name:'hr'},{name:'mnc'}\)
* 指定属性更新，通过操作符$set
* db.stu.update\({name:'hr'},{$set:{name:'hys'}}\)
* 修改多条匹配到的数据
* db.stu.update\({},{$set:{gender:0}},{multi:true}\)

### 保存

> 如果有\_id则修改数据，没有\_id则添加文档
* db.`Tname`.save\(`document`\)

### 删除

>语法： db.`Tname`.remove\(`query`, {justOne: `boolean`}\)
* db.`Tname`.remove\({}, {justOne: `boolean`}\)

### 查询

#### 基本查询

* find\(`query`\) \# 条件查询，不带`query`则全部查询出来
* findOne\(`query`\) \# 查询一条文档
* pretty\(\) \# 查询结果结构化显示

#### 条件操作符
语法： `$type`

type类型 | type值
--- | ---
双精度型|1
字符串|2
对象|3
数组|4
二进制数据|5
对象ID|7
布尔类型|8
数据|9
空|10
正则表达式|11
JS代码|13
符号|14
有作用域的JS代码|15
32位整型数|16
时间戳|17
64位整型数|18
Min key|255
Max key|127

范例，查找name是字符串的文档记录
```sql
db.shiyanlou.find({"name":{$type:2}})
```


#### 比较运算符

| 运算符 | 全称 | 功能 |
| --- | --- | --- |
| : | : | 默认是等于，没有运算符 |
| $lt | low than | 小于或等于 |
| $lte | low than equal | 小于或等于 |
| $gt | greate than | 大于 |
| $gte | greate than equal | 大于或等于 |
| $ne | not equal | 不等于 |

#### 逻辑运算符

| 运算符 | 功能 | 示例 |
| --- | --- | --- |
| - | 逻辑与 | db.stu.find\({age:{$gte:18},gender:1}\) |
| $or | 逻辑或 | db.stu.find\({$or:\[{age:{$gt:18}},{gender:1}\]}\) |

> 同时使用：
```sql
db.stu.find({$or:[{age:{$gte:18}},{gender:1}],name:'gj'})
```

#### 范围运算符

> 使用"$in"，"$nin" 判断是否在某个范围内
>
> * 例：查询年龄为18、28的学生
> * db.stu.find\({age:{$in:\[18,28\]}}\)

#### 正则表达式

> 使用//或$regex编写正则表达式
>
> * 例：查询姓黄的学生
> * db.stu.find\({name:/^黄/}\)
> * db.stu.find\({name:{$regex:'^黄'}}}\)

#### 自定义查询

> 使用$where后面写一个函数，返回满足条件的数据
>
> * 例：查询年龄大于30的学生
> * db.stu.find\({$where:function\(\){return this.age&gt;20}}\)

#### 结果集的函数
函数 | 说明 | 示例
---- | ---- | ----
limit(`num`) | 限制查询结果数量，`num`为数量 | db.stu.find().limit(2)
skip(`num`)  | 跳过指定数量的文档 |db.stu.find().skip(2)
sort({`fiels`: `1/-1`}) | 排序参数1为升序，-1为降序 |  db.stu.find().sort({gender:-1,age:1}
count(`conditions`)    | 统计个数，可以没有条件 | db.stu.count({age:{$gt:20},gender:1})
find({}, {`field`: `boolean`}) | 投影，字段为1显示，字段为0不显示 | db.stu.find({},{_id:0,name:1,gender:1})
distinct(`field`, `conditions`) | 对数据进行去重。 | db.stu.distinct(`field`,{name:1,gender:1})

