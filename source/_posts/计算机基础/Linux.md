# Linux

### 简单命令

```
   本地语言: locale
   修改 : export LC_ALL=en_US.utf8
   日历: cal  [month]  [year]
   计算器 : bc

```

Linux 文件的权限与目录配置

改变文件的属性和权限

chgrp :改变文件所属群组
chown :改变文件拥有者
chmod :改变文件的权限, SUID, SGID, SBIT 等等的特性

chomd a+x fileName : 给 user/group/others 权限都加上可执行

w:权限非常重要，小心赋予，因为可以删除 ， 如果一个 账号目录具有全部权限，它就能删除该目录下的所有文件（不管是谁创造的）

x:权限非常重要，如果没有目录没有的话，cd 不进去
