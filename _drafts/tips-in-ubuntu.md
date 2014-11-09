---
layout: post
category : ubuntu
tagline: "Supporting tagline"
tags : [virtualbox, jetbrains]
---
{% include JB/setup %}

##virtualbox share files

在文件 /etc/rc.local 中（用root用户）追加如下命令。

	mount -t vboxsf share /mnt/share

##jetbrains desktop

在启动shell脚本开始处加入(after "Create Desktop Entry...")。

	export JDK_HOME='your java home folder'

##hot key

改成win键，不会与软件的快捷键冲突。