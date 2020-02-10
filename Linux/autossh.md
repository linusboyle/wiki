# autossh

autossh会自动维持ssh连接。要开始一个非交互式的，仅做转发的ssh连接，可以用下面的命令：

>  autossh -M 0 -f -T -N proxy

-M禁止autossh进行连接测试
-f使得autossh转为后台
其余选项为ssh的选项