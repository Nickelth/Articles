最近发现docker容器内无法连网，但是主机可以正常连接。

尝试重启docker服务，ssh直接断了。

首先考虑是不是docker和主机ip或者路由的网段冲突了，使用docker network ls查看docker的网络配置，使用docker network inspect查看具体的配置内容。然后使用ip a查看主机ip，使用ip route查看主机路由表。

一圈看下来，没有看到冲突的内容。

我们的主机网络是用netplan配置的，打开netplan配置文件，里面也只设置了一个网卡的配置，没有任何和docker相关的内容。

那么可能只是docker这边的问题了。直接删除/var/lib/docker/network，然后重启docker，这样docker会重新生成网络配置。

这样就可以连接了。进一步排查发现，有几个docker容器自动生成了自定义网络配置，所以可以用docker network rm删除这些网络配置。可能是因为这些配置导致了网络冲突。

这一次排查几乎只使用了chatgpt，像这样冷门的问题几乎找不到网上的信息，但是chatgpt却能够很快给出排查和解决的思路。