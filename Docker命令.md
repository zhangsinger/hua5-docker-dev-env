## Docker 常用命令

~~~shell
docker-compose up -d                      # 创建并且后台运行方式启动所有容器
docker-compose up -d hua5-demo filebeat logstash kibana elastic-slave2 sentinel nginx php  mysql     # 创建并且已后台运行的方式启动nginx、php、mysql容器

docker-compose start mysql                # 启动服务
docker-compose stop mysql                 # 停止服务
docker-compose restart mysql              # 重启服务
docker-compose build php                  # 构建或者重新构建服务

docker-compose rm mysql                   # 删除并且停止mysql容器
docker-compose down                       # 停止并删除容器，网络，图像和挂载卷

docker ps                                 # 查看所有运行中的容器
docker exec -it redis /bin/sh             # 进入容器
docker inspect mysql                      # 查看容器内网IP

docker build -t  hua5-demo:1.0 .          # 打包镜像 
~~~

