												daocloud安装

```
安装docker
curl -sSL https://get.daocloud.io/docker | sh
安装compose
curl -L https://get.daocloud.io/docker/compose/releases/download/v2.12.2/docker-compose-`uname -s`-`uname -m` > /usr/local/bin/docker-compose
chmod +x /usr/local/bin/docker-compose
启动docker
sudo systemctl start docker
删除安装包
yum remove docker-ce
删除镜像，容器，配置文件
rm -rf /var/lib/docker
列出镜像列表
 docker images
各个选项说明:

REPOSITORY：表示镜像的仓库源

TAG：镜像的标签

IMAGE ID：镜像ID

CREATED：镜像创建时间

SIZE：镜像大小

删除镜像
docker rmi hello-world

```

