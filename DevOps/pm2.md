## 前言

pm2 是一个守护进程管理器，可以让应用在后台运行，无缝重启应用，开机自启动，负载均衡。它可以非常轻松的启动一个 application

-   pm2 start app.js
-   pm2 start app.py
-   pm2 start shell.sh
-   pm2 start binary-file

### 常用的命令

`pm2 start`
启动一个进程

`pm2 log`
显示日志信息

`pm2 restart`
重新启动进程

`pm2 reload`
重新加载进程

`pm2 stop`
暂停进程

`pm2 delete`
删除进程

`pm2 update`
更新 pm2

`pm2 updatePM2`
在内存中更新 pm2

`pm2 flush`
情况所有的日志文件

### 常用的 CLI 参数

`--name <name>`
为进程起一个名字

`--watch`
监听文件，发生改动时自动 restart

`--ignore-watch=filename`
忽略监听的文件，通常是 node_modules

`--log <log_path>`
制定日志存放位置

`--time`
给日志文件加上日期

`--lines <number>`
显示多少行日志

`-- arg arg`
传递额外的参数给进程

### 使用用例

```js
// 指定python3来运行脚本
pm2 start index.py --interpreter python3

// 使用 npm 来启动进程
pm2 start npm -- run dev

```

### pm2 配置

默认的配置文件路径 $HOME/.pm2

### 链接

[开机自启动](https://pm2.keymetrics.io/docs/usage/startup/)

[多应用编排](https://pm2.keymetrics.io/docs/usage/application-declaration/)

[负载均衡](https://pm2.keymetrics.io/docs/usage/cluster-mode/)

[简单实用](https://blog.csdn.net/qq_32281471/article/details/91369344)

[源代码](https://github.com/Unitech/pm2)

[原理分析:简书](https://www.jianshu.com/p/ac843b516fda)

[官方文档](https://pm2.keymetrics.io/docs/usage/quick-start/)
