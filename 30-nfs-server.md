## [系统环境] 
CentOS release 6.7 (Final)

## 服务端配置
### 1. 安装nfs-utils和rpcbind

yum install -y nfs-utils rpcbind

### 2.设置开机启动服务
chkconfig nfs on

chkconfig rpcbind on

### 3.启动相关服务
service rpcbind start

service nfs start

### 4.创建共享目录
mkdir  /share

### 5.编辑/etc/exports文件添加如下内容
vim /etc/exports

/share client_ip(rw,no_root_squash,no_subtree_check)

客户端的指定方式：

指定ip地址的主机：192.168.0.100
指定子网中的所有主机：192.168.0.0/24 或 192.168.0.0/255.255.255.0
指定域名的主机：nfs.test.com
指定域中的所有主机：*.test.com
所有主机：*

选项说明:

ro：共享目录只读；
rw：共享目录可读可写；
all_squash：所有访问用户都映射为匿名用户或用户组；
no_all_squash（默认）：访问用户先与本机用户匹配，匹配失败后再映射为匿名用户或用户组；
root_squash（默认）：将来访的root用户映射为匿名用户或用户组；
no_root_squash：来访的root用户保持root帐号权限；
anonuid=<UID>：指定匿名访问用户的本地用户UID，默认为nfsnobody（65534）；
anongid=<GID>：指定匿名访问用户的本地用户组GID，默认为nfsnobody（65534）；
secure（默认）：限制客户端只能从小于1024的tcp/ip端口连接服务器；
insecure：允许客户端从大于1024的tcp/ip端口连接服务器；
sync：将数据同步写入内存缓冲区与磁盘中，效率低，但可以保证数据的一致性；
async：将数据先保存在内存缓冲区中，必要时才写入磁盘；
wdelay（默认）：检查是否有相关的写操作，如果有则将这些写操作一起执行，这样可以提高效率；
no_wdelay：若有写操作则立即执行，应与sync配合使用；
subtree_check（默认） ：若输出目录是一个子目录，则nfs服务器将检查其父目录的权限；
no_subtree_check ：即使输出目录是一个子目录，nfs服务器也不检查其父目录的权限，这样可以提高效率；

### 6.刷新配置立即生效
exportfs -a

## 客户端配置
### 1. 安装nfs-utils和rpcbind
yum install nfs-utils rpcbind

### 2.设置开机启动服务
chkconfig nfs on
chkconfig rpcbind on

### 3.启动服务
service rpcbind start
service nfs start

### 4.创建挂载点
mkdir -p /mnt/share

### 5.挂载目录
mount -t nfs server_ip:/share /mnt/share

### 6.查看挂载的目录
df -h

### 7.卸载挂载的目录
umount /mnt/share

### 8.编辑/etc/fstab，开机自动挂载
vim /etc/fstab

# 在结尾添加如下一行
server_ip:/share /mnt/share nfs rw,tcp,intr 0 1
