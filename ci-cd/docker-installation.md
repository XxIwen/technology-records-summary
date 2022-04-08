## 1. [Redhat7安装docker](https://cloud.tencent.com/developer/article/1667149)
- 下载`libcgroup-0.41-21.el7.x86_64.rpm`和`docker-engine-1.7.1-1.el6.x86_64.rpm`两个rpm包
```sh
address: https://get.docker.com/rpm/1.7.1/centos-6/RPMS/x86_64/docker-engine-1.7.1-1.el6.x86_64.rpm
```
- 安装
```sh
rpm -ivh libcgroup-0.41-21.el7.x86_64.rpm

rpm -ivh docker-engine-1.7.1-1.el6.x86_64.rpm
```
- 启动
```sh
service docker start
```
- 测试是否安装成功
```sh
docker version
docker info
docker pull hello-world
docker run --name mycontainer hello-world
```