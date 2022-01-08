# Nodejs基础复习

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
console.log(Ext); //txt

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

  
