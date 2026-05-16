---
title: 系统部署及维护常用linux命令整理
date: 2026-05-16 19:24:51
tags:
---
# 系统部署及维护常用linux命令整理

<!-- toc -->

# 一、系统命令类
## 1.Linux 查看操作操作系统版本
`cat /etc/redhat-release`

## 2.启动或关闭防火墙
①Centos6启动或关闭防火墙及及开放指定端口

| 版本    | 开启                      | 关闭                     | 状态                       | 开放端口                                                     |
| ------- | ------------------------- | ------------------------ | -------------------------- | ------------------------------------------------------------ |
| Centos6 | service iptables start    | service iptables stop    | service iptables status    | firewall-cmd --zone=public --add-port=端口/tcp --permanentfirewall-cmd --reload |
| Centos7 | systemctl start firewalld | systemctl stop firewalld | systemctl status firewalld | firewall-cmd --zone=public --add-port=端口/tcp --permanentfirewall-cmd --reload |

②部署系统时需要关闭selinux,关闭selinux方法

`vim /etc/selinux/config，将selinux的值改为disabled如下图：`

## 3.配置YUM源

①删除默认yum源配置文件

cd /etc/yum.repos.d
rm -rf *

②挂载本地yum源镜像（需将镜像文件放置到opt目录下）
cd /opt
mount  -t  iso9660  -o  loop  CentOS-7.9-x86_64-DVD-2009.iso  /mnt

③新建yum源配置文件并设好默认参数

**vim  /etc/yum.repos.d/luocs.repo**

```
[Server]
name=Server
baseurl=file:///mnt/
gpgcheck=0
enabled=1
Type your information message here.
```

④检查yum源
yum list

## 4.修改主机名
vim /etc/hosts
在最后一行添加，IP地址与主机名的映射，如下图：

```
									关于主机名的小贴士

修改主机名

hostnamectl set-hostname 主机名

查看主机名

hostname
```

# 二、<a id="linux-title2">压缩和解压类</a>
## 1.gzip/gunzip
gzip命令用来压缩文件。gzip是个使用广泛的压缩程序，文件经它压缩过后，其名称后面会多处“.gz”扩展名。
gunzip命令用来解压缩文件。gunzip是个使用广泛的解压缩程序，它用于解开被[gzip](http://man.linuxde.net/gzip)压缩过的文件，这些压缩文件预设最后的扩展名为.gz。
gzip 文件
gunzip 文件

## 2.zip/unzip
zip [选项] xxx.zip 将要压缩的内容
unzip [选项] xxx.zip
常用选项
- zip -r 递归压缩，压缩目录
- unzip -d <目录> 指定解压后文件的存放目录

## 3.tar命令

| 选项 | 功能               |
| :--- | :----------------- |
| -c   | 产生.tar打包文件   |
| -v   | 显示详细信息       |
| -f   | 指定压缩后的文件名 |
| -z   | 打包同时压缩       |
| -x   | 解包.tar文件       |

常用打包解压命令
解压到当前目录：tar -zxvf xxx.tar.gz
解压到指定目录：tar -zxvf xxx.tar.gz -C 【指定目录】
打包：
tar -zcvf xxx.tar.gz 【打包的内容】
tar -cvf xxx.tar 【打包的内容】

# 三、<a id="linux-title3">文件目录类</a>

##  1.pwd指令
显示当前工作目录的绝对路径

##  2.ls指令
查看当前目录的所有内容信息
ls [选项] [目录或文件]
常用选项
-a:显示当前目录所有的文件和目录，包含隐藏的
-l:以列表的方式显示信息
## 3.cd指令
切换到指定目录
cd  【文件路径】
特殊
cd ~或cd:回到当前用户的家目录
cd ..:回到上一级目录
## 4.mkdir指令
创建目录
mkdir [选项] 要创建的目录
常用选项
-p创建多级目录
## 5.rmdir指令
删除空目录
rmdir [选项] 要删除的空目录
使用细节
rmdir删除的是空目录，要求这个目录下没有子文件也没有文件夹
如果需要删除非空目录，需要使用rm -rf 要删除的目录
例如rm -rf /home/animal
## 6.touch指令
创建文件
touch 文件名称
## 7.cp指令
拷贝文件到指定目录
cp [选项] source dest
常用选项
-r递归复制整个文件夹
使用细节
使用\cp强制覆盖不提示
## 8.rm指令
删除文件或目录
rm [选项] 要删除的文件或目录
常用选项
-r递归删除
-f强制删除
## 9.mv指令
移动文件与目录 或重命名
mv oldFileName newFileName (重命名)
mv 【文件】【指定目录】 (移动文件)

## 10.cat指令
查看文件内容
cat [选项] 要查看的文件
常用选项
-n显示行号
使用细节
cat只能浏览文件，而不能修改文件
## 11.more指令
一个基于VI编辑器的文本过滤器
more 要查看的文件
操作	功能说明
space（空格）	向下翻一页
enter（回车）	向下翻一行
q	立刻离开
ctrl+f	向下滚动一个屏
ctrl+b	返回上一屏
## 12.less指令
less 要查看的文件
操作	功能说明
space	向下翻一页
[pagedown]	向下翻一页
[pageup]	向上翻一页
/字串	向下搜寻[字串]；n：向下查找，N：向上查找
?字串	向上搜寻[字串]；n：向上查找，N：向下查找
q	立刻离开
## 13.echo指令
输出内容到控制台
案例
echo $PATH输出环境变量
echo $HOSTNAME输出主机名
##  14.head/tail指令
显示文件开头/结尾的部分内容，默认前10行
head 文件名
head -n 5 文件名   【显示前5行】
tail 文件名
tail -n 5 文件名 【显示后5行】
tail -f 文件名 实时监控文件追加的内容
##  15.ln指令
软链接，存放其他文件的路径 ln->link
ln -s [原文件或目录] [软连接名] (给文件创造一个软连接)
unlink 软连接名 (删除软连接)
启动软链接的服务：/etc/init.d/链接名 start
关闭软链接的服务：/etc/init.d/软链接名 stop
##  16.history指令
查看已经执行的命令，也可以执行某个历史命令
history (显示所有历史命令)
history 10 (显示近期10个历史命令)
!10 执行历史中第10条命令



# 四、<a id="linux-title4">时间日期类 </a>
## 1.date指令
①显示当前日期
date 显示年、月、日、时、分、秒、及周几
date +%Y  显示年份
date +%m  显示月份
date +%d  显示一个月中第几天
date "+%Y-%m-%d %H:%M:%S"  显示年、月、日、时、分、秒
②设置日期
date -s 字符串时间
示例：
date -s 2023-02-03 20:02:10

## 2.cal指令
查看日历
案例
- cal显示当前日历
- cal 2023   显示2023年日历

# 五、<a id="linux-title5">搜索查找类</a>
## 1.find指令
从指定目录向下递归地遍历其各个子目录并显示
find [搜索范围] [选项]
| 选项  | 功能                            |
| :---- | :------------------------------ |
| -name | 按照指定文件名查找模式          |
| -user | 查找属于指定用户的文件          |
| -size | 按指定大小查找(单位可以是M,G,k) |
案例
find /home -name *.java 在/home目录下查找java文件
find /opt -user easytong 在/opt目录下查找属于easytong的文件
find / -size +200M查找整个linux系统下大于200M的文件
+n大于n，-n小于n，n等于n

**常用指令：**

find /opt/test -name '*.sh' -exec chmod +x {} \; 将test目录下所有的.sh文件添加可执行权限

## 2.locate指令

快速定位文件路径，使用前需要执行updatedb

updatedb (locate基于数据库查询，必须定期更新)

locate 文件名

## 3.grep与|

grep过滤查找，|表示将前一个命令的结果传递给后面的命令处理

示例：查询系统中在运行的java进程

ps -ef | grep java

# 六、<a id="linux-title6">组管理</a>

在linux中的每个用户必须属于一个组，不能独立于组外。

在linux中每个文件都有所有者，所在组，其他组的概念。

## 1.文件/目录所有者

- 查看文件所有者
  ls -ahl
- 修改文件所有者(单独修改文件所有者，不会影响其所在组)
  chown 用户名 文件名
  参数 -R 递归执行
- 修改文件所在组
  chgrp 组名 文件名
  参数 -R 递归执行

chown newowner 文件/目录 (改变所有者)

chown newowner:newgroup 文件/目录 (改变所有者和所在组)

参数 -R:如果是目录，则递归生效

## 2.组的创建

- 创建组
  groupadd easytong
- 创建一个用户并指定组
  useradd -g easytong tom

# 七、<a id="linux-title7">权限管理</a>
## 1.权限的基本介绍

drwxr-xr-x 说明

- 第0位 文件类型
  - l是链接
  - d是目录
  - c是字符设备文件，如鼠标/键盘
  - b是块设备，如硬盘
  - \- 是文件
- 第1~3位 文件所有者的权限
- 第4~6位 同组用户的权限
- 第7~9位 其他组用户的权限

## 2.rwx权限详解

- rwx作用于文件
  - r代表可读
  - w代表可写
    (不代表可删除，可删除文件的前提是对此文件所在目录具有写权限)
  - x代表可执行
- rwx作用于目录
  - r代表可读，可以使用ls查看目录内容
  - w代表可写，可以修改。可在目录内创建+删除+重命名目录
  - x代表可执行，即可以进入该目录

可以用数字表示；r=4,w=2,x=1；rwx=4+2+1=7

## 3.修改权限

chmod(全称:change mode);控制用户对文件的权限的命令
①+、-、=变更权限
u所有者;g所有组;o其他人;a所有人(即u+g+o)
chmod u=rwx,g=rx,o=x 文件/目录
chmod o+w 文件/目录
chmod a-x 文件/目录
②通过数字变更权限
r=4 w=2 x=1
chmod u=rwx,g=rx,o=x 文件/目录相当于chmod 751 文件/目录
示例：
将/home/abc.txt文件的权限修改成rwxr-xr-x
chmod 755 /home/abc.txt

# 八、<a id="linux-title8">定时任务调度</a>
## 1.crontab循环定时任务

crontab 进行定时任务的设置

| 选项 | 功能                |
| :--- | :------------------ |
| -e   | 编辑crontab定时任务 |
| -l   | 查询crontab定时任务 |
service crond restart 重启任务调度

## 2.快速入门

设置任务调度文件:/etc/crontab
案例：每小时每分钟执行一次 ls -l /etc/ > /tmp/to.txt
1. crontab -e
2. */1 * * * * ls -l /etc/ -> /tmp/to.txt

## 5个占位符的说明

| 项目    | 含义                           |
| :------ | :----------------------------- |
| 第一个* | 一小时中的第几分钟             |
| 第二个* | 一天中的第几小时               |
| 第三个* | 一个月的第几天                 |
| 第四个* | 一年中的第几月                 |
| 第五个* | 一周中的星期几 (0,7都代表周日) |

## 特殊符号说明

| 特殊符号 | 含义                                                         |
| :------- | :----------------------------------------------------------- |
| *        | 代表任何时间，如第一个*代表一小时中的每一分钟                |
| ,        | 代表不连续的时间，如0 8,12,16 * * *代表每天的8:00,12:00,16:00执行一次 |
| -        | 代表连续的时间范围，如0 5 * * 1-6代表周一到周六的5:00执行一次 |
| */n      | 代表每隔多久执行一次，如*/10 * * * *代表每10分钟执行一次     |

## 3.应用案例

1. 每隔1分钟，将当前的日期信息追加到/tmp/mydate中
   */1 * * * * date >> /tmp/mydate/
2. 每隔1分钟，将当前日期和日历都追加到/tmp/mycal中
   ①vim /home/my.sh写入date >> /tmp/mycal和cal >> /tmp/mycal
   ②chmod u+x /tmp/my.sh给my.sh增加执行权限
   ③crontab -e并写入*/1 * * * * /home/my.sh
   3.每天2:00将mysql数据库testdb备份到文件中
   0 2 * * * mysqldump -u root -p root testdb > /home/db.bak