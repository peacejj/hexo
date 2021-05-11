---
title: ssh 免密码登录linux
date: 2019-07-08 17:56:40
tags: ssh
categories: 基础知识
excerpt: ssh配置

---

假设：从A机器免密码跳转到B机器上，双方用户都要求是eshop

一、登录到A机器
     进入目录/home/eshop，运行ssh-keygen -t rsa
     会生成 隐藏目录 .ssh , 在该目录下会有 id_rsa（私钥） 和 id_rsa.pub（公钥） 两个文件


二、B机器
    复制A机器生成的id_rsa.pub（公钥）到/home/eshop/.ssh/id_rsa.pub
    复制内容到 /home/eshop/.ssh/authorized_keys（如果没有就新建）中，命令：cat id_rsa.pub >> authorized_keys
    修改authorized_keys权限为600，命令：chmod 600 authorized_keys
    chmod 700 ~/.ssh/


三、A机器
    执行
    ssh B机器ip
    
注：有时还得输密码配置，情况各异。
可能情况参照：https://blog.csdn.net/u010180815/article/details/78323596
