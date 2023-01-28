# PagerMaid 安装指南

采用 Docker Compose 安装，有封号危险

```shell
sudo curl -L "https://github.com/docker/compose/releases/download/v2.2.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
sudo ln -s /usr/local/bin/docker-compose /usr/bin/docker-compose
docker-compose --version
```

```shell
#拉取代码
git clone https://gitlab.com/Xtao-Labs/pagermaid-modify
 
cd pagermaid-modify
 
cp docker-compose.gen.yml docker-compose.yml
 
#安装
docker-compose up -d
 
#配置
docker exec -it pagermaid bash utils/docker-config.sh
```

