# 常见数据库环境 Docker 化

## Docker

首先安装并加速 Docker 原始镜像，编辑 docker.sh 一件执行

```shell
sudo yum install -y yum-utils device-mapper-persistent-data lvm2
sudo yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
sudo yum makecache fast
sudo yum -y install docker-ce
sudo mkdir -p /etc/docker
sudo tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://registry.docker-cn.com"]
}
EOF
sudo systemctl daemon-reload
sudo systemctl restart docker
```

## MYSQL

[官方镜像链接](https://hub.docker.com/_/mysql)

```shell
docker run -itd --name mysql_inner -p 3306:3306 -e MYSQL_ROOT_PASSWORD=123456 mysql:5.7
```

安装好后默认账号为 `root` 密码为 `123456`

## Oracle

由于 oracle 数据库从 9 版本后不再提供官方镜像并且开始收费，直接使用个人镜像

```shell
docker search oracle11g
docker run -h "oracle" --name "oracle_outer" -d -p 1521:1521 deepdiver/docker-oracle-xe-11g
```

## SQL Server

```shell
docker run -e "ACCEPT_EULA=Y" -e "MSSQL_SA_PASSWORD=defence*2018" \
   -p 1433:1433 --name sqlserver_outer \
   -d \
   mcr.microsoft.com/mssql/server:2019-latest
```

[微软官方部署教程](https://learn.microsoft.com/zh-cn/sql/linux/quickstart-install-connect-docker?view=sql-server-linux-ver15&preserve-view=true&pivots=cs1-bash)

## PostgreSQL

```shell
docker run -d --name postgres_inner -p 5432:5432 -e POSTGRES_PASSWORD=123456 -e PGDATA=/var/lib/postgresql/data/pgdata -v /custom/mount:/var/lib/postgresql/data postgres:11
```

postgre 的密码有时可能是本身的限时时效性导致的，需要进到 docker 内部进行密码更改

```shell
su postgres
psql
ALTER USER postgres PASSWORD '123456';
```

## ElasticSearch

由于 ES 数据库以及其配套管理工具 kibana 的跨域等问题，需要创建一个虚拟网络

```shell
docker network create somenetwork
docker run -d --name elasticsearch --net somenetwork -p 9200:9200 -p 9300:9300 -e "discovery.type=single-node" elasticsearch:7.17.8
```

装好了之后访问 9200 端口发现是直接不需要任何密码的，以下设置数据库密码

```shell
docker exec -it elasticsearch /bin/bash
vim config/elasticsearch.yml
```

把下面的内容替换上去

```yml
cluster.name: "docker-cluster"
network.host: 0.0.0.0
http.cors.enabled: true
http.cors.allow-origin: "*"
xpack.security.enabled: true
```

然后退出 docker 镜像，重启

```shell
docker restart elasticsearch
docker exec -it elasticsearch /bin/bash
```

设置密码

```shell
bin/elasticsearch-setup-passwords interactive
docker restart elasticsearch
```

### Kibana

配置 ES 数据库管理工具

```shell
docker run -d --name kibana --net somenetwork -p 5601:5601 kibana:7.17.8
```

根目录建立 kibana.yml 然后配置如下：

```shell
server.host: "0.0.0.0"
server.shutdownTimeout: "5s"
elasticsearch.hosts: [ "http://192.168.11.192:9200" ]
monitoring.ui.container.elasticsearch.enabled: true
elasticsearch.username: "elastic"
elasticsearch.password: "123456"
```

由于官方的 docker 内部没装编辑器，直接复制

```shell
docker cp kibana.yml kibana:/usr/share/kibana/config
```

然后重启，有可能会出现启动错误等现象，需要看日志，或者先把 ES 配置好后再试一次

```shell
docker restart kibana
docker logs -f -t kibana
```