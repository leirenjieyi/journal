# Compose 文件结构和举例
```
version: "3"
services:

  redis:
    image: redis:alpine
    ports:
      - "6379"
    networks:
      - frontend
    deploy:
      replicas: 2
      update_config:
        parallelism: 2
        delay: 10s
      restart_policy:
        condition: on-failure

  db:
    image: postgres:9.4
    volumes:
      - db-data:/var/lib/postgresql/data
    networks:
      - backend
    deploy:
      placement:
        constraints: [node.role == manager]

  vote:
    image: dockersamples/examplevotingapp_vote:before
    ports:
      - 5000:80
    networks:
      - frontend
    depends_on:
      - redis
    deploy:
      replicas: 2
      update_config:
        parallelism: 2
      restart_policy:
        condition: on-failure

  result:
    image: dockersamples/examplevotingapp_result:before
    ports:
      - 5001:80
    networks:
      - backend
    depends_on:
      - db
    deploy:
      replicas: 1
      update_config:
        parallelism: 2
        delay: 10s
      restart_policy:
        condition: on-failure

  worker:
    image: dockersamples/examplevotingapp_worker
    networks:
      - frontend
      - backend
    deploy:
      mode: replicated
      replicas: 1
      labels: [APP=VOTING]
      restart_policy:
        condition: on-failure
        delay: 10s
        max_attempts: 3
        window: 120s
      placement:
        constraints: [node.role == manager]

  visualizer:
    image: dockersamples/visualizer:stable
    ports:
      - "8080:8080"
    stop_grace_period: 1m30s
    volumes:
      - "/var/run/docker.sock:/var/run/docker.sock"
    deploy:
      placement:
        constraints: [node.role == manager]

networks:
  frontend:
  backend:

volumes:
  db-data:
```

此参考页面上的主题是按顶级键按字母顺序组织的，以反映撰写文件本身的结构。在配置文件中定义一个节的顶级键，如build、deploy、deliveron、network等，列出了支持它们的选项作为子主题。这将映射到Compose文件的<key>：<Options>：<value>缩进结构。

Compose文件入门讲解：

> Your first docker-compose.yml File <https://docs.docker.com/get-started/part3/#your-first-docker-composeyml-file>

>Add a new service and redeploy  <https://docs.docker.com/get-started/part5/#add-a-new-service-and-redeploy>

>Docker for Beginners lab <https://github.com/docker/labs/tree/master/beginner/>

## 服务配置说明
Compose文件是YAML格式文件，定义了service,network,volume.默认路径是 ./docker-compose.yml

    Tip:Compose文件扩展名可以为yml或yaml,两者都可以。

service定义包含应用于为该服务启动的每个容器的配置，这与将命令行参数传递给 docker container create 非常相似。同样，network 和 volume 定义类似于 docker network create 和 docker volume create。

## build
在构建时应用的配置选项。

可以将build指定为包含生成上下文路径的字符串：
```
version: '3'
services:
  webapp:
    build: ./dir
```