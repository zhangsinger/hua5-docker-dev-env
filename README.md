hua5-docker-dev-env（Docker + Nginx + MySQL5,8 + PHP7,8 + Redis + Nacos + Sentinel + Seata + Zipkin + ElasticSearch + MongoDB + RabbitMQ）

Hua5-Docker-Dev-Env 项目：

1. 支持**多版本PHP**共存，可任意切换（PHP7.4、PHP8.0)
2. Nginx支持绑定**任意多个域名**
3. 支持**HTTPS和HTTP/2**
4. **运行数据、配置文件、日志文件**都可在宿主机Docker目录中直接修改查看
5. 可一键选配常用服务：
   - 多PHP版本：PHP7.4、PHP8.0
   - Web服务：Nginx
   - 数据库：MySQL5、MySQL8、Redis、memcached、MongoDB、ElasticSearch
   - 消息队列：RabbitMQ
   - Cloud组件：Nacos、Sentinel、Seata、Zipkin
   - 辅助工具：Kibana、Logstash
6. 所有镜像源于[Docker官方仓库](https://hub.docker.com)
7. 一次配置，**Windows、Linux、MacOs**皆可用
8. 支持安装certbot获取免费https用的SSL证书


## 1.目录结构

```
/
├── data                           数据库数据目录
│   ├── esdata                     ElasticSearch 数据目录
│   ├── mongo                      MongoDB 数据目录
│   ├── mysql                      MySQL8 数据目录
│   └── mysql5                     MySQL5 数据目录
│   └── redis                      Redis 数据目录
├── services                       服务构建文件和配置文件目录
│   ├── elasticsearch              ElasticSearch 配置文件目录
│   ├── mysql                      MySQL8 配置文件目录
│   ├── mysql5                     MySQL5 配置文件目录
│   ├── nacos                      Nacos 配置文件目录
│   ├── nginx                      Nginx 配置文件目录
│   ├── php7                       PHP7 配置目录
│   ├── php8                       PHP8 配置目录
│   ├── rabbitmq                   RabbitMQ 配置目录
│   └── redis                      Redis 配置目录
│   └── seata                      Seata 配置目录
│   └── supervisor                 Supervisor 配置目录
├── logs                           日志目录
├── docker-compose.sample.yml      Docker 服务配置示例文件
├── env.smaple                     环境参数配置示例文件
├── bash.alias.sample              快捷进入容器命令的环境变量示例文件
└── www                            默认的Web目录
```

## 2.快速使用

1. 本地安装

   - `git`
   - `Docker-Desktop（Windows推荐）`  https://www.docker.com/products/docker-desktop/
   - `OrbStack（Mac推荐）`  https://orbstack.dev/download

2. `clone`项目：

   ```bash
   $ git clone https://github.com/hua5/docker.git
   ```

3. 拷贝并命名配置文件（Windows系统请用`copy`命令），启动：

   ```bash
   # 在项目目录运行命令行
   $ cp env.sample .env                               # 复制环境变量文件
   $ cp docker-compose.sample.yml docker-compose.yml  # 复制 docker-compose 配置文件                               
   $ docker-compose up -d                             # 启动
   ```

4. 默认启动 5 个服务：MySQL8、Redis、Nacos、Sentinel、Seata。

5. 开启更多其他服务，如Nginx、PHP、MongoDB、RabbitMQ、ElasticSearch等，请删除 `docker-compose.yml `文件中服务块前的 # 注释。

6. 如果开启了Nginx和PHP服务，在浏览器中访问：`http://localhost:8080`就能看到效果，Web代码默认在文件`./www/localhost/index.php`，可在 .env 文件中设置为其他目录。

## 3.各项服务及端口

| 服务名称         | 访问端口                               | 默认用户密码        |
| ---------------- | -------------------------------------- | ------------------- |
| nginx、openresty | http://localhost:8080                  |                     |
| Php7、php8       | php:9000                               |                     |
| mysql、mysql5    | localhost:3306                         | root : root         |
| redis            | localhost:6379                         |                     |
| nacos            | http://localhost:8848/nacos            | nacos : nacos       |
| sentinel         | http://localhost:8858                  | sentinel : sentinel |
| seata            | localhost:8091                         |                     |
| zipkin           | http://localhost:9411/zipkin           |                     |
| memcached        | localhost:11211                        |                     |
| rabbitmq         | http://localhost:15672  localhost:5672 | rabbitmq : rabbitmq |
| mongodb          | localhost:27017                        | root : 123456       |
| elasticsearch    | localhost:9200  9300                   |                     |
| kibana           | http://localhost:5601                  |                     |
| logstash         | localhost:9600  5044                   |                     |
| node             | http://localhost:8081                  |                     |
| supervisor       | localhost:9001                         |                     |



## 3.PHP和扩展

### 3.1 切换Nginx使用的PHP版本

首先，需要启动其他版本的PHP，比如PHP8.0，那就先在`docker-compose.yml`文件中删除PHP8前面的注释，再启动PHP8容器。PHP8启动后，打开Nginx 配置，修改`fastcgi_pass`的主机地址，由`php7改为`php8，其中 `php7` 和 `php8` 是`docker-compose.yml`文件中服务器的名称。最后，**重启 Nginx** 生效。

```bash
重启 Nginx
$ docker exec -it nginx nginx -s reload
```

这里两个`nginx`，第一个是容器名，第二个是容器中的`nginx`程序。


### 3.2 安装PHP扩展

如果要安装更多扩展，请打开`.env`文件修改如下的PHP配置，可用的扩展请看同文件的`env.sample`注释块说明。
增加需要的PHP扩展：

```bash
PHP7_EXTENSIONS=pdo_mysql,opcache,redis       # PHP7 要安装的扩展列表，英文逗号隔开
PHP8_EXTENSIONS=opcache,redis                 # PHP8要安装的扩展列表，英文逗号隔开
```

然后重新build PHP镜像。

```bash
docker-compose build php
```

### 3.4 Host中使用php命令行（php-cli）

1. 参考[bash.alias.sample](bash.alias.sample)示例文件，将对应 php cli 函数拷贝到主机，Linux拷贝到 `~/.bashrc`，mac拷贝到 `~/.zshrc` 。

2. 让文件起效：

   ```bash
   source ~/.bashrc
   source ~/.zshrc
   ```

3. 然后就可以在主机中执行php命令了：

   ```bash
   ~ php -v
   ```

### 3.5 使用composer

**方法1：主机中使用composer命令**

1. 确定composer缓存的路径目录存在。比如，我的hua5-docker-dev-env下载在`~/hua5-docker-dev-env`目录，那composer的缓存路径就是`~/hua5-docker-dev-env/data/composer`。

2. 参考 [bash.alias.sample](bash.alias.sample) 示例文件，修改文件中函数的目录映射，将对应 php composer 函数拷贝到主机的 `~/.bashrc`文件。

3. 让文件起效：

   ```bash
   source ~/.bashrc
   ```

4. 在主机的任何目录下就能用composer了：

   ```bash
   cd ~/docker/www/
   composer -v
   ```

5. 第一次使用 composer 会在 `/data/composer` 目录下生成一个**config.json**文件，可以在这个文件中指定国内仓库，例如：

   ```json
   {
       "config": {},
       "repositories": {
           "packagist": {
               "type": "composer",
               "url": "https://mirrors.aliyun.com/composer/"
           }
       }
   }
   ```

**方法2：容器中使用composer命令**

进入容器，再执行`composer`命令，以PHP7容器为例：

```bash
docker exec -it php /bin/sh
cd /www/localhost
composer -v
```

## 4.管理命令

### 4.1 服务器启动和构建命令

如需管理服务，请在命令后面加上服务器名称，例如：

```bash
$ docker-compose up -d                      # 创建并且后台运行方式启动所有容器
$ docker-compose up -d nginx php  mysql     # 创建并且已后台运行的方式启动nginx、php、mysql容器

$ docker-compose start mysql                # 启动服务
$ docker-compose stop mysql                 # 停止服务
$ docker-compose restart mysql              # 重启服务
$ docker-compose build php                  # 构建或者重新构建服务

$ docker-compose rm mysql                   # 删除并且停止mysql容器
$ docker-compose down                       # 停止并删除容器，网络，图像和挂载卷

$ docker ps                                 # 查看所有运行中的容器
$ docker exec -it redis /bin/sh             # 进入容器
$ docker inspect mysql                      # 查看容器内网IP
```

### 4.2 可添加快捷进入容器的环境变量

在开发的时候，我们可能经常使用`docker exec -it`进入到容器中，把常用的做成命令别名是个省事的方法。

首先，在主机中查看可用的容器：

```bash
$ docker ps           # 查看所有运行中的容器
$ docker ps -a        # 所有容器
```

输出的`NAMES`那一列就是容器的名称，如果使用默认配置，那么名称就是`nginx`、`php`、`mysql`等。

然后，Windows为CMD设置别名，Linux打开`~/.bashrc`文件，Mac打开`~/.bash_profile`文件，加上：

```bash
alias dnginx='docker exec -it nginx /bin/sh'
alias dphp='docker exec -it php /bin/sh'
alias dmysql='docker exec -it mysql /bin/bash'
alias dredis='docker exec -it redis /bin/sh'
```

下次进入容器就非常快捷了，如进入php容器：

```bash
$ dphp
```

### 4.3 查看docker容器内网IP

```bash
$ docker inspect mysql
```

## 5.使用Log

Log文件生成的位置依赖于conf下各log配置的值。

## 6.MySql数据库连接

本地**Navicat**等工具连接：

- host：localhost
- port：`3306`
- username：root
- password：root

在其他容器内连接MySQL容器，直接用容器内网IP或者容器名称：

- host：mysql  / 容器内网IP
- port：`3306`
- username：root
- password：root

## 7.Redis连接

本地**RDM**等工具连接：

- host：localhost
- port：`6379`

在其他容器内连接Redis容器，直接用容器内网IP或者容器名称：

- host：redis / 容器内网IP
- port：`6379`

