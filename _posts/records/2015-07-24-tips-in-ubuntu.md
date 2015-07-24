---
layout: post
category : records
tagline: "Supporting tagline"
tags : [virtualbox, jetbrains]
---
{% include JB/setup %}

##virtualbox share files

在文件 /etc/rc.local 中（用root用户）追加如下命令。

	mount -t vboxsf -o uid=1000,gid=1000 share /mnt/share # first mkdir /mnt/share, 1000来自于id -u

##virtualbox hot key

改成win键，不会与软件的快捷键冲突。

##jetbrains desktop

在启动shell脚本开始处加入(after "Create Desktop Entry...")。

	export JDK_HOME='your java home folder'

##Problem: Waiting for headers when installing

删除/var/cache/apt/archives/partial文件夹，重新安装
