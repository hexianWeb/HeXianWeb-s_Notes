# **tmux** 使用（常用）

> 作者：何贤

*在tmux中 所有

## tmux基本概念

> tmux 一款终端复用软件。他可以在一个窗口开启多个面板

## tmux安装

```
sudo apt-get install tmux
```

## tmux会话相关操作

### 新建session

```
tmux # 新建以一个无名的会话名称
tmux new -s <termials_name> #新建一个有名的会话
```

### 断开当前session

```
tmux detach # 断开当前会话
```

### 进入之前的session

```
tmux a # 默认进入第一个会话
tmux a -t demo #进入到名称为demo的会话
```

### 关闭会话

```
tmux kill-session -t demo #关闭名为demo的会话
tmux kill-server #关闭服务器
```



### 查看会话

```
tmux ls # 查看所有会话
```



## tmux面板(常用)



| 按键   | 描述             |
| ------ | ---------------- |
| "      | 上下分割         |
| %      | 左右分割         |
| x      | 关闭面板         |
| z      | 最大化当前面板   |
| 方向键 | 调整当前所处终端 |
| t      | 显示time         |





## tmux窗口(常用)



| 按键 | 描述         |
| ---- | ------------ |
| c    | 新建         |
| &    | 关闭当前窗口 |
| 0~9  | 切换指定窗口 |
| p    | 上一窗口     |
| n    | 下一窗口     |
| ，   | 重命名窗口   |



## tmux系统（常用）

| 按键 | 描述                |
| ---- | ------------------- |
| ?    | 帮助文档            |
| d    | 断开会话(detach)    |
| r    | 重新载入当前回话    |
| [    | 复制模式 （q:quit） |
| ]    | 粘粘                |



**参考文档**

```
http://louiszhai.github.io/2017/09/30/tmux/
```









