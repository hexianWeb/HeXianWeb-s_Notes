# Node加Express进阶

> 作者：何贤

## 获取Node

```
在系统上安装 Node 非常简单。Node 团队做了很多努力，以确保在所有主流平台上都能简
单直接地安装 Node
```

**Linux用户背包管理器提供的Nodejs版本会非常的古老，尽量去官网手动下载**,当然也不是不能用，只不过在安装依赖时会遇到一些麻烦。

- 这里我将所有的安装方法（仅Linux版本列出

  ）

```
# 1.Linux自带包管理工具安装
sudo apt-get install nodejs
//你会获得一个低版本但不用手动配置的node

# 2.去Node官方网站下载
//官网网址：https://nodejs.org/zh-cn/download/

wget https://nodejs.org/dist/v10.16.0/node-v10.16.0-linux-x64.tar.xz    // 下载某个你需要的版本
tar xf node-v10.16.0-linux-x64.tar.xz  // 解压
cd node-v10.16.0-linux-x64  // 进入解压目录
# ./bin/node -v   // 执行node命令 查看版本
v10.16.0

//不要忘记你需要为下载的Nodejs增加软连接 方便使用
ln -s /usr/software/nodejs/bin/npm   /usr/local/bin/ 
ln -s /usr/software/nodejs/bin/node   /usr/local/bin/
```



### 最优解决方案

> 正如标题所说，我认为在学习阶段应该安装nvm,他可以让你使用不同版本的node

**安装或更新**

安装或更新nvm,你应该运行安装脚本。要做到这一点,你可以手动下载并运行脚本,或者使用卷曲或Wget命令如下:

```bash

curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.1/install.sh | bash

wget -qO- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.1/install.sh | bash
```

随后执行这个命令：

```bash
export NVM_DIR="$([ -z "${XDG_CONFIG_HOME-}" ] && printf %s "${HOME}/.nvm" || printf %s "${XDG_CONFIG_HOME}/nvm")"
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh" # This loads nvm
```



**这里有一些nvm常用的命令提示**

```
- `nvm arch`：显示node是运行在32位还是64位。
- `nvm install <version> [arch]` ：安装node， version是特定版本也可以是最新稳定版本latest。可选参数arch指定安装32位还是64位版本，默认是系统位数。可以添加--insecure绕过远程服务器的SSL。
- `nvm list [available]` ：显示已安装的列表。可选参数available，显示可安装的所有版本。list可简化为ls。
- `nvm on` ：开启node.js版本管理。
- `nvm off` ：关闭node.js版本管理。
- `nvm proxy [url]` ：设置下载代理。不加可选参数url，显示当前代理。将url设置为none则移除代理。
- `nvm node_mirror [url]` ：设置node镜像。默认是https://nodejs.org/dist/。如果不写url，则使用默认url。设置后可至安装目录settings.txt文件查看，也可直接在该文件操作。
- `nvm npm_mirror [url]` ：设置npm镜像。https://github.com/npm/cli/archive/。如果不写url，则使用默认url。设置后可至安装目录settings.txt文件查看，也可直接在该文件操作。
- `nvm uninstall <version>` ：卸载指定版本node。
- `nvm use [version] [arch]` ：使用制定版本node。可指定32/64位。
- `nvm root [path]` ：设置存储不同版本node的目录。如果未设置，默认使用当前目录。
- `nvm version` ：显示nvm版本。version可简化为v。
```

方便得切换工作环境

## 卸载Node

> 安全的卸载，这真的很重要

```bash
    #apt-get 卸载
    sudo apt-get remove --purge npm
    sudo apt-get remove --purge nodejs
    sudo apt-get remove --purge nodejs-legacy
    sudo apt-get autoremove

    #手动删除 npm 相关目录
    rm -r /usr/local/bin/npm
    rm -r /usr/local/lib/node-moudels
    find / -name npm
    rm -r /tmp/npm* 
```



> 当然 ，软连接有时候会很烦 找不到目标文件夹，我可去他的吧

**这里我提供一个清除软连接的脚本**

```bash]
#! /bin/bash
echo "enter scan path: "
read path

if [ -z $path ]
then
    echo "please enter scan path"
    exit
fi

for file in $(find $path -type l)
do
    if [ ! -e $file ]
    then
        echo "rm $file"
        rm -f $file
    fi
done
~       
```



## npm

> npm 是随处可见的 Node 开发包管理器



```bash
npm init //任何时候你都应该优先想到这个

//此命令将要求你输入几个参数，例如此应用的名称和版本。 你可以直接按“回车”键接受大部分默认设置即可，下面这个除外：
entry point: (index.js) // 入口文件 你不会想看到漫天的index.js 所以请命名你的入口文件在初始化项目时
```

npm 在 **package.json** 文件中管理项目的依赖项以及项目的元数据。要创建这个文件，最简单的办法是运行 npm init ：它会问一系列的问题，然后为你生成一个 package.json 文件帮你起步,



```bahs
npm install --save <package name>
```

如果你用了 --save 选项，它还会更新 package.json 文件。因为 node_modules 随时都可以用 npm 重新生成.（记得把node_modules添加进.gitignore文件中，你不会想花半个小时去拉取一个项目）



### npm 常用指令 

```
npm install --save package
npm views package versions
```






*也许是我学的太少了，这里待补充*

# Express

## 安装

```bash
npm install --save express
```



> 推荐用npx （npm要在8.2.0以上）

```bash
$ npx express-generator --view=hbs
```

**随后执行**npm install即可创建完毕。

## 第一步：精简的服务器

> 凡事总有第一步



```js
var app = express();
app.set('port', process.env.PORT || 3000);
// 定制 404 页面
app.use(function(req, res){
res.type('text/plain');
res.status(404);
res.send('404 - Not Found');
});
// 定制 500 页面
app.use(function(err, req, res, next){
console.error(err.stack);
res.type('text/plain');
res.status(500);
res.send('500 - Server Error');
});
app.listen(app.get('port'), function(){
console.log( 'Express started on http://localhost:' +
a
```

> node 入口文件名.js 启动就额可以在本地的3000端口看见一个简陋页面



## Handlebars？ 模板框架！

*tips:这是一种抽象程度较低的模板框架*

### 安装视图引擎

> npm install --save express3-handlebars

*尽管名字中是 express3，但这个包在 Express4.0 中也可以使用*

- 但凡你尝试过用JS生成一些HTML，都知道这绝对不能称得上是一中好的体验，这也是为什么有的PHP后端开发者会将这个垃圾语言视为珍宝。

**Jade**是Express初始自带的模板引擎，这毫不意外，毕竟JS从头写到尾的体验谁不喜欢呢！但我还是个新手，我可不想学了大半天的HTML化为灰烬。

![](https://i.imgur.com/xKzYs23.png)



## QA

> 毋庸置疑 QA很重要 

### mocha：页面测试

> 来次页面测试吧

**首先 我们需要一个测试框架**

```
npm install --save-dev mocha
```

*tips:注意，我们用的是 --save-dev 而不是 --save ，这是告诉 npm 要把这个包放在开发依赖项中，
不要放在运行时依赖项里。这样当我们部署网站的现场实例时，可以减少项目的依赖项。*

**其次我们需要一个断言库**

```bash
npm install --save-dev chai
```

**最后我希望你在入口文件内加入以下代码**

```js
app.use(function(req, res, next){
res.locals.showTests = app.get('env') !== 'production' &&
req.query.test === '1';
next();
});
```

这个中间件会帮助我们有条件的引入测试框架，没人希望在浏览网页的时候看到测试内容



例如：

```html
<head>
<title>Meadowlark Travel</title>
    {{#if showTests}} //当locals里的showTests为true时 所有的测试会被引入
    <link rel="stylesheet" href="/vendor/mocha.css">
    {{/if}}
<script src="//code.jquery.com/jquery-2.0.2.min.js"></script>
</head>
{{#if showTests}}	
<div id="mocha"></div>
<script src="/vendor/mocha.js"></script>
<script src="/vendor/chai.js"></script>
<script>
mocha.ui('tdd'); //tdd是我们应该尝试的形式
var assert = chai.assert;
</script>
<script src="/qa/tests-global.js"></script>
{{#if pageTestScript}}
<script src="{{pageTestScript}}"></script>
{{/if}}
<script>mocha.run();</script>
{{/if}}
```



**Grunt 你一定需要的集成插件**



