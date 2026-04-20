---
title: 分享几个常用的运维 shell 脚本
date: 2026-04-21 02:19:07
tags: ['分享','运维']
---

今天咸鱼给大家分享几个不错的 Linux 运维脚本，这些脚本中大量使用了 Linux 的文本三剑客：
```
1. awk
2. grep
3. sed
```
建议大家这三个工具都要了解并最好能够较为熟练的使用

**根据 PID 显示进程所有信息**

**根据用户输入的 PID，过滤出该 PID 所有的信息**

{% asset_img  1.png%}

```
#! /bin/bash

read -p "请输入要查询的PID: " P

n=`ps -aux| awk '$2~/^'${P}'$/{print $0}'|wc -l`

if [ $n -eq 0 ];then
 echo "该PID不存在！！"
 exit
fi
echo -e "\e[32m--------------------------------\e[0m"
echo "进程PID: ${P}"
echo "进程命令：$(ps -aux| awk '$2~/^'$P'$/{for (i=11;i<=NF;i++) printf("%s ",$i)}')"
echo "进程所属用户: $(ps -aux| awk '$2~/^'$P'$/{print $1}')"
echo "CPU占用率：$(ps -aux| awk '$2~/^'$P'$/{print $3}')%"
echo "内存占用率：$(ps -aux| awk '$2~/^'$P'$/{print $4}')%"
echo "进程开始运行的时间：$(ps -aux| awk '$2~/^'$P'$/{print $9}')"
echo "进程运行的时间：$(ps -aux| awk '$2~/^'$P'$/{print $10}')"
echo "进程状态：$(ps -aux| awk '$2~/^'$P'$/{print $8}')"
echo "进程虚拟内存：$(ps -aux| awk '$2~/^'$P'$/{print $5}')"
echo "进程共享内存：$(ps -aux| awk '$2~/^'$P'$/{print $6}')"
echo -e "\e[32m--------------------------------\e[0m"
```

{% asset_img  2.png%}显示该进程所有信息

**根据进程名显示该进程所有信息**
根据输入的程序的名字模糊过滤出所对应的 PID，并显示出详细信息，如果有多个PID，则全部显示

{% asset_img  3.png%}

```
#! /bin/bash

read -p "请输入要查询的进程名：" NAME

N=`ps -aux | grep $NAME | grep -v grep | wc -l` ##统计进程总数

if [ $N -le 0 ];then
  echo "该进程名没有运行！"
fi
i=1
while [ $N -gt 0 ]
do
  echo -e "\e[32m***************************************************************\e[0m"
  echo "进程PID: $(ps -aux | grep $NAME | grep -v grep | awk 'NR=='$i'{print $0}'| awk '{print $2}')"
  echo "进程命令：$(ps -aux | grep $NAME | grep -v grep | awk 'NR=='$i'{print $0}'| awk '{for (j=11;j<=NF;j++) printf("%s ",$j)}')"
  echo "进程所属用户: $(ps -aux | grep $NAME | grep -v grep | awk 'NR=='$i'{print $0}'| awk '{print $1}')"
  echo "CPU占用率：$(ps -aux | grep $NAME | grep -v grep | awk 'NR=='$i'{print $0}'| awk '{print $3}')%"
  echo "内存占用率：$(ps -aux | grep $NAME | grep -v grep | awk 'NR=='$i'{print $0}'| awk '{print $4}')%"
  echo "进程开始运行的时间：$(ps -aux | grep $NAME | grep -v grep | awk 'NR=='$i'{print $0}'| awk '{print $9}')"
  echo "进程运行的时间：$(ps -aux | grep $NAME | grep -v grep | awk 'NR=='$i'{print $0}'| awk '{print $10}')"
  echo "进程状态：$(ps -aux | grep $NAME | grep -v grep | awk 'NR=='$i'{print $0}'| awk '{print $8}')"
  echo "进程虚拟内存：$(ps -aux | grep $NAME | grep -v grep | awk 'NR=='$i'{print $0}'| awk '{print $5}')"
  echo "进程共享内存：$(ps -aux | grep $NAME | grep -v grep | awk 'NR=='$i'{print $0}'| awk '{print $6}')"
  echo -e "\e[32m***************************************************************\e[0m"
  let N-- i++
done
```

{% asset_img  4.png%}


**根据用户名查看该用户的相关信息**

{% asset_img  5.png%}

```
#! /bin/bash

read -p "请输入要查询的用户名：" name

echo "------------------------------"

n=`cat /etc/passwd | awk -F: '$1~/^'${name}'$/{print}' | wc -l`

if [ $n -eq 0 ];then
echo -e "\e[31m该用户不存在！\e[0m"
echo "------------------------------"
else
  echo "该用户的用户名：${name}"
  echo "该用户的UID：$(cat /etc/passwd | awk -F: '$1~/^'${name}'$/{print}'|awk -F: '{print $3}')"
  echo "该用户的组为：$(id ${name} | awk {'print $3'})"
  echo "该用户的GID为：$(cat /etc/passwd | awk -F: '$1~/^'${name}'$/{print}'|awk -F: '{print $4}')"
  echo "该用户的家目录为：$(cat /etc/passwd | awk -F: '$1~/^'${name}'$/{print}'|awk -F: '{print $6}')"
  Login=$(cat /etc/passwd | awk -F: '$1~/^'${name}'$/{print}'|awk -F: '{print $7}')
  if [ ${Login} == "/bin/bash" ];then
  echo -e "\e[32m该用户有登录系统的权限\e[0m"
  echo "------------------------------"
  elif [ ${Login} == "/sbin/nologin" ];then
  echo -e "\e[31m该用户没有登录系统的权限！\e[0m"
  echo "------------------------------"
  fi
fi
```

 

**查看 tcp 的连接状态**

 

{% asset_img  6.png%}

```
#! /bin/bash

#统计不同状态 tcp 连接（除了 LISTEN ）
all_status_tcp=$(netstat -nt | awk 'NR>2 {print $6}' | sort | uniq -c)

#打印各状态 tcp 连接以及连接数
all_tcp=$(netstat -na | awk '/^tcp/ {++S[$NF]};END {for(a in S) print a, S[a]}')


#统计有哪些 IP 地址连接到了本地 80 端口（ipv4）
connect_80_ip=$(netstat -ant| grep -v 'tcp6' | awk '/:80/{split($5,ip,":");++S[ip[1]]}END{for (a in S) print S[a],a}' |sort -n)


#输出前十个连接到了本地 80 端口的 IP 地址（ipv4）
top10_connect_80_ip=$(netstat -ant| grep -v 'tcp6' | awk '/:80/{split($5,ip,":");++S[ip[1]]}END{for (a in S) print S[a],a}' |sort -rn|head -n 10)


echo -e "\e[31m不同状态(除了LISTEN) tcp 连接及连接数为：\e[0m\n${all_status_tcp}"
echo -e "\e[31m各个状态 tcp 连接以及连接数为：\e[0m\n${all_tcp}"
echo -e "\e[31m连接到本地80端口的 IP 地址及连接数为：\e[0m\n${connect_80_ip}"
echo -e "\e[31m前十个连接到本地80端口的 IP 地址及连接数为：\e[0m\n${top10_connect_80_ip}"
```

 

**PS：下面例子里我检测的是 22 端口**

{% asset_img  7.png%}



**显示系统性能**

{% asset_img  8.png%}

```
#!/bin/bash

#物理内存使用量
mem_used=$(free -m | grep Mem | awk '{print$3}')

#物理内存总量
mem_total=$(free -m | grep Mem | awk '{print$2}')

#cpu核数
cpu_num=$(lscpu  | grep 'CPU(s)' | awk 'NR==1 {print$2}')

#平均负载
load_average=$(uptime  | awk -F : '{print$5}')

#用户态的CPU使用率
cpu_us=$(top -d 1 -n 1 | grep Cpu | awk -F',' '{print $1}' | awk '{print $(NF-1)}')

#内核态的CPU使用率
cpu_sys=$(top -d 1 -n 1 | grep Cpu | awk -F',' '{print $2}' | awk '{print $(NF-1)}')

#等待I/O的CPU使用率
cpu_wa=$(top -d 1 -n 1 | grep Cpu | awk -F',' '{print $5}' | awk '{print $(NF-1)}')

#处理硬中断的CPU使用率
cpu_hi=$(top -d 1 -n 1 | grep Cpu | awk -F',' '{print $6}' | awk '{print $(NF-1)}')

#处理软中断的CPU使用率
cpu_si=$(top -d 1 -n 1 | grep Cpu | awk -F',' '{print $7}'| awk '{print $(NF-1)}')

echo -e "物理内存使用量(M)为：${mem_used}"
echo -e "物理内存总量(M)为：${mem_total}"
echo -e "cpu核数为：${cpu_num}"
echo -e "平均负载为：${load_average}"
echo -e "用户态的CPU使用率为：${cpu_us}"
echo -e "内核态的CPU使用率为：${cpu_sys}"
echo -e "等待I/O的CPU使用率为：${cpu_wa}"
echo -e "处理硬中断的CPU使用率为：${cpu_hi}"
echo -e "处理软中断的CPU使用率为：${cpu_si}"
```

 

{% asset_img  9.png%}

 

**文件不安全的权限检查**

{% asset_img  10.png%}

```
#查找系统中任何用户都有写权限的文件（目录），并存放到/tmp/anynone_write.txt
find / -type f -perm -2 -o -perm -20 -exec echo {} >> /tmp/anynone_write.txt   \;

#查找系统中所有含 's' 位权限的程序，并存放到/tmp/s_permission.txt
find / -type f -perm -4000 -o -perm -2000 -print -exec echo {} >> /tmp/s_permission.txt  \;

#查找系统中没有属主以及属组的文件，并存放到/tmp/none.txt
find / -nouser -o -nogroup -exec echo {} >> /tmp/none.txt  \;
```
