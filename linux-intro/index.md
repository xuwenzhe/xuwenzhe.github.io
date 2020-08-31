# Linux入门


## 目录结构
* `/bin` (`/usr/bin`, `/usr/local/bin`): binary,存放最经常使用的命令
* `/sbin` (`/usr/sbin`, `/usr/local/sbin`): s: super user, 存放系统管理员使用的系统管理程序
* `/home`: 存放普通用户的主目录，每个用户都有一个自己的目录
* `/root`: 超级权限者的用户主目录(This is not the root (/) filesystem. It is the home directory for the root user.)
* `/lib`: 系统开机所需要最基本的动态连接共享库，类似Windows的DLL文件。几乎所有应用程序都需要用到这些共享库
* `/etc`: 系统管理所需要的配置文件和子目录
* `/usr`: 用户的很多应用程序和文件都放在这个目录下，类似Windows的program files目录
* `/proc`,`/srv`,`/sys`: 内核相关目录（不要轻易修改）
* `/dev`: 硬件以文件的形式存放在该处
* `/media`: U盘，光驱，等等
* `/mnt`: 用户临时挂载的文件系统
* `/opt`: 存放安装软件
* `/usr/local`: 存放安装软件所安装的目录，一般是通过编译源码的方式安装的程序
* `/var`: 存放经常被修改的东西，比如日志文件


{{< figure src="https://media.geeksforgeeks.org/wp-content/cdn-uploads/unix.png" class="center" width="1000" >}}



## Vi/Vim快捷键

1. 拷贝当前行`yy`, 拷贝当前行以下的5行`5yy`,粘贴`p`
2. 删除当前行`dd`, 删除当前行以下的5行`5dd`
3. 查找关键词`/keyword`,下一个出现`n`
4. 显示行号`:set nu`,隐藏行号`:set nonu`
5. 阅览最首行`gg`最末行`G`
6. 撤销输入动作`u`
7. 移动光标至第20行`20`+`shift g`

## 开关重启命令

```bash
sync # 把内存数据同步到磁盘，以下操作前先执行该命令
shutdown -h now # 立即关机
shutdown -h 1 # 1min 关机
shutdown -r now # 立即重启
halt # 等同于关机
reboot # 立即重启
```

## 用户管理

```bash
useradd xiaoming # 自动创建home下同名目录
passwd xiaoming # 指定密码
userdel xiaoming # 删除用户xiaoming，home下其目录会保留
userdel -r xiaoming # 删除用户xiaoming，home下其目录不会保留
su - xiaoming # 切换用户登陆， 权限不足会给提示，exit返回到原先用户
groupadd zuming # 创建组
groupdel zuming # 删除组
useradd -g zuming xiaoming # 创建用户时，指定组
id xiaoming # 查询用户信息
usermod -g zuming2 xiaoming # 修改用户所在组
```

* 用户配置文件在： `/etc/passwd` （用户id，组id，家目录，shell）
* 组配置文件在：`/etc/group`
* 口令配置文件（密码，登录信息）：`/etc/shadow`(加密的)

## 实用命令

```bash
man ls # 显示ls用法
help cd # 显示cd用法
touch filename # 创建空文件
cat -n filename ｜ more # 打开文件内容（只读方式）, -n 显示行号，more分页显示
more filename # 按页显示文件，空格翻页，Enter下一行，ctrl+f/b
less filename # 分页查看，lazy加载，利于显示大文件 "less is more"
ls -l > filename # 重定向（覆盖）
ls -l >> filename # 追加（append到尾部）
head -n 5 filename # 只显示文件前5行（默认10行）
tail -n 5 filename # 只显示文件后5行（默认10行） -f追踪文件更新，日志监控经常用
date "+%Y-%m-%d %H:%M:%S" # 按格式显示当前年月日时分秒，“+”必要， -s设定
cal # 显示当前日历
find [scope] [-name 文件名, -user 用户, -size 文件大小]
grep [-n 显示匹配行行号， -i 忽略大小写] 查找内容 源文件
cat hello.txt | grep -ni yes # 在hello.txt中，不区分大小写的查找yes
grep -r [keyword] /data/reports
zip -r xxx.zip files
unzip -d 位置 xxx.zip
tar -zcvf a.tar.gz a1.txt a2.txt
tar -zxvf a.tar.gz
```


## 组管理，权限管理
在linux中的每个用户必须属于一个组，不能独立于组外。在linux中每个文件有所有者，所在组，其他组的概念。



## 任务调度

```bash
crontab -e
crontab -l
crontab -r
```

`45 22 * * *` 在22点45分执行

`0 17 * * 1` 每周一17点0分执行

`0 5 1,15 * *` 每月1号15号凌晨5点执行

`40 4 * * 1-5` 每周一到周五凌晨4点40分执行

`*/10 4 * * *` 每天凌晨4点每隔10分执行一次

`0 0 1,15 * 1` 每月1号和15号，每周一的0点0分执行。星期几和几号最好不要同时出现，容易混乱


## 进程管理

```bash
ps -a/u/x | more
ps -ef | grep sshd # 查看sshd的父进程ID
kill [pid]
pstree -p
top
netstat -anp | more # 查看网络服务
```

* `USER` 用户名称
* `PID` 进程号
* `%CPU` 进程占用CPU的百分比
* `%MEM` 进程占用物理内存的百分比
* `VSZ` 进程占用的虚拟内存大小（KB）
* `RSS` 进程占用的物理内存大小（KB）
* `TT` 终端名称
* `STAT` 进程状态 S-睡眠 R-正在运行 Z-僵死
* `STARTED` 进程的启动时间
* `TIME` 使用CPU总时间
* `COMMAND` 启动进程的命令和参数

## 服务管理

开机流程：

开机 -> BIOS -> /boot -> init process (1) -> 运行级别 -> 运行级别对应的服务


```bash
service [service name] start|stop|restart|reload|status
ls -l /etc/init.d
chkconfig --list
```

服务的运行级别(runlevel)：
* 运行级别0: 系统停机状态，系统默认运行级别不能设为0，否则不能正常启动
* 运行级别1: 单用户工作状态，root权限，用于系统维护，禁止远程登录
* 运行级别2: 多用户状态（没有NFS），不支持网络
* 运行级别3: 完全的多用户状态（有NFS），登陆后进入控制台命令行模式
* 运行级别4: 系统未使用，保留
* 运行级别5: X11控制台，登陆后进入图形GUI模式
* 运行级别6: 系统正常关闭并重启，默认运行级别不能设为6，否则不能正常启动

## Ubuntu

```bash
su root # 切换root用户，因为初始root未设置密码，提示错误
sudo passwd # 设置root密码
su root # 切换root用户，设置root密码后可以成功切换
```



































