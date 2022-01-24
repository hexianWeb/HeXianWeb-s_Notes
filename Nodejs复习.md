# Nodejs基础复习（作者：何贤）

*nodemon可以帮助代码自动更新*

## Fs模块

### readFile方法获取文件

```js
// 1.导入 fs模块 来操作文件
const fs = require("fs");

// 2.调用fs 的readFile方法获取文件
// 参数1：读取文件的存放路径
// 参数2：读取时的编码格式 默认一般为 utf8
// 参数3：回调函数 拿取读取成功和失败的结果
fs.readFile("./files/1.txt", "utf8", function (err, dataStr) {
  if (err) {
    console.log("读取文件失败");
  }
  //   打印成功的结果
  console.log("文件的内容是" + dataStr);
});

```

### writeFile方法写入内容

```js
// 1.导入 fs模块 来操作文件
const fs = require("fs");

// 2.调用fs 的writeFile函数 写入文件的内容
// 参数1：表示文件的存放路径
// 参数2：表示写入的内容
// 参数3: 编码格式 默认utf8
// 参数4： 回调函数
fs.writeFile("./files/1.txt", "这里是WriteFile函数", function (err) {
  if (err) {
    console.log("写入失败");
  } else {
    console.log("写入成功");
  }
});

```



#### 一个写入的小例子

```js
// 1.导入 fs模块 来操作文件
const fs = require("fs");

// 2.读取文件内容
fs.readFile("./files/1.txt", "utf8", function (err, data) {
  if (err) {
    return console.log("读取文件失败" + err.message);
  } else {
    console.log("读取文件成功!" + data);
  }
  //   3.分割成绩数据
  const arrOld = data.split(" ");
  const arrNew = [];
  arrOld.forEach((item) => {
    arrNew.push(item.replace("=", ":"));
  });
  //   4.合并写入目标文件
  const newStr = arrNew.join("\r\n");
  fs.writeFile("./files/2.txt", newStr, function (err) {
    if (err) {
      return console.log("写入失败" + err.message);
    }
    console.log("写入成功");
  });
});

```

### 路径动态拼接

**代码在运行，会执行node命令所处的目录**

- 解决方法一： 绝对路径代替相对路径

  缺点 ：移植性非常差 不利于维护

- 解决方法二：**__dirname**

  ​	__dirname 表示的当前文件所处的目录

```js
// 1.导入 fs模块 来操作文件
const fs = require("fs");
const path = require("path");
// 2.调用fs 的writeFile函数 写入文件的内容
// 参数1：表示文件的存放路径
// 参数2：表示写入的内容
// 参数3: 编码格式 默认utf8
// 参数4： 回调函数
fs.writeFile(
  path.join(__dirname, "./files/2.txt"),
  "这里是WriteFile函数",
  function (err) {
    if (err) {
      console.log("写入失败" + err.message);
    } else {
      console.log("写入成功");
    }
  }
);

```

## PS模块

### path.join() 函数 路径拼接

**用来处理路径的模块**

**path.join()来进行路径拼接**

```js
const path = require("path");
// ../会抵消掉一层路径
const pathStr = path.join("/home", "/hexian/c", "../", "/NODE_练习");
console.log(pathStr); // /home/hexian/NODE_练习
const pathStr2 = path.join(__dirname, "./files/1.txt");
console.log(pathStr2); // /home/hexian/Node_练习/files/1.txt

```

### path. basename（）、extname()求文件名 文件后缀 函数函数

第二个参数为可选参数，如果不设置则输出名字+格式

```js
const path = require("path");

// 定义一个文件的存放路径
const fpath = "/home/hexian/Node_练习/files/1.txt";

const fullName = path.basename(fpath);
console.log(fullName); // 1.txt

const nameWithoutExt = path.basename(fpath, ".txt");
console.log(nameWithoutExt); //1

const Ext = path.extname(fpath);
console.log(Ext); //txt/

```

## HTTP 模块

### 创建基本的web服务器

```js
// 1.导入http模块
const http = require("http");
// 2.创建web服务器实例
const server = http.createServer();
// 3.为服务器绑定request时间，监听客服端的请求
server.on("request", function (req, res) {
  console.log("有人访问了服务器");
});
// 4.启动服务器
server.listen(8080, function () {
  console.log("已经运行在本机的8080端口了");
});
//终止 ctrl+C
```

### request 请求对象

**req返回的对象里 url属性为客户端请求的url地址，method属性为访问对象的方式**

```js
// 1.导入http模块
const http = require("http");
// 2.创建web服务器实例
const server = http.createServer();
// 3.为服务器绑定request时间，监听客服端的请求
server.on("request", (req) => {
  // 获取客户端请求的url地址
  const url = req.url;
  //  获取客户端请求的方式
  const method = req.method;
  console.log(`有人访问了服务器，请求的地址是${url}方式是${method}`);
});
// 4.启动服务器
server.listen(8080, function () {
  console.log("server running at http://127.0.0.1:8080");
});

```



**response相应对象**

**调用res.end()向客户端相应一些内容**

### 

```js
// 1.导入http模块
const http = require("http");
// 2.创建web服务器实例
const server = http.createServer();
// 3.为服务器绑定request时间，监听客服端的请求
server.on("request", (req, res) => {
  // 获取客户端请求的url地址
  const url = req.url;
  //  获取客户端请求的方式
  const method = req.method;
  const str = `有人访问了服务器，请求的地址是${url}方式是${method}`;
  //   调用res.end()向客户端相应一些内容
  res.end(str);
});
// 4.启动服务器
server.listen(8080, function () {
  console.log("server running at http://127.0.0.1:8080");
});

```



### 解决中文乱码

**重新设置响应头 解决中文乱码格式**

```js
  res.setHeader("Content-Type", "text/html; charset=utf-8");

```

### 根据不同url相应不同的html内容

```js
const http = require("http");

const server = http.createServer();

server.on("request", (req, res) => {
  //1.获取请求的url地址
  const url = req.url;
  //2. 默认设置 404NotFonud
  let content = "<h1> 404 Not Found</h1>";
  //3. 判断用户请求的页面
  if (url == "/" || url == "/index.html") {
    content = "<h1>首页</h1>";
  } else if (url == "/about.html") {
    content = "<h1>关于页面</h1>";
  }
  //4.设置响应体
  res.setHeader("Content-Type", "text/html; charset=utf-8");
  // 5.返回对象
  res.end(content);
});

server.listen(8080, () => {
  console.log("server running at http://127.0.0.1:8080");
});

```

## Node.js 中的模块化

### module对象

**首先看下module对象里面有什么**

```js
Module {
  id: '.',
  path: '/home/hexian/Node_练习',
  exports: {},//借助exports 将模块内的对象共享出去
  filename: '/home/hexian/Node_练习/11模块化.js',
  loaded: false,
  children: [],
  paths: [
    '/home/hexian/Node_练习/node_modules',
    '/home/hexian/node_modules',
    '/home/node_modules',
    '/node_modules'
  ]
}
```



exports可以将模块内的成员共享除去 

外界用require方法导入自定义模块                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       

**module.exports**默认情况为空

#### 向外共享

```js

const anthorName = "hexian";
module.exports.username = anthorName;
module.exports.sayHello = function () {
  console.log("Hello");
};
// module.exports ={ } (默认情况)

```

#### 其余引入

```js
// 引入自定义模块
const self_defined = require("./11模块化");
console.log(self_defined);
//{ username: 'hexian', sayHello: [Function (anonymous)] } 输出结果

```



### exports对象

**默认情况下，exports和module.exports指向同一个对象,最终共享结果，还是以module。exports指向的对象为准**

## npm工具与包

### 常用

设置淘宝镜像源（我不需要）

```js
npm install -g cnpm --registry=https://registry.npmmirror.com
```

查看当前的npm基础信息

```js
npm config list	
```

### 创建模块

创建模块，package.json 文件是必不可少的。我们可以使用 NPM 生成 package.json 文件，生成的文件包含了基本的结果。

```
$ npm init
This utility will walk you through creating a package.json file.
It only covers the most common items, and tries to guess sensible defaults.

See `npm help json` for definitive documentation on these fields
and exactly what they do.

Use `npm install <pkg> --save` afterwards to install a package and
save it as a dependency in the package.json file.

Press ^C at any time to quit.
name: (node_modules) runoob                   # 模块名
version: (1.0.0) 
description: Node.js 测试模块(www.runoob.com)  # 描述
entry point: (index.js) 
test command: make test
git repository: https://github.com/runoob/runoob.git  # Github 地址
keywords: 
author: 
license: (ISC) 
About to write to ……/node_modules/package.json:      # 生成地址

{
  "name": "runoob",
  "version": "1.0.0",
  "description": "Node.js 测试模块(www.runoob.com)",
  ……
}


Is this ok? (yes) yes
```

以上的信息，你需要根据你自己的情况输入。在最后输入 "yes" 后会生成 package.json 文件。

接下来我们可以使用以下命令在 npm 资源库中注册用户（使用邮箱注册）：

### 包管理配置文件

**快速创建 package.json**

```js
npm init -y
```

*项目名不要包含中文与空格*

### 如何一次安装所有的node_module

```
npm install 
```

运行时，npm会读取当前目录的package.json

上记录的所有依赖包

### devDependencies节点

**又名开发依赖包**

*dependencies节点 核心依赖包*

某些包只在开发阶段用而上线后不用则应放入这样节点中

```js
npm i 包名 -D
npm install 包名 --save-dev
```

### nrm工具

（更方便的镜像源切换）

**nrm ls **

```

  npm ---------- https://registry.npmjs.org/
  yarn --------- https://registry.yarnpkg.com/
  tencent ------ https://mirrors.cloud.tencent.com/npm/
  cnpm --------- https://r.cnpmjs.org/
  taobao ------- https://registry.npmmirror.com/
  npmMirror ---- https://skimdb.npmjs.com/registry/

```

**nrm use 镜像源**

## 发布自己的包

### 开发属于自己的包

- 需要实现的功能

- 初始化包的基本结构
        有包的根目录
        存在
       - package.json文件（包管理配置文件）
       - index.js （包的入口文件）
       - README.md（包的说明文件）
    
- 初始化package.json文件

### 发布一个包

- 在终端登录自己npm账号

  ```js
  npm login // 先切换回官方镜像源
  ```

- 进入项目根目录 

  ```js
  npm publish
  ```

- 删除已经发布的包
	```js
		npm unpubilsh 
	```

## 模块加载机制

- **模块在第一次加载后就会被缓存**

- **内置模块的加载优先级是最高的**

- **自定义模块加载必须需要有路径标识符**/

## Express框架

### Express的基本使用(请求篇)

#### request的 query对象

**通过req.query可以获取客户端发送过来的查询对象**

例如

```js
// 1.导入 express
const express = require("express");
// 2.创建web服务器
const app = express();
app.get("/", (req, res) => {
  // 通过req.query可以获取客户端发送过来的查询对象
  // req默认为空
  console.log(req.query);
  res.send(req.query);
});
// 3.启动web服务器
app.listen(8081, () => {
  console.log("服务器运行在：http://127.0.0.1:8081");
});
```

对其发送请求

```
http://127.0.0.1:8081?name=hexian&age=20
```

得到结果

```js
{
    "name": "hexian",
    "age": "20"
}//无查询条件默认为空
```

#### 如何获取URL中的动态参数

例

```js
// 这里的id是一个动态参数可以是任意值
app.get("/user/:id", (req, res) => {
  console.log(req.params);
  res.send(req.params);
});
```

发送请求

```js
http://127.0.0.1:8081/user/2
```

得到结果

```js
{
    "id": "2"
}
```

*可以获取多个动态参数*

### Express托管静态资源

**static()方法指定静态资源目录**

示例代码

```js
const express = require("express");
const app = express();

app.use(express.static("./public"));

app.listen(8080, () => {
  console.log("http://127.0.0.1:8080");
});

```

**被开放静态资源目录结构**

```tree
./public/
 └── cartoon.jpg
```

**发送请求**

```
http://127.0.0.1:8080/cartoon.jpg
```

*被设置为静态资源的目录不会出现在路径中*

**可同时托管多个静态资源目录，只需多次调用static方法即可**



- 如果需要增加访问路径前缀 ,则只需要增加一个路径参数即可

例如

```js
const express = require("express");
const app = express();

app.use("/public", express.static("./public"));

app.listen(8080, () => {
  console.log("http://127.0.0.1:8080");
});

```

```
http://127.0.0.1:8080/public/cartoon.jpg //访问路径

```



### Express 路由



Express不建议将路由直接挂载到app上，而推荐将路由抽离为单独的模块

1. 创建路由模块对应的.js文件
2. 调用express.Router()函数创建路有对象
3. 向路由对象上挂载具体的路由
4. 使用module.exports向外共享路由对象
5. 使用app.use()函数注册路由模块

  **路由模块对应的.js文件**

```js
const express = require("express");

const router = express.Router();

router.get("/user/list", (req, res) => {
  res.send("Get user list");
});
router.post("/user/add", (req, res) => {
  res.send("Add new user");
});
module.exports = router;

```

**导入路由模块**

```js
const express = require("express");
const app = express();

// 1.导入路由模块
const router = require("./15路由对象");

app.use(router);

app.listen(8081, () => {
  console.log("http://127.0.0.1:8081");
});

```

**为路由模块添加访问前缀和挂载静态资源时的操作一致*

### Express中间件

*本质是一个function处理函数*

#### **全局生效的中间件**

> 通过app.use将中间件注册为全局生效



```js
const express = require("express");

const app = express();

const mv = function (req, res, next) {
  console.log("这是最简单的中间件函数");
  next();
};

// 将mv注册为全局生效的中间件
app.use(mv);

app.get("/", (req, res) => {
  res.send("Home Page");
});
app.get("/user", (req, res) => {
  res.send("user Page");
});
app.listen(8081, () => {
  console.log("http://127.0.0.1:8081");
});

```

**简化形式**

```js
app.use((req, res, next) => {
  console.log("中间件运行");
  next();
});
```



**中间件的作用**

> 多个中间件共享一个req与res

例如

```js
app.use((req, res, next) => {
  console.log("中间件运行");
  const time = Date.now();
  req.require_time = time;
  next();
});
```



后序路由则可以直接从req或res中获取上游设置的属性

```js
app.get("/", (req, res) => {
  res.send("Home Page" + req.require_time);
});//Home Page1641721037593
```



#### 局部生效中间件

> 不适用app.use方法注册的中间件

```js

const mw1 = (req, res, next) => {
  console.log("局部生效");
  next();
};
app.get("/", mw1, (req, res) => {
  res.send("Home Page");
});
```

**在一个路由中可以挂载多个局部中间件，可用逗号分隔或[A,B,C]等形式**

**注意**

1. 一定要在路由之前注册中间件
2. 中间件必须调用next()函数
3. next()后不要在写额外代码了
4. 连续调用多个中间件 他们req与res共享

#### 中间件的分类

1. **应用级别的中间件**

   绑定到app实例上的中间件

2. **路由级别的中间件**

   绑定到exports.Router()实例上的中间件

3. **错误级别的中间件**

   > 必须注册在所有的路由之后

   例如：

   

   ```
   app.get("/user", (req, res) => {
     // 人为制造错误
     throw new Error("这是一个错误");
     res.send("User Page");
   });
   
   ```

   ```js
   // 定义错误级别的中间件，捕获整个项目的异常错误，防止程序的崩溃
   app.use((err, req, res, next) => {
     console.log("发生了错误" + err.message);
     res.send("Error:" + err.message);
   });
   ```

   

4. **Express内置中间件**

   1. express.static 托管静态资源

      ```js
      const express = require("express");
      const app = express();
      
      app.use("/public", express.static("./public"));
      
      app.listen(8080, () => {
        console.log("http://127.0.0.1:8080");
      });
      
      ```

      

   2. express.json解析JSON格式的请求提数据

      ```js
      const express = require("express");
      
      const app = express();
      
      //解析表单中的JSON格式数据
      app.use(express.json());
      app.post("/user", (req, res) => {
        // 在玩服务器中可以使用req.body属性接受客户端发送过来的请求体数据
        console.log(req.body);
        const resContent = req.body;
        res.send(resContent);
      });
      
      ```

      

   3. express.urlencoded解析URL-encoded格式请求体数据

      ```js
      const express = require("express");
      
      const app = express();
      //解析urlencoded格式的数据 固定写法
      app.use(express.urlencoded({ extended: false }));
      
      app.post("/book", (req, res) => {
        console.log(req.body);
        res.send(req.body);
      });
      app.listen(8081, () => {
        console.log("http://127.0.0.1:8081");
      });
      
      ```

      

5. **第三方的中间件** 

   

6. **自定义中间件**(重要)

   ```js
   const express = require("express");
   
   const app = express();
   
   // querystring 模块 解析请求体数据
   const querystring = require("querystring");
   
   // 这是解析表单数据的中间件
   app.use((req, res, next) => {
     // 定义中间件具体的业务逻辑
     let str = "";
     // data事件 当有数据传输过来时 就会触发 data量大的话 data事件会触发好几次
     req.on("data", (chunk) => {
       // 拼接请求体数据 隐式转换为字符串
       str += chunk;
     });
     // end事件，当data传输完毕时 就会触发end事件
     req.on("end", () => {
       // 在str中存放的是完整的请求体数据
       // console.log(str);
       // 把字符串格式的请求体数据解析成对象格式
       const body = querystring.parse(str);
       console.log(body);
     });
   });
   
   app.post("/user", () => {});
   app.listen(8081, () => {
     console.log("http://127.0.0.1:8081");
   });
   
   ```

### 跨域请求问题（重要 路由之前）

   **CORS**

1. 运行npm install cors 安装中间件

2. 导入中间件

      ```js
      // 一定要在路由模块引入之前配置cors模块 从而解决接口跨域请求问题
      const cors = require("cors");
      ```

3. 使用中间件 注意 应用应该是方法

   ```js
   app.use(cors());
   ```


### Express 与 mysql

#### 连接数据库以及验证



````js
// 导入mysql相关包
const mysql = require("mysql");

// 建立与数据库的连接
const db = mysql.createPool({
  host: "127.0.0.1",
  user: "root",
  password: "root",
  database: "node_database",
});

// 监测是否正确连接
db.query("select 1", (err, res) => {
  if (err) {
    console.log("连接异常" + err.message);
  }
  console.log(res);
});


````

#### 查询语句

```js
// 导入mysql相关包
const mysql = require("mysql");

// 建立与数据库的连接
const db = mysql.createPool({
  host: "127.0.0.1",
  user: "root",
  password: "root",
  database: "node_database",
});
const sql = "select * FROM user";

db.query(sql, (err, res) => {
  if (err) {
    console.log("连接异常" + err.message);
  }
  console.log(res);
});

```

#### 插入语句

> 通过res返回的affectedRows作为判断依据来判断是否插入成功

**方法一：朴素插入**

```js
// 导入mysql相关包
const mysql = require("mysql");

// 建立与数据库的连接
const db = mysql.createPool({
  host: "127.0.0.1",
  user: "root",
  password: "root",
  database: "node_database",
});
const insert_sql =
  "INSERT INTO `node_database`.`user` (`id`, `username`, `email`, `password`) VALUES ('4', 'two_test_man', '', '123456')";

db.query(insert_sql, (err, res) => {
  if (err) {
    console.log("连接异常" + err.message);
  }
  if (res.affectedRows === 1) {
    console.log("成功插入");
  }
});


```

**方法二：占位插入**

```js
const testMan3 = { username: "SpiderMan", password: "123456" };
const insert_sql =
  "INSERT INTO `node_database`.`user` ( `username`,`password`) VALUES ( ?,  ?)";//?占位 将由后面的数组元素依次替代

db.query(insert_sql, [testMan3.username, testMan3.password], (err, res) => {
  if (err) {
    console.log("连接异常" + err.message);
  } else if (res.affectedRows === 1) {
    console.log("成功插入");
  }
}
```

**方法三：对象插入**

```js
//直接将数据对象当成占位符值
const testMan4 = { username: "hexianMan", password: "123456" };
const insert_sql2 = "INSERT INTO `node_database`.`user` SET ?";
db.query(insert_sql2, testMan4, (err, res) => {
  if (err) {
    console.log("连接异常" + err.message);
  } else if (res.affectedRows === 1) {
    console.log("成功插入");
  }
})


```



#### 更新语句

> 同上 不写了

*对象中的属性会自动与数据库表中的列相匹配不需要绑定每个数据，*

```js
const hexianMan = { id: 6, username: "changMan", password: "123456" };
const update_sql = "UPDATE `node_database`.`user` SET ?  WHERE ID =?";
db.query(update_sql, [hexianMan, hexianMan.id], (err, res) => {
  if (err) {
    console.log("连接异常" + err.message);
  } else if (res.affectedRows === 1) {
    console.log("成功修改");
  }
});

```



### express的session中间件使用

> 引入中间件之后按以下格式使用

*当express-session配置成功后 即可通过req.session使用*



```js

// 引入session中间件
const session = require("express-session");
app.use(
  session({
    // 固定写法
    resave: false,
    // 固定写法
    saveUninitialized: true,
    // 任意字符作为加密手段
    secret: "hexian",
  })
);

```




## MYSQL （Linux学习）



*选择稳定版本，5.6拥有较完善的文档以及活跃的社区，在性能上5.6的性能也有了大幅提升*

### MYSQL安装

### rpm安装

**需要手段解决依赖**

```js
4个软件包依赖
mysql-client
mysql-devel
mysql-server
mysql-shared
// 进入镜像资源网站 wget对应资源即可
```

*不建议使用*

### yum安装

```
yum install mariadb-server mariadb
```

> 如果需要下载指定版本 需要查询对应的mysql版本的yum下载源

### apt-get安装

1. 安装服务端

   ```
   sudo apt-get install mysql-server
   ```

   

2. 安装客服端

   ```
   sudo apt-get install mysql-client
   ```

   

3. 安装编译时链接的库

   ```
   sudo apt-get install libmysqlclient-dev
   ```

   

### 编译安装

### 安装时一些踩过的坑

#### 1.误删/etc/mysql文件

**原因**

如果你因为觉得mysql不好用或者忘记密码等原因，因而删除了自己的/etc/mysql文件，那么在你下一次安装这类包的时候，系统会判定："哦，这家伙不想要/etc/mysql 这个文件（因为他曾经删除了），但是mysql需要，那好吧，你的想法优先，我报错。"

**解决**

你应该备份你的数据库，你可以试着清除所有mysql相关包(特别是mysql-common mariadb-server - *和/或mysql服务器- *)并重新安装它们。注意,只是删除包是没有用的，因为dpkg将保留您的本地修改/etc/mysql/设计。

那么以下是我的操作步骤

- 卸载以往的版本

  ```
  sudo apt-get remove --purge mysql-\*
  sudo apt-get autoremove --purge mysql-server
  sudo apt-get remove mysql-common
  ```

- 清除数据缓存

  ```
  dpkg -l |grep ^rc|awk '{print $2}' |sudo xargs dpkg -P
  ```

  

- 查看MySQL的剩余依赖项

  ```
  dpkg --list|grep mysql//如果有 则将依赖项删除
  sudo apt-get autoremove --purge mysql-apt-config
  ```

  

- 更新软件源

  ```
  sudo apt-get update
  ```
  
  
  
  *随后就可以重新安装了*
  
  
  
  **测试**
  
  检测有没有安装成功
  
  ```
  sudo netstat -tap|grep mysql
  ```
  
  
#### 2.mysql密码是什么来着？

有时候我们会忘记某个用户的账户以及密码，这时我们需要停止mysql服务，修改my.cnf文件，重新启动后跟新对应用户的密码，最后不要忘记把my.cnf文件修改回去

1. **停止mysql服务**

   ```
   service mysql stop
   ```

   

2. **修改my.cnf文件**

> 在某些版本中，原本的my.cnf文件引用了别的文件的软连接
>
> 因此您需要下到对应的文件进而修改文本 内容

```
如Server version: 8.0.27-0ubuntu0.20.04.1 (Ubuntu)
//文件目录为
├── conf.d
│   ├── mysql.cnf
│   └── mysqldump.cnf
├── debian.cnf
├── debian-start
├── my.cnf -> /etc/alternatives/my.cnf //软连接
├── my.cnf.fallback
├── mysql.cnf
└── mysql.conf.d
    ├── mysql.cnf
    └── mysqld.cnf //实际需要修改的目标文件


```



3. **重启mysql服务**

   ```
   server mysql start
   ```

4.**执行以下命令**

```
$ mysql

# 选择mysql数据库
mysql> use mysql;

# 更改user表中root用户密码
mysql> update user set authentication_string=PASSWORD("new_pass") where user='root';


```

5.**将my.cnf修改会原内容**





#### 3.未设置密码

> 有时候安装过程未出现密码设置的提示，安装完毕后无法知道登录任何一个User角色

*原因是因为在最近的Ubuntu安装（当然也可能是其他安装）中，MySQL默认使用了UNIX auth_socket plugin插件。*



```
$ sudo mysql -u root # I had to use "sudo" since is new installation

mysql> USE mysql;
mysql> SELECT User, Host, plugin FROM mysql.user;

+------------------+-----------------------+
| User             | plugin                |
+------------------+-----------------------+
| root             | auth_socket           |
| mysql.sys        | mysql_native_password |
| debian-sys-maint | mysql_native_password |
+------------------+-----------------------+

```



**这篇文章里有很多解决方法**

https://stackoverflow.com/questions/39281594/error-1698-28000-access-denied-for-user-rootlocalhost



而我的解决方法是，利用sudo特权登录到mysql中随后使用

```
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'new-password';//更新密码
```

## 基本常用指令

### **1、查看mysql版本**



```
方法一：status;
方法二：select version();
```

### **2、Mysql启动、停止、重启常用命令**



#### 启动方式

```
1、使用 service 启动：
[root@localhost /]# service mysqld start (5.0版本是mysqld)
[root@szxdb etc]# service mysql start (5.5.7版本是mysql)

2、使用 mysqld 脚本启动：
/etc/inint.d/mysqld start

3、使用 safe_mysqld 启动：
safe_mysqld&
```



#### 停止方式

```
1、使用 service 启动：
service mysqld stop

2、使用 mysqld 脚本启动：
/etc/inint.d/mysqld stop

3、mysqladmin shutdown
```



#### 重启方式

```

1、使用 service 启动：
service mysqld restart
service mysql restart (5.5.7版本命令)

2、使用 mysqld 脚本启动：
/etc/init.d/mysqld restart
```



### mysql用户设置

> 如果需要添加MYSQL用户，只需要对user表执行

```
INSERT INTO user 
          (host, user, password, 
           select_priv, insert_priv, update_priv) 
           VALUES ('localhost', 'newUser_name', 
           PASSWORD('newUser_password'), 'Y', 'Y', 'Y');
```



或者**(我用的)**

```
mysql> use mysql
// 创建新的用户
mysql> create user 'hexian'@'localhost' identified by '3628546';
Query OK, 0 rows affected (0.03 sec)
//用户授权
mysql> grant all privileges on *.* to 'hexian'@'localhost' with grant option;
Query OK, 0 rows affected (0.00 sec)
//刷新权限
mysql> flush privileges;
Query OK, 0 rows affected (0.01 sec)

```

### mysql创建数据库

> CREATE DATABASE 数据库名；

*注意，如果使用普通用户,可能需要特定权限来创建或者删除MySQL数据库,这里使用root用户 并用mysqladmin来创建数据库*



**命令**



```
 mysqladmin -u root -p create RUNOOB
```



**结果**

```
mysql> show databases
    -> ;
+--------------------+
| Database           |
+--------------------+
| RUNOOB             |
| information_schema |
| mysql              |
| performance_schema |
| sys                |
+--------------------+
5 rows in set (0.01 sec)

```

### mysql删除数据库

> drop database <数据库名>



**指令**

```
mysqladmin -u root -p drop RUNOOB
```



**结果1**



```
mysqladmin -u root -p drop RUNOOB
Enter password: 
Dropping the database is potentially a very bad thing to do.
Any data stored in the database will be destroyed.

Do you really want to drop the 'RUNOOB' database [y/N] y
Database "RUNOOB" dropped

```



**结果2**



```
show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| sys                |
+--------------------+
4 rows in set (0.00 sec)

```



# 项目实现

## 初始化

## 创建项目

```js
npm init -y
```

**引入对应的包文件**

```
api_server@1.0.0 /home/hexian/Node_test/api_server
├── body-parser@1.19.1
├── cors@2.8.5
├── express-jwt@6.1.0
├── express@4.17.2
├── jsonwebtoken@8.5.1
├── mysql@2.18.1
└── nodemon@2.0.15//这是常用的
```

**定义入口文件**

> 1.配置跨域请求
>
> 2.配置解析表单数据的中间件

```js
//导入express
const express = require("express");

// 创建服务器实例
const app = express();

// 解析表单数据的中间件 x-www 
app.use(express.urlencoded({ extended: false }));

// 导入并且配置cors中间件 注意是带有()的 并且需要在路由模块之前导入
const cors = require("cors");
app.use(cors());

// 导入注册登陆的路由模块
const userrouter = require("./router/user");
app.use("/api", userrouter);

// 启动服务器
app.listen(8081, () => {
  console.log("http://127.0.0.1:8081");
});

```



### 路由模块

> 模块目录

```
├── router //只存放映射关系
│   └── user.js
└── router_handler //真正函数处理模块
```



**在设计模块时，为了保证路由模块的纯粹性，所有的路由处理函数都必须抽离到对应的路由处理函数模块中**

*这是路由函数处理模块*

```js
// 注册新用户的处理函数
exports.regUser = (req, res) => {
  res.send("注册模块");
};

// 新用户登录的处理函数
exports.login = (req, res) => {
  res.send("登录模块");
};
```

*这是路由映射处理模块*

```js
const express = require("express");

const router = express.Router();

// 引入router_handler中的对应处理模块
const userRouter = require("../router_handler/user");
// 注册新用户
router.post("/regUser", userRouter.regUser);//可以看到 只接受暴露的模块 本身不写任何内容

//登录
router.post("/login", userRouter.login);

// 暴露这个路由模块
module.exports = router;

```



### 连接数据库的工具类

```js
// 导入mysql模块
const mysql = require("mysql");

// 建立与数据库的连接
const db = mysql.createPool({
  host: "127.0.0.1",
  user: "root",
  password: "root",
  database: "node_database",
});

// 暴露这个工具类
module.exports = db;
```

### 注册模块

**前置内容**

> 为了保证密码的安全性，不建议在数据库以明文形式保护用户的密码，推荐对密码进行加密存储

**bcryptjs**

1. 运行如下命令，安装指定版本的 `bcryptjs` ：

```bash
npm i bcryptjs@2.4.3Copy to clipboardErrorCopied
```

1. 在 `/router_handler/user.js` 中，导入 `bcryptjs` ：

```js
const bcrypt = require('bcryptjs')
```

1. 在注册用户的处理函数中，确认用户名可用之后，调用 `bcrypt.hashSync(明文密码, 随机盐的长度)` 方法，对用户的密码进行加密处理：

```js
// 对用户的密码,进行 bcrype 加密，返回值是加密之后的密码字符串
userinfo.password = bcrypt.hashSync(userinfo.password, 10)
```

- **实现步骤**

1. 检测表单数据是否合法
2. 监测用户名是否被占用
3. 对密码进行加密处理
4. 插入新用户

### 优化表单验证

> 后端永远不要相信前端提交过来的任何内容

```
express-joi辅助表单验证
注意 由于版本同步问题
当你使用的是
├── @escook/express-joi@1.1.1
├── @hapi/joi@17.1.1
两个包时
可能会出现以下情况
Error: Cannot mix different versions of joi schemas
```

**如何修改**

```
这是因为 @hapi/joi的版本过低而express-joi使用的是joi 一般会高出3个版本
你需要在node_moudules中找到escook 并
修改引入的Joi
//初始的包源↓
// const Joi = require('joi')
// 更改引入的包源↓ 
const Joi =require('@hapi/joi')

```



#### 用户验证规则

> 具体可以参考joi文档

```
// require the joi to help us check info
const joi = require("@hapi/joi");

/**
 * string() 值必须是字符串
 * alphanum() 值只能是包含 a-zA-Z0-9 的字符串
 * min(length) 最小长度
 * max(length) 最大长度
 * required() 值是必填项，不能为 undefined
 * pattern(正则表达式) 值必须符合正则表达式的规则
 */

// 定义用户名的检查规则
const username = joi.string().alphanum().min(3).max(10).required();

// 定义密码的监测规则

const password = joi
  .string()
  .alphanum()
  .pattern(/^[\S]{6,12}$/)
  .required();

//   暴露规则 方便app.js引用规则
exports.reg_login_shcema = {
  body: {
    username,
    password,
  },
};


```

#### 路由挂载匹配规则

```bash
├── app.js
├── db //数据库
│   └── index.js
├── package.json
├── package-lock.json
├── router //路由映射
│   └── user.js
├── router_handler //路由处理
│   └── user.js
└── schema //匹配规则
    └── user.js
```



*在/router/user.js中*

```
// 导入表单验证中间件
const expressJoi = require("@escook/express-joi");
// 导入用户注册验证规则
const { reg_login_shcema } = require("../schema/user");

// 注册新用户
// 加入验证Joi后，所发送的数据 会先通过expressJoi匹配是否合格，合格按原逻辑进行操作 不合格就会爆出全局ERR 由全局错误处理中间件处理
router.post("/regUser", expressJoi(reg_login_shcema), userRouter.regUser);


```

### 登录模块

> 实现步骤

1. 检测表单是否合法
2. 根据用户名查询用户数据
3. 判断密码是否匹配用户
4. 生产JWT的token字符串

## 个人中心

### 实现步骤

1. 初始化 **路由** 模块
2. 初始化 **路由处理函数** 模块
3. 获取用户的基本信息
