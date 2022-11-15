---
title: Shell 脚本
---

- 交互式 （interactive shell）
等待用户的输入、并且执行用户键入的命令
- 非交互式 （non-interactive shell）
读取存放在文件中的命令并执行
- 登录式 （login shell）
需要用户使用账号密码登录后才能进入的shell
- 非登录式 （non-login shell）
即不需要用户登录的shell
- 交互式登录shell
通过终端输入账号密码、会清除掉所有变量，通过文件重新读入
- 非交互登录shell
通过终端输入账号密码、会继承上一个shell的全部变量

### .bashrc
它是Bash的交互式非登录配置文件、如果使用的Shell不是Bash，而是ZSH，那么它的配置文件就是.zshrc
环境变量、别名通常会存放在这个配置文件中、每当打开Bash Shell时，它都会运行。

存放位置
~/.bashrc 当前用户
/etc/bashrc 系统

### .profile
它是整个系统Shell的配置文件，它也可以用来写入各种命令、环境变量、别名。它会在用户登入之后执行一次

存放位置
~/.profile 当前用户
/etc/profile 系统


[bash和zsh的区别](https://blog.csdn.net/qq_40520596/article/details/104642218)
[基础语法](https://www.runoob.com/linux/linux-shell.html)