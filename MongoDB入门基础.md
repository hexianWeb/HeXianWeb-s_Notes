

# MongoDB入门基础

## MongoDB安装

### 第 1 步 — 安装 MongoDB

首先，通过运行以下命令导入最新稳定版 MongoDB 的公共 GPG 密钥。如果您打算使用 MongoDB 以外的版本4.4，请务必更改`4.4`此命令的 URL 部分以与您要安装的版本保持一致：

```bash
curl -fsSL https://www.mongodb.org/static/pgp/server-4.4.asc | sudo apt-key add -
```

cURL 是用于传输数据的许多操作系统上可用的命令行工具。它读取存储在传递给它的 URL 中的任何数据，并将内容打印到系统的输出。在以下示例中，cURL 打印 GPG 密钥文件的内容，然后将其通过管道传输到以下`sudo apt-key add -`命令中，从而将 GPG 密钥添加到您的受信任密钥列表中。

另外，请注意，此`curl`命令使用的选项`-fsSL`一起基本上告诉 cURL 以静默方式失败。这意味着如果由于某种原因 cURL 无法联系 GPG 服务器或 GPG 服务器已关闭，它不会意外地将生成的错误代码添加到您的受信任密钥列表中。

如果密钥添加成功，此命令将返回：

```
OK
```

如果您想仔细检查是否正确添加了密钥，可以使用以下命令执行此操作：

```bash
apt-key list
```



这将在输出中的某处返回 MongoDB 键：

```
Output/etc/apt/trusted.gpg
--------------------
pub   rsa4096 2019-05-28 [SC] [expires: 2024-05-26]
      2069 1EEC 3521 6C63 CAF6  6CE1 6564 08E3 90CF B1F5
uid           [ unknown] MongoDB 4.4 Release Signing Key <packaging@mongodb.com>
. . .
```

此时，您的 APT 安装仍然不知道在哪里可以找到`mongodb-org`安装最新版本 MongoDB 所需的包。

在您的服务器上，APT 会在两个地方查找要下载和安装的软件包的在线资源：`sources.list`文件和`sources.list.d`目录。 `sources.list`是一个列出 APT 数据的活动源的文件，每行一个源，最首选的源首先列出。该`sources.list.d`目录允许您将此类`sources.list`条目添加为单独的文件。

运行以下命令，这将在`sources.list.d`名为`mongodb-org-4.4.list`. 此文件中的唯一内容是单行内容`deb [ arch=amd64,arm64 ] https://repo.mongodb.org/apt/ubuntu focal/mongodb-org/4.4 multiverse`：

```bash
echo "deb [ arch=amd64,arm64 ] https://repo.mongodb.org/apt/ubuntu focal/mongodb-org/4.4 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-4.4.list
```

这一行告诉 APT 它需要知道的关于源是什么以及在哪里找到它的所有信息：

- `deb`：这意味着源条目引用了常规的 Debian 架构。在其他情况下，该行的这一部分可能`deb-src`是 ，这意味着源条目代表 Debian 发行版的源代码。
- `[ arch=amd64,arm64 ]`：这指定应将 APT 数据下载到哪些架构。在这种情况下，它指定了`amd64`和`arm64`架构。
- `https://repo.mongodb.org/apt/ubuntu`：这是一个 URI，表示可以找到 APT 数据的位置。在这种情况下，URI 指向官方 MongoDB 存储库所在的 HTTPS 地址。
- `focal/mongodb-org/4.4`: Ubuntu 存储库可以包含几个不同的版本。这指定您只需要可用于 Ubuntu 发行版`4.4`的`mongodb-org`软件包`focal`版本（“Focal Fossa”是 Ubuntu 20.04 的代号）。
- `multiverse`：这部分将 APT 指向四个主要的 Ubuntu 存储库之一。在这种情况下，它指向[`multiverse`存储库](https://help.ubuntu.com/community/Repositories#Multiverse)。

运行此命令后，更新服务器的本地包索引，以便 APT 知道在哪里可以找到`mongodb-org`包：

```bash
sudo apt update
```



之后，您可以安装 MongoDB：

```bash
sudo apt install mongodb-org
```

出现提示时，按`Y`和`ENTER`确认您要安装该软件包。

命令完成后，MongoDB 将安装在您的系统上。但是它还没有准备好使用。接下来，您将启动 MongoDB 并确认它工作正常。

### 第 2 步 — 启动 MongoDB 服务并测试数据库

上一步中描述的安装过程会自动将 MongoDB 配置为由 控制的守护程序运行，这意味着您可以使用各种命令`systemd`来管理 MongoDB 。`systemctl`但是，此安装过程不会自动启动该服务。

运行以下`systemctl`命令启动 MongoDB 服务：

```bash
sudo systemctl start mongod.service
```



然后检查服务的状态。请注意，此命令不包含`.service`在服务文件定义中。 `systemctl`如果它不存在，则会将此后缀附加到您自动传递的任何参数中，因此没有必要包含它：

```bash
sudo systemctl status mongod
```



此命令将返回如下输出，表明服务已启动并正在运行：

```
Output● mongod.service - MongoDB Database Server
     Loaded: loaded (/lib/systemd/system/mongod.service; disabled; vendor preset: enabled)
     Active: active (running) since Tue 2020-06-09 12:57:06 UTC; 2s ago
       Docs: https://docs.mongodb.org/manual
   Main PID: 37128 (mongod)
     Memory: 64.8M
     CGroup: /system.slice/mongod.service
             └─37128 /usr/bin/mongod --config /etc/mongod.conf
```

确认服务按预期运行后，使 MongoDB 服务在启动时启动：

```bash
sudo systemctl enable mongod
```



您可以通过连接到数据库服务器并执行诊断命令来进一步验证数据库是否正常运行。以下命令将连接到数据库并输出其当前版本、服务器地址和端口。它还将返回 MongoDB 内部`connectionStatus`命令的结果：

```bash
mongo --eval 'db.runCommand({ connectionStatus: 1 })'
```



`connectionStatus`将检查并返回数据库连接的状态。`1`响应中字段的值`ok`表示服务器正在按预期工作：

```
OutputMongoDB shell version v4.4.0
connecting to: mongodb://127.0.0.1:27017/?compressors=disabled&gssapiServiceName=mongodb
Implicit session: session { "id" : UUID("1dc7d67a-0af5-4394-b9c4-8a6db3ff7e64") }
MongoDB server version: 4.4.0
{
	"authInfo" : {
		"authenticatedUsers" : [ ],
		"authenticatedUserRoles" : [ ]
	},
	"ok" : 1
}
```

另外，请注意数据库在端口`27017`on 上运行`127.0.0.1`，本地环回地址代表**localhost**。这是 MongoDB 的默认端口号。

接下来，我们将了解如何使用`systemd`.

### 第 3 步 — 管理 MongoDB 服务

如前所述，步骤 1 中描述的安装过程将 MongoDB 配置为作为`systemd`服务运行。这意味着您可以`systemctl`像使用其他 Ubuntu 系统服务一样使用标准命令来管理它。

如前所述，该`systemctl status`命令检查 MongoDB 服务的状态：

```bash
sudo systemctl status mongod
```



您可以通过键入以下内容随时停止服务：

```bash
sudo systemctl stop mongod
```



要在服务停止时启动服务，请运行：

```bash
sudo systemctl start mongod
```



您还可以在服务器已经运行时重新启动它：

```bash
sudo systemctl restart mongod
```



在第 2 步中，您启用了 MongoDB 以随服务器自动启动。如果您希望禁用此自动启动，请键入：

```bash
sudo systemctl disable mongod
```



然后要重新启用它以在启动时启动，请`enable`再次运行命令：

```bash
sudo systemctl enable mongod
```



有关如何管理`systemd`服务的更多信息，请查看[Systemd Essentials：使用服务、单元和日志](https://www.digitalocean.com/community/tutorials/systemd-essentials-working-with-services-units-and-the-journal)。

### 第4步 — 创建一个管理用户

自版本发布以来3.0，MongoDB 守护进程被配置为仅接受来自本地 Unix 套接字的连接，并且不会自动向更广泛的 Internet 开放。但是，默认情况下仍禁用身份验证。这意味着任何可以访问安装了 MongoDB 的服务器的用户也可以完全访问数据库。

```bash
> db.createUser(
... {
... user: "AdminSammy",
... pwd: passwordPrompt(),
... roles: [ { role: "userAdminAnyDatabase", db: "admin" }, "readWriteAnyDatabase" ]
... }
... )
```

### 第5步 __ 启用身份验证

> 要启用身份验证，您必须编辑`mongod.conf`MongoDB 的配置文件。启用它并重新启动 Mongo 服务后，用户仍然可以在不进行身份验证的情况下连接到数据库。但是，在他们提供正确的用户名和密码之前，他们将无法读取或修改任何数据。

```
vim /etc/mongod.conf
```



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

LINUX环境下，可以通过查看端口27017的状态。

```
netstat -nalp | grep mongod
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



## MongoDB For Linux



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



具体操作方法：

LINUX环境下，可以通过查看端口27017的状态。

```
netstat -lanp | grep "27017"
```

![linux如何查看mongodb是否启动](https://cache.yisu.com/upload/ask/2021-06-30/ask_image_60dc41d1ea0be9105.png)

可以看到已经启动了mongod服务。

然后关闭mongod服务

```
sudo service mongod stop
```

![linux如何查看mongodb是否启动](https://cache.yisu.com/upload/ask/2021-06-30/ask_image_60dc41db5b6619140.png)

可以看到端口状态发生了变化

再开启服务

```
sudo service mongod start
```

![linux如何查看mongodb是否启动](https://cache.yisu.com/upload/ask/2021-06-30/ask_image_60dc41e230d5e3520.png)

端口又变成了listen状态。