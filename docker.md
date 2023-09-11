## Docker

### 容器与容器/宿主机之间互联
```
在容器内连接物理机使用 host.docker.internal 代替 127.0.0.1

```


### docker-compose.yml 例子
```yml
version: '3.1'
services:
    redis:
        image: 'redis:7.2.1'
        volumes:
            - '.:/application' #这是当前目录在容器linux内的目录，下面的配置文件替换同理#
            - './phpdocker/redis/redis.conf:/etc/redis/conf.d/redis.conf'
        ports:
            - '6379:6379'

    elastic:
        image: 'elasticsearch:8.6.1'
        container_name: elasticsearch
        volumes:
            - '.:/application'
            - './phpdocker/elasticsearch/elasticsearch.yml:/usr/share/elasticsearch/config/elasticsearch.yml'
            - './data:/usr/share/elasticsearch/data'
        ports:
            - '9200:9200'
            - '9300:9300'

    mysql:
        image: 'mysql:8.0'
        working_dir: /application
        volumes:
            - '.:/application'
        environment:
            - MYSQL_ROOT_PASSWORD=12345678
            - MYSQL_DATABASE=dophp
            - MYSQL_USER=123456
            - MYSQL_PASSWORD=123456
        ports:
            - '3368:3306'

    webserver:
        image: 'nginx:alpine'
        working_dir: /application
        volumes:
            - '.:/application'
            - './phpdocker/nginx/nginx.conf:/etc/nginx/conf.d/default.conf'
        ports:
            - '3366:80'

    php-fpm:
        build: phpdocker/php-fpm
        working_dir: /application
        volumes:
            - '.:/application'
            - './phpdocker/php-fpm/php-ini-overrides.ini:/etc/php/8.2/fpm/conf.d/99-overrides.ini'
```

### Docker使用elasticsearch的一些坑
es有最低内存需求，需要修改宿主机，比如windows wsl子系统的配置文件sysctl.conf，而不是容器内的linux
es配置例子:
```
# 集群节点名称
node.name: "node-1"
# 网络访问限制
network.host: 0.0.0.0
# 以单一节点模式启动
discovery.type: single-node

# 设置集群名称为elasticsearch
#cluster.name: "docker-es-cluster"
# 单节点时不能设置
#cluster.initial_master_nodes: ["node-1"]

# 是否支持跨域
http.cors.enabled: true
# 表示支持所有域名
http.cors.allow-origin: "*"
# 内存交换的选项，官网建议为true
bootstrap.memory_lock: true

# 无账号密码
xpack.security.enabled: false
```

