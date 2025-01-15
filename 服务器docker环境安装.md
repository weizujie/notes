##### 关闭防火墙selinux
```sh
# 关闭防火墙
systemctl stop firewalld
systemctl disable firewalld

##### 每台机器配置时间同步
```sh
# 设置时区
vi /etc/profile

export TZ="Asia/Shanghai"

# 安装时间同步
yum install ntp -y

vi /etc/ntp.conf
# 阿里云时间同步
server ntp.aliyun.com
server ntp1.aliyun.com
server ntp2.aliyun.com
server ntp3.aliyun.com

# 启动ntpd
systemctl restart ntpd

# 设置开机启动
systemctl enable ntpd
# or
chkconfig ntpd on

```

##### 修改hostname
```sh
hostnamectl set-hostname crm-node04
```


##### 安装docker
```sh
# 配置yum源  删除该目录下所有repo文件（备份到backup目录）
cd /etc/yum.repos.d
mkdir -p backup_repo
mv *.repo backup_repo/
# 配置阿里云yum源
curl -o CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo
# or配置华为云yum源
curl -o /etc/yum.repos.d/CentOS-Base.repo https://repo.huaweicloud.com/repository/conf/CentOS-7-reg.repo

# 配置docker repo
curl -o docker-ce.repo https://download.docker.com/linux/centos/docker-ce.repo
# 刷新yum缓存 18.03.0-ce
yum clean all
yum makecache

# 更新yum
yum update -y

# 安装指定版本docker
yum install -y docker-ce-18.03.0.ce-1.el7.centos

# or安装最新docker
yum install -y docker-ce

# 启动docker并设置自启动
systemctl start docker
systemctl enable docker
```

##### Docker配置文件daemon.json：

1. 设置/etc/docker/daemon.json 文件.

```json
{
 "log-driver":"json-file",
 "log-opts":{
    "max-size" :"50m","max-file":"3"
  }
}
```



2. 创建并修改完daemon.json文件后，需要让这个文件生效

   a. 修改完成后reload配置文件： ```sudo systemctl daemon-reload```
   b.重启docker服务
   sudo systemctl restart docker.service
   c.查看状态
   sudo systemctl status docker -l
   d.查看服务
   sudo docker info


##### 安装rancher
docker run -d --restart=unless-stopped -p 18000:8080 rancher/server:v1.6.17


##### 卸载rancher
docker rm -f $(docker ps -qa)
docker rmi -f $(docker images -q)
docker volume rm $(docker volume ls -q)

rm -rf /etc/ceph \
       /etc/cni \
       /etc/kubernetes \
       /opt/cni \
       /opt/rke \
       /run/secrets/kubernetes.io \
       /run/calico \
       /run/flannel \
       /var/lib/calico \
       /var/lib/etcd \
       /var/lib/cni \
       /var/lib/kubelet \
       /var/lib/rancher/rke/log \
       /var/log/containers \
       /var/log/pods \
       /var/run/calico


##### 卸载docker
1.杀死docker有关的容器：
docker kill $(docker ps -a -q)
2.删除所有docker容器：
docker rm $(docker ps -a -q)
3.删除所有docker镜像：
docker rmi $(docker images -q)
4.停止 docker 服务:
systemctl stop docker
5.删除相关目录：
rm -rf /etc/docker
rm -rf /run/docker
rm -rf /var/lib/dockershim
rm -rf /var/lib/docker
6.如果删除不掉，则先umount：
umount /var/lib/docker/devicemapper
7.查看系统已经安装了哪些docker包
yum list installed | grep docker
卸载相关包：
yum remove {name}
不再出现相关信息，证明删除成功，再看看docker命令：-bash: /usr/bin/docker: No such file or directory则卸载成功