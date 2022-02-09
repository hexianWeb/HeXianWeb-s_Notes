

# MongoDB入门基础

## MongoDB与正常RDBMS的对应

| RDBMS  | MongoDB                           |
| :----- | :-------------------------------- |
| 数据库 | 数据库                            |
| 表格   | 集合                              |
| 行     | 文档                              |
| 列     | 字段                              |
| 表联合 | 嵌入文档                          |
| 主键   | 主键 (MongoDB 提供了 key 为 _id ) |

> **mongodb://localhost用来连接mongo**

```
sudo /usr/local/mongodb/bin/mongod --dbpath /usr/local/mongodb/data --logpath /usr/local/mongodb/logs/mongodb.log --logappend --port=27017 --fork
```



## 基础语法



### 数据库操作



#### 创建数据库

> 1. 指令可以在数据库不存在的情况下创建数据库
> 2. 在数据库拥有的情况下使用该数据库
> 3. 数据库只有在内含有有效数据时，才会真实的被创建

```
use DATABASE_NAME
```



#### 删除数据库

> db.dropDatabase()

*你需要进入到相应的数据库中，执行该指令。才能成功的删除这个数据库*

### 集合操作

#### 新增集合

> db.集合名.insert(JSON数据)

**集合存在则插入数据，集合不存在则隐式创建**

练习：在test数据库的c1集合中插入数据

```
use test 
db.c1.insert({JSON-Data})
//你可以通过db.集合名.find() 来查看数据
{ "_id" : ObjectId("6200d9e7d5e96972020ad1df"), "name" : "hexian", "age" : 18 }
```

**插入多条集合**

> mongo中支持加入JS语言

```bash
> for(var i=1; i<=10 ;i++){
... db.c2.insert({"uanme":"hexian"+i,"age":"18"+i})}
WriteResult({ "nInserted" : 1 })
> db.c2.find()
{ "_id" : ObjectId("6200dd9ae81d01938d36e515"), "uanme" : "hexian1", "age" : "181" }
{ "_id" : ObjectId("6200dd9ae81d01938d36e516"), "uanme" : "hexian2", "age" : "182" }
{ "_id" : ObjectId("6200dd9ae81d01938d36e517"), "uanme" : "hexian3", "age" : "183" }
{ "_id" : ObjectId("6200dd9ae81d01938d36e518"), "uanme" : "hexian4", "age" : "184" }
{ "_id" : ObjectId("6200dd9ae81d01938d36e519"), "uanme" : "hexian5", "age" : "185" }
{ "_id" : ObjectId("6200dd9ae81d01938d36e51a"), "uanme" : "hexian6", "age" : "186" }
{ "_id" : ObjectId("6200dd9ae81d01938d36e51b"), "uanme" : "hexian7", "age" : "187" }
{ "_id" : ObjectId("6200dd9ae81d01938d36e51c"), "uanme" : "hexian8", "age" : "188" }
{ "_id" : ObjectId("6200dd9ae81d01938d36e51d"), "uanme" : "hexian9", "age" : "189" }
{ "_id" : ObjectId("6200dd9ae81d01938d36e51e"), "uanme" : "hexian10", "age" : "1810" }

```



#### 查询集合

> db.集合名.find()

*可以在语法后加入pretty()*美化查询结果的显示方式



**这里是一些查询范例集合**

| 操作       | 格式                     | 范例                                        | RDBMS中的类似语句       |
| :--------- | :----------------------- | :------------------------------------------ | :---------------------- |
| 等于       | `{<key>:<value>`}        | `db.col.find({"by":"菜鸟教程"}).pretty()`   | `where by = '菜鸟教程'` |
| 小于       | `{<key>:{$lt:<value>}}`  | `db.col.find({"likes":{$lt:50}}).pretty()`  | `where likes < 50`      |
| 小于或等于 | `{<key>:{$lte:<value>}}` | `db.col.find({"likes":{$lte:50}}).pretty()` | `where likes <= 50`     |
| 大于       | `{<key>:{$gt:<value>}}`  | `db.col.find({"likes":{$gt:50}}).pretty()`  | `where likes > 50`      |
| 大于或等于 | `{<key>:{$gte:<value>}}` | `db.col.find({"likes":{$gte:50}}).pretty()` | `where likes >= 50`     |
| 不等于     | `{<key>:{$ne:<value>}}`  | `db.col.find({"likes":{$ne:50}}).pretty()`  | `where likes != 50`     |



**AND条件**

>  db.集合名.find({<key>:<value>,<key>:<value>}).pretty()

**OR条件**

> db.集合名.find({$or:[{<key>:<value>,<key>,<value>}]}).pretty()

**显示特定的列**

> db.col.find({},{列名:0/1}) 
>
> //跟在查询条件后面，0除此以外，1只显示此

例

```bash
> db.c1.find({},{name:1})
{ "_id" : ObjectId("6200d9e7d5e96972020ad1df"), "name" : "hexian" }
```





#### 更新文档

> 主要有update() 和 save（）方法

**update方法**

```
db.collection.update(
   <query>,
   <update>,
   {
     upsert: <boolean>,
     multi: <boolean>,
     writeConcern: <document>
   }
```

**save方法**

```
db.collection.save(
     <document>,
   {
     writeConcern: <document>
   }
)
```

#### 修改器部分（属于更新文档内容)

**但是如果直接更新，会覆盖掉原先的数据，所以应该用修改器来修改数据**

> **$set**进行修改，可以防止原先的数据被覆盖掉

例：

```bash
> db.c1.update({name:"hexian"),{$set:{name:"hexianWEB"}})
uncaught exception: SyntaxError: missing } after property list :
@(shell):1:27
> db.c1.update({name:"hexian"},{$set:{name:"hexianWEB"}})
WriteResult({ "nMatched" : 1, "nUpserted" : 0, "nModified" : 1 })
> db.c1.find()
{ "_id" : ObjectId("6200d9e7d5e96972020ad1df"), "name" : "hexianWEB", "age" : 18 }

```

**有些时候，我们需要在未知数据上进行递增或递减操作**

> 这时需要利用**$inc**进行操作

```bash
> db.c1.find()
{ "_id" : ObjectId("6200d9e7d5e96972020ad1df"), "name" : "hexianWEB", "age" : 18 }
> db.c1.update({name:"hexianWEB"},{$inc:{age:2}}) //递减-2
WriteResult({ "nMatched" : 1, "nUpserted" : 0, "nModified" : 1 })
> db.c1.find()
{ "_id" : ObjectId("6200d9e7d5e96972020ad1df"), "name" : "hexianWEB", "age" : 20 }
```



**有些时候，我需要修改字段**



> 利用$rename 将name 改字段为uname

```bash
> db.c1.update({name:"hexianWEB"},{$rename:{name:"user_name"}})
WriteResult({ "nMatched" : 1, "nUpserted" : 0, "nModified" : 1 })
> db.c1.find()
{ "_id" : ObjectId("6200d9e7d5e96972020ad1df"), "age" : 20, "user_name" : "hexianWEB" } 
```



**有些时候，删除字段**

> $unset:{<key>:<value>}

```bash
> db.col.find()
{ "_id" : ObjectId("6200e6d4c68931d18f13b0e4"), "title" : "MongoDB", "description" : "MongoDB 是一个 Nosql 数据库", "by" : "菜鸟教程", "url" : "http://www.runoob.com", "tags" : [ "mongodb", "database", "NoSQL" ], "likes" : 100 }
> db.col.update({"by":"菜鸟教程"},{$unset:{url:true}})
WriteResult({ "nMatched" : 1, "nUpserted" : 0, "nModified" : 1 })
> db.col.find()
{ "_id" : ObjectId("6200e6d4c68931d18f13b0e4"), "title" : "MongoDB", "description" : "MongoDB 是一个 Nosql 数据库", "by" : "菜鸟教程", "tags" : [ "mongodb", "database", "NoSQL" ], "likes" : 100 } // url字段被删除了

```



#### 创建集合

```js
db.createCollection(name,options)
```

### 项目实战



#### 排序和分页功能

排序功能 通过**sort({JSON:1/-1})**来判断升序还是降序，其中-1为降序，1为升序

```bash
{
        "_id" : ObjectId("62010abec68931d18f13b0e8"),
        "id" : 3,
        "no" : "HX3",
        "uname" : "何贤3号",
        "tel" : "18379991019",
        "sex" : "男",
        "school" : "ECJTU",
        "remark" : "穷人"
}
{
        "_id" : ObjectId("62010abec68931d18f13b0e7"),
        "id" : 2,
        "no" : "HX2",
        "uname" : "何贤2号",
        "tel" : "18379991019",
        "sex" : "男",
        "school" : "ECJTU",
        "remark" : "穷人"
}
{
        "_id" : ObjectId("62010abec68931d18f13b0e6"),
        "id" : 1,
        "no" : "HX1",
        "uname" : "何贤1号",
        "tel" : "18379991019",
        "sex" : "男",
        "school" : "ECJTU",
        "remark" : "穷人"
}
> db.stu.find().sort({id:-1}).pretty()//

```



**Limit与Skip方法**

- 语法：db.find().sort().skip(num).limit(num)
- 说明: skip跳过指定数量(可选).limit限制查询数量

#### 聚合查询

> 通过数据聚集 统计 来实现更好的数据展示

**基础语法**：

```
db.集合名称.aggregate([
	{管道:{表达式}}
	...
])
```

常用管道

```
$group	将集合中的文档分组，用于统计结果
$match	过滤数据，只要输出符合条件的文档
$sort	聚合数据进一步排序
$skip	跳过指定文档数
$limit	限制集合数据返回文档数
.....
```

准备

```
use test4
db.c1.insert({_id:1,name:"a",sex:1,age:1})
db.c1.insert({_id:2,name:"a",sex:1,age:2})
db.c1.insert({_id:3,name:"b",sex:2,age:3})
db.c1.insert({_id:4,name:"c",sex:2,age:4})
db.c1.insert({_id:5,name:"d",sex:2,age:5})
```

练习

- 统计男生、女生的总年龄

```
db.c1.aggregate([
	{
		$group:{//进行分组
			_id:"$sex",//按性别分
			rs:{$sum:"$age"}//年龄汇总 
		}
	}
])

```

- 结果

```
{ "_id" : 1, "rs" : 3 }
{ "_id" : 2, "rs" : 12 }
```



- 统计男生、女生的总人数

  ```bash
      db.c1.aggregate([
          {
              $group:{
                      _id:"$sex",
                      rs:{$sum:1}
                      }
          }
      ])
  ```

  

- 结果展示

  ```
  { "_id" : 1, "rs" : 2 }
  { "_id" : 2, "rs" : 3 }
  ```

  

  

  - 求学生总数和平均年龄

  ```
  db.c1.aggregate([
      {
      	$group:{
              _id:null,
              total_num:{$sum:1},
              total_avg:{$avg:"$age"}
      	}
      }
  ])
  ```

- 结果展示

  ```bash
  { "_id" : null, "total_num" : 5, "total_avg" : 3 }
  ```

  #### 

#### 索引

**语法**

> db.集合名称.createIndex(待创建索引的列[,额外选项])

**参数**：

- 待创建索引的列：{键:1,...键：-1}
  - tips: 1升序 -1 降序
- 额外选项：设置索引名称或者唯一索引等
- 删除索引的语法
  - 全部删除：db.集合名称.dropIndexes()
  - 删除指定:   db.集合名称.dropIndex(索引名)
- 查看索引语法:db.集合名.getIndexes()



```bash
> db.c1.createIndex({name:1},{name:"hexian"}) //建立普通索引 并赋予一个别名
{
        "numIndexesBefore" : 1,
        "numIndexesAfter" : 2,
        "createdCollectionAutomatically" : false,
        "ok" : 1
}
> db.c1.getIndexes()
[
        {
                "v" : 2,
                "key" : {
                        "_id" : 1
                },
                "name" : "_id_"
        },
        {
                "v" : 2,
                "key" : {
                        "name" : 1   //普通索引规则
                },
                "name" : "hexian"     // 名称 若要操作这个索引 则需要引用此名称
        }
]

```



**创建唯一索引**

> 即目标内容不可重复，唯一
>
> 语法：db.集合名.createIndex(待添加的列,{unique:列名})

在age 1~5都存在的情况下 设置age为主键 随后插入age的报错情况

```
> db.c1.insert({age:5})
WriteResult({
        "nInserted" : 0,
        "writeError" : {
                "code" : 11000,
                "errmsg" : "E11000 duplicate key error collection: test4.c1 index: age dup key: { age: 5.0 }"
        }
})

```

**分析索引**

> explain 解析查询当前搜索的数据所需时间以及经过的文档数

```

db.集合名.find({<key>:<value>}).explain("executionStats")

db.c1.find({age:18}).explain("executionStats")

```



返回结果中的

​	*executionTimeMillis needTime需要注意一下*



#### 选择规则

```

- 为常做条件、排序、分组的字段建立索引
- 选择唯一性索引 （同值较少比如性别字段）
- 选择较小的数据列，为较长的字符串使用前缀索引 （索引文件更小）
```



### 权限设置

**语法**

创建账号

```
db.createUser({
	"user":"账号",
	"pwd":"密码",
	"roles":[{
		role:"角色",
		db:"所属数据库"
	}]
})
```

角色

```
#角色种类
超级用户角色:root
数据库用户角色：read,readWrite
数据库管理角色:dbAdmin,userAdmin
集群管理角色:clusterAdmin、clusterManager、clusterMontitor、hostManager;
备份恢复角色:backup、restore;
所有数据库角色:readAnyDatabase、readWriteAnyDatabase、userAdminAnyDatabase、dbAdminAnyDatabase

#角色说明
root：只在admin数据库中可用。超级账号，超级权限
read：允许用户读取指定数据库
readWrite：允许用户读写指定数据库
dbAdmin：允许用户在指定数据库中执行管理函数，如索引创建、删除、查看统计或访问system.profile
```

### 开启验证模式

#### 概念

指用户需要输入账号密码才能登录使用

#### 操作步骤

```
1.添加超级管理员
2.退出卸载服务
3.重新安装需要输入账号密码的服务(注在原安装命令基础上加上--auth即可)
4.启动服务 -> 登录测试
```

创建账号

```
db.createUser({
	"user":"账号",
	"pwd":"密码",
	"roles":[{
		role:"角色",
		db:"所属数据库"
	}]
})
```



步骤1：添加超级管理员

> mongo
>
> 

```
use admin 
db.createUser({
	"user":"admin",
	"pwd":"admin888",
	"roles":[{
	"role":"root",
	"db":"admin"
	}]
})
```

**验证查询成功**

```
> show dbs
admin   0.000GB
config  0.000GB
local   0.000GB
school  0.000GB
test    0.000GB
test4   0.000GB
test5   0.005GB
> use admin
switched to db admin
> show collections
system.users
system.version
> db.system.users.find().pretty()
{
        "_id" : "admin.admin",
        "userId" : UUID("64554eb6-5701-4acd-b55d-0ddc3898d23d"),
        "user" : "admin",
        "db" : "admin",
        "credentials" : {
                "SCRAM-SHA-1" : {
                        "iterationCount" : 10000,
                        "salt" : "NvZJHUA/QUJ/zYG/hje6mA==",
                        "storedKey" : "td+KDzyFG8FyEWBJg4HXIswB9fU=",
                        "serverKey" : "js3ed6aDbZyqm3bXRs/Kb5wYC/I="
                },
                "SCRAM-SHA-256" : {
                        "iterationCount" : 15000,
                        "salt" : "lDT/i6E+yVf8VK2t7lWoOL4K8xjCEZaQfbTOoA==",
                        "storedKey" : "dRaCApI5T2ByMPslk7ui4mgCsC50ZTH7Tmaxhr3AJRc=",
                        "serverKey" : "8wuh6zL/mjzq8pOqQX7+0+pvLSvSWqlJ9rMdU+BMGX8="
                }
        },
        "roles" : [
                {
                        "role" : "root",
                        "db" : "admin"
                }
        ]
}

```





### 备份数据库mongodump

- 语法

  ```
  导出数据语法:mongodump -h -port -u -p -d -o
  导出语法说明
   -h		host 		服务器IP地址(一般不写 默认本机)
   -port		 		端口(一般不写 默认27017)
   -u		user 		账号
   -p 	pwd	 		密码
   -d		database	数据库(注意：数据库不写则导出全局)
   -o		open		备份到指定目录下
  ```

- 练习(备份所有数据):mongodump -u admin -p admin888 -o C:\MongoDB\bak
- 练习(备份指定数据):mongodump -u shop1 -p admin888 -d shop -o C:\MongoDB\bak2

[![image-20210617190123592](https://blog-theta-ten.vercel.app/img/MongoDB%E5%A4%87%E4%BB%BD%E5%92%8C%E8%BF%98%E5%8E%9F/image-20210617190123592.png)](https://blog-theta-ten.vercel.app/img/MongoDB备份和还原/image-20210617190123592.png)image-20210617190123592

### 还原数据库mongostore

- 语法：

  ```
  还原数据语法:mongorestore -h -port -u -p -d --drop 备份数据目录
  还原数据说明:
  -h
  -port
  -u
  -p
  -d		不写则还原全部数据库
  --drop	先删除数据库再导入
  ```

- 练习

  - 还原所有数据：mongorestore -u admin -p admin888 –drop C:\MongoDB\bak

    [![image-20210617190610124](https://blog-theta-ten.vercel.app/img/MongoDB%E5%A4%87%E4%BB%BD%E5%92%8C%E8%BF%98%E5%8E%9F/image-20210617190610124.png)](https://blog-theta-ten.vercel.app/img/MongoDB备份和还原/image-20210617190610124.png)image-20210617190610124[![image-20210617190850232](https://blog-theta-ten.vercel.app/img/MongoDB%E5%A4%87%E4%BB%BD%E5%92%8C%E8%BF%98%E5%8E%9F/image-20210617190850232.png)](https://blog-theta-ten.vercel.app/img/MongoDB备份和还原/image-20210617190850232.png)image-20210617190850232

    验证：

    [![image-20210617191044518](https://blog-theta-ten.vercel.app/img/MongoDB%E5%A4%87%E4%BB%BD%E5%92%8C%E8%BF%98%E5%8E%9F/image-20210617191044518.png)](https://blog-theta-ten.vercel.app/img/MongoDB备份和还原/image-20210617191044518.png)image-20210617191044518

  - 还原指定数据: mongorestore -u shop2 -p admin888 -d shop –drop C:\MongoDB\bak2\shop

    [![image-20210617191320105](https://blog-theta-ten.vercel.app/img/MongoDB%E5%A4%87%E4%BB%BD%E5%92%8C%E8%BF%98%E5%8E%9F/image-20210617191320105.png)](https://blog-theta-ten.vercel.app/img/MongoDB备份和还原/image-20210617191320105.png)image-20210617191320105

    [![image-20210617191539304](https://blog-theta-ten.vercel.app/img/MongoDB%E5%A4%87%E4%BB%BD%E5%92%8C%E8%BF%98%E5%8E%9F/image-20210617191539304.png)](https://blog-theta-ten.vercel.app/img/MongoDB备份和还原/image-20210617191539304.png)image-20210617191539304

    [![image-20210617191646552](https://blog-theta-ten.vercel.app/img/MongoDB%E5%A4%87%E4%BB%BD%E5%92%8C%E8%BF%98%E5%8E%9F/image-20210617191646552.png)](https://blog-theta-ten.vercel.app/img/MongoDB备份和还原/image-20210617191646552.png)image-
    
    

## mongodb中间件（了解)

### 插入数据

> insertOne | insertMany

```js
// 原版mongodb 中间价使用
// 需要先创建一个MongoDB对象
const MongoClient = require("mongodb").MongoClient;

// 配置好指定的URL与端口号
const url = "mongodb://localhost:27017/";

MongoClient.connect(url, function (err, db) {
  if (err) {
    throw err;
  }
  console.log("数据库已经创建");
  //   创建集合 MongoDB会自动创建数据库和集合 通常不存在会默认创建而不是报错
  var dbo = db.db("test");
  var myobj = { name: "何贤", url: "www.hexian.com" };
  //   文档插入操作
  dbo.collection("site").insertOne(myobj, function (err, res) {
    if (err) {
      throw err;
    }
    console.log("文档插入成功");
    db.close();
  });
});
```

### 查询数据

```js
    dbo.collection("site").find(whereStr).toArray(function (err,result) { //whereStr是指查询数据
       if (err) {
          throw err; 
       }
        [something code]
       db.close();
```

### 

### 更新数据

```js
var MongoClient = require('mongodb').MongoClient;
var url = "mongodb://localhost:27017/";
 
MongoClient.connect(url, function(err, db) {
    if (err) throw err;
    var dbo = db.db("runoob");
    var whereStr = {"name":'菜鸟教程'};  // 查询条件
    var updateStr = {$set: { "url" : "https://www.runoob.com" }};
    dbo.collection("site").updateOne(whereStr, updateStr, function(err, res) {
        if (err) throw err;
        console.log("文档更新成功");
        db.close();
    });
});
```

**其余类似mongo原语句规则，举一反三，这里不一一举例**

### **Promise数据操作**


```js
const MongoClient = require("mongodb").MongoClient;

const url = "mongodb://localhost:27017/";

MongoClient.connect(url)
  .then((conn) => {
    console.log("数据库已经连接");
    const dbase = conn.db("test").collection("site");

    //   增加
    dbase
      .insertOne({ name: "xiange", site: "www.4399.com" })
      .then((res) => {
        //   查询
        return dbase
          .find()
          .toArray()
          .then((arr) => {
            console.log(arr);
          });
      })
      .then(() => {
        // 更新
        return dbase.updateOne(
          { name: "xiange" },
          {
            $set: { site: "xiange.com" },
          }
        );
      })
      .then((res) => {
        return dbase
          .find()
          .toArray()
          .then((arr) => {
            console.log(arr);
          });
      })
      .catch((err) => {
        console.log("数据操作失败" + err.message);
      }) + err.message;
  })
  .finally(() => {
    conn.close();
  })
  .catch((err) => {
    console.log("数据库连接失败");
  });


```

> 链式编程是不是很难阅读 还好我们还有另一种方案

### **异步函数**

*最优选择*

```js
const MongoClient = require("mongodb").MongoClient;

const url = "mongodb://localhost/";

async function dataOperate() {
  var conn = null;
  try {
    //   连接数据库
    conn = await MongoClient.connect(url);

    // 指定集合
    const test = conn.db("test").collection("site");

    // 增加文档
    await test.insertOne({ site: "whynot.com" });

    // 查询文档
    var arr = await test.find().toArray();
    console.log(arr);

    // 更改
    await test.updateMany(
      { name: "xiange" },
      {
        $set: { site: "mememe.com" },
      }
    );

    // 查询
    arr = await test.find().toArray();
    console.log(arr);
  } catch (err) {
    console.error(err);
  } finally {
    if (conn != null) {
      conn.close();
    }
  }
}
dataOperate();

```





## mongoose

### 快速入门

> npm install mongoose 安装中间件

```
// 引入mongoose中间件
var mongoose = require("mongoose");
```



> 连接数据库

```
// 连接本地数据库
mongoose.connect("mongodb://localhost/test");
```



>  connect()会返回一个待定的连接 这里加入成功与失败的警告

```

var db = mongoose.connection;

db.on("error", console.error.bind(console, "连接错误："));

db.once("open", () => {
	//someting code
})
```





```


// connect()会返回一个待定的连接 这里加入成功与失败的警告
var db = mongoose.connection;

db.on("error", console.error.bind(console, "连接错误："));

db.once("open", () => {


  // 连接成功
  console.log("连接成功");

  // 在Mongoose里 一切都始于Schema
  var cat_Schema = mongoose.Schema({
    name: String,
  });

  // 注意了 methods 是给document用的 而且需要在model前声明
  cat_Schema.methods.speak = function () {
    var greeting = this.name ? "我的名字是" + this.name : "我还没有名字";
    console.log(greeting);
  };
  //将schema编译成一个Model
  var cat = mongoose.model("cat", cat_Schema);

  // Model 使我们构造document的Class 每个document都是一置猫
  // 他的行为和属性都会声明在schema中
  var Chinese_LiHua = new cat({ name: "狸花猫" });
  //该document使用methods方法
  Chinese_LiHua.speak();

  //   最后 将这个document利用**save方法**存入数据库
  //   tips: the first options always is err
  Chinese_LiHua.save((err, Chinese_LiHua) => {
    if (err) {
      return console.error(err);
    }
    Chinese_LiHua.speak();
  });
});
```

### 

### Schema 规则范式

*在快速入门时，我遇到了类似expressJoi Schema设定*

例

```js
// 引入mongoose中间件
var mongoose = require("mongoose");

// 范例 规则定义
var blogSchema = mongoose.Schema({
  title: String,
  author: String,
  body: String,
  comments: [{ body: String, date: Date }],
  date: { type: Date, default: Date.now },
  hidden: Boolean,
  meta: {
    votes: Number,
    favs: Number,
  },
});
```



**这很重要**

document 里每个属性的类型都会被转换为 在 `blogSchema` 里定义对应的 [SchemaType](http://www.mongoosejs.net/docs/api.html#schematype_SchemaType)。 例如 `title` 属性会被转换为 SchemaType [String](http://www.mongoosejs.net/docs/api.html#schema-string-js)， 而 `date`属性会被转换为 SchemaType `Date`。 还可以像上面 `meta` 属性，更详细地指定嵌套在里面的属性类型。

#### 创建一个model

> 试着将schema转换为一个Model 

```
var Blog = mongoose.model('Blog',blogSchema)
```

#### 

#### 这里是document

*document是Models的实例

