---
title: linux命令—老记混系列
date: 2020-11-24 09:59:11
excerpt: 常用的linux命令
tags: linux
position: 1
image_url:
---

```
查看当前进程的线程数量
# pstree -p 进程号 | wc -l

查看文件夹下文件数量
# ls | wc -w

查看磁盘占用：
# df -h

查看内存占用
# free -m

查看端口占用
# netstat -anp | grep 端口号

查看全部端口占用
# netstat -nultp

查看机器是固态还是机械
# lsblk -d -o name,rota
对于其返回值，看rota值来判断，如果rota为1，则意味旋转，则为机械盘，若rota为0则意味着发该盘为固态

查看硬盘信息
# fdisk -l
# hdparm -i /dev/sda1
# hdparm -i /dev/sdb1 |grep -i serialno
SerialNo=Z1Z0MKWS 硬盘序列号

vim中查询计数（例：查yes）
# :%s/yes//gn

查看某个进程的线程数
# pstree -p 进程id | wc -l

远程复制
# scp -r flume/ hadoop@hadoop03:$PWD

查看cpu个数
# lscpu
 
每个cpu的核数
#cat /proc/cpuinfo | grep "cpu cores" | uniq
 
查看内存总数
#cat /proc/meminfo | grep MemTotal

修改拥有者（chown）
# chown hadoop:hadoop rootfs

修改权限（chmod ）
  文字设定法
# chmod [who] [+ | - | =] [mode] 文件名
  eg: chmod g+r，o+r example
    who：
      u 表示“用户（user）”，即文件或目录的所有者。
      g 表示“同组（group）用户”，即与文件属主有相同组ID的所有用户。
      o 表示“其他（others）用户”。
      a 表示“所有（all）用户”。它是系统默认值。
    操作符：
      + 添加某个权限。
      - 取消某个权限。
      = 赋予给定权限并取消其他所有权限（如果有的话）。
    mode：
      r 可读。
      w 可写。
      x 可执行。
      X 只有目标文件对某些用户是可执行的或该目标文件是目录时才追加x 属性。
      s 在文件执行时把进程的属主或组ID置为该文件的文件属主。方式“u＋s”设置文件的用户ID位，“g＋s”设置组ID位。
      t 保存程序的文本到交换设备上。
      u 与文件属主拥有一样的权限。
      g 与和文件属主同组的用户拥有一样的权限。
      o 与其他用户拥有一样的权限。
  数字设定法
# chmod [mode] 文件名
0表示没有权限，1表示可执行权限，2表示可写权限，4表示可读权限，然后将其相加。


[viewer@ tomcat]$ ll
total 160
-rw-r----- 1 root root  7142 Dec 12  2018 RELEASE-NOTES
-rw-r----- 1 root root 16262 Dec 12  2018 RUNNING.txt
drwxr-x--- 2 root root  4096 Feb 27 13:34 temp
drwxr-x--- 4 root root  4096 Apr 19 13:36 webapps
drwxr-x--- 3 root root  4096 Feb 27 13:35 work

#清除cache
echo 1 > /proc/sys/vm/drop_caches:表示清除pagecache。
echo 2 > /proc/sys/vm/drop_caches:表示清除回收slab分配器中的对象（包括目录项缓存和inode缓存）。slab分配器是内核中管理内存的一种机制，其中很多缓存数据实现都是用的pagecache。
echo 3 > /proc/sys/vm/drop_caches:表示清除pagecache和slab分配器中的缓存对象。

从本地复制到远程
# 拷贝文件
scp /home/test/test.txt root@192.168.0.2:/home/test/
# 拷贝目录
scp -r /home/test/ root@192.168.0.2:/home/test/

从远程复制到本地
# 拷贝文件
scp root@192.168.0.2:/home/test/ /home/test/test.txt
# 拷贝目录
scp -r root@192.168.0.2:/home/test/ v/home/test/

# 压缩解压
压缩：
tar -zcvf /home/xahot.tar.gz /xahot
zip -r ./xahot.zip ./* -r


解压：
tar –xvf file.tar //解压 tar包
tar -xzvf file.tar.gz //解压tar.gz
tar -xjvf file.tar.bz2   //解压 tar.bz2
tar –xZvf file.tar.Z   //解压tar.Z
unrar e file.rar //解压rar
unzip file.zip //解压zip
```
