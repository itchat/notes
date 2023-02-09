# 数据库环境搭建测试

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

## MySQL

[官方镜像链接](https://hub.docker.com/_/mysql)

```shell
docker run -itd --name mysql_inner -p 3306:3306 -e MYSQL_ROOT_PASSWORD=123456 mysql:5.7
```

安装好后默认账号为 `root` 密码为 `123456`

### 增量同步开启

登录测试设备 nano 一个 my.cnf

```cnf
# For advice on how to change settings please see
# http://dev.mysql.com/doc/refman/5.7/en/server-configuration-defaults.html

[mysqld]
#
# Remove leading # and set to the amount of RAM for the most important data
# cache in MySQL. Start at 70% of total RAM for dedicated server, else 10%.
# innodb_buffer_pool_size = 128M
#
# Remove leading # to turn on a very important data integrity option: logging
# changes to the binary log between backups.
# log_bin
#
# Remove leading # to set options mainly useful for reporting servers.
# The server defaults are faster for transactions and fast SELECTs.
# Adjust sizes as needed, experiment to find the optimal values.
# join_buffer_size = 128M
# sort_buffer_size = 2M
# read_rnd_buffer_size = 2M
skip-host-cache
skip-name-resolve
datadir=/var/lib/mysql
socket=/var/run/mysqld/mysqld.sock
secure-file-priv=/var/lib/mysql-files
user=mysql
log-bin=mysql-bin
binlog-format=ROW
server_id=1

# Disabling symbolic-links is recommended to prevent assorted security risks
symbolic-links=0

#log-error=/var/log/mysqld.log
pid-file=/var/run/mysqld/mysqld.pid
[client]
socket=/var/run/mysqld/mysqld.sock

!includedir /etc/mysql/conf.d/
!includedir /etc/mysql/mysql.conf.d/
```

或直接在原有 cnf 目录下添加以下三行代码即可

```cnf
log-bin=mysql-bin
binlog-format=ROW
server_id=1
```

```shell
docker cp my.cnf mysql_inner:/etc/my.cnf
docker restart mysql_inner
```

### MySQL 生成批量 Tables 与随机数据

```python
import pymysql
import random
import string 

# Connect to the database
connection = pymysql.connect(
    host='192.168.11.192',
    port=3306,
    user='root',
    password='123456',
    db='inner'
)

# Create a cursor object
cursor = connection.cursor()

# Create 1000 tables
for i in range(1000):
    table_name = "table" + str(i)
    sql = "CREATE TABLE {} (id INT PRIMARY KEY, data VARCHAR(255))".format(table_name)
    cursor.execute(sql)
    connection.commit()
    print("Table {} created successfully.".format(table_name))

# Insert random data into each table
for i in range(1000):
    table_name = "table" + str(i)
    for j in range(100):
        data = ''.join(random.choices(string.ascii_letters + string.digits, k=255))
        sql = "INSERT INTO {} (id, data) VALUES ({}, '{}')".format(table_name, j, data)
        cursor.execute(sql)
        connection.commit()
    print("Data inserted into {} successfully.".format(table_name))

# Close the cursor and connection
cursor.close()
connection.close()
```

## Oracle

由于 oracle 数据库从 9 版本后不再提供官方镜像并且开始收费，直接使用个人镜像

```shell
docker search oracle11g
docker run -h "oracle" --name "oracle_outer" -d -p 1521:1521 deepdiver/docker-oracle-xe-11g
```

Oracle 过一周左右默认密码就会过期，用 docker exec 登录上去修改密码即可，不要用 Navicat 修改。默认账户密码为 system 与 oracle

### DCN 开启增量功能

```sql
grant change notification to system
```

采用 DCN 注册订阅，监听增删改查事件得到 ROWID 根据 ROWID 执行 SQL 查询得到变化数据。因此需要在目标端配置一个 varchar 类型大小至少在 18 个字节以上的字段进行 ROW_ID 拷贝

## SQL Server

```shell
docker run -e "ACCEPT_EULA=Y" -e "MSSQL_SA_PASSWORD=defence*2018" \
   -p 1433:1433 --name sqlserver_outer \
   -d \
   mcr.microsoft.com/mssql/server:2019-latest
```

[微软官方部署教程](https://learn.microsoft.com/zh-cn/sql/linux/quickstart-install-connect-docker?view=sql-server-linux-ver15&preserve-view=true&pivots=cs1-bash)

### 增量同步

首先开启 Server Agent 功能

```shell
docker exec -u 0 -it sqlserver_outer /bin/bash
/opt/mssql/bin/mssql-conf set sqlagent.enabled true
docker restart sqlserver_inner
```

针对源数据库表开启 CDC 同步功能

```sql
USE test123
GO
EXECUTE sys.sp_cdc_enable_db;
GO
EXEC sys.sp_cdc_enable_table  
@source_schema = N'dbo',  
@source_name   = N'users',  
@role_name     = NULL,  
@supports_net_changes = 1  
GO 
```

如果要关闭 CDC 

```sql
USE mydb  
GO  
EXEC sys.sp_cdc_disable_db  
GO  
EXEC sys.sp_cdc_disable_table  
@source_schema = N'dbo',  
@source_name   = N'MyTable',  
@capture_instance = N'dbo_MyTable'  
GO 
```

```sql
-- SQL Server 表结构查看
exec sp_help 'dbo.users'
select is_cdc_enabled from sys.databases where name='test123'

-- 创建用户名密码
create login sqlserver with password='defence*2018'
use test123;
create user sqlserver for login sqlserver

-- 创建自定义架构
create schema sqltest
grant alter on schema::sqltest to sqlserver
exec sp_droprolemember 'db_denydatareader',sqlserver
exec sp_droprolemember 'db_denydatawriter',sqlserver

-- 数据库权限
select name, SUSER_NAME(owner_sid) as db from master.sys.DATABASEs
```

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

### 增量功能开启

通过复制流技术监听事件

```shell
docker cp postgres_outer:/var/lib/postgresql/data/pgdata/postgresql.conf .
```

首先从 docker container 中复制相关配置文件到主机，然后 nano 编辑，cltr + w 键寻找 `wal_level` 字符将其修改成 `logical` 然后传回 container 后重启

```shell
docker cp postgresql.conf postgres_outer:/var/lib/postgresql/data/pgdata/postgresql.conf
docker restart postgres_outer
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