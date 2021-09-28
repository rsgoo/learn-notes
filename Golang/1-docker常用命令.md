##### 1: 匿名挂载

```dockerfile
# 容器卷默认挂载到宿主机的 /var/lib/docker/volumes/ 下
# /usr/local/data 是容器中目录，与/var/lib/docker/volumes/container_id 是映射的
docker run -id -v /usr/local/data --name ubuntu_01 ubuntu
```

> 也可以通过 `docker inspect`  查看

```dockerfile
docker inspect 3f39c889957ad905b36d16b2fed6774be58a3da64716050adfc562425d531ede
docker inspect container_name
docker inspect container_id
```

##### 2: docker具名挂载

```dockerfile
docker run -id -v host_path:container_path --name ubuntu_02 ubuntu

# example
docker run -id -v ubuntu02_data:/docker_data --name ubuntu_02 ubuntu
```

##### 3: 容器卷的读写 | 只读

- 只读：只能通过修改 **宿主机** 内容实现对容器数据的管理

  > docker run -id -v /宿主机目录:/容器目录:ro 镜像名称

- 读写：宿主机和容器可以双向操作数据（默认）

  > docker run -id -v  /宿主机目录:/容器目录:rw 镜像名称

##### 4: 数据卷继承

docker run -id --volumes-form 



##### 5: 私有镜像仓库搭建

- 修改 `/etc/docker/daemon.json`  如下

  ```json
  {
      "registry-mirrors":[
          "https://reg-mirror.qiniu.com/",
          "https://docker.mirrors.ustc.edu.cn/"
      ],
      //192.168.142.132 为宿主机ip
      "insecure-registries":["192.168.142.132:5000"]
  }
  ```

- 浏览器打开 `http://192.168.142.132:5000/v2/_catalog`

- 下载 `registry` 镜像，并运行

  ```shell
  # 下载镜像
  docker pull registry
  
  # 运行镜像
  docker run -id --name my_registry -p 5000:5000 -v /home/ins/docker_data/docker_registry:/var/lib/registry registry
  ```

- 给镜像打标签并推送到私有仓库

  ```shell
  # 给镜像打标签
  docker tag nginx:latest  192.168.142.132:5000/private_nginx:latest
  
  # 镜像推送到私有仓库
  docker push  192.168.142.132:5000/private_nginx:latest
  ```

- 再次访问 `http://192.168.142.132:5000/v2/_catalog` 验证推送的私有化镜像

  ```json
  {"repositories":["private_nginx"]}
  ```



##### 6：自定义网络

> docker network create custom_network（network_nmae）

> docker run -it --name bbox06 --net custom_network  busybox



##### 7: Docker 搭建Redis cluster 集群

1. 在两台机器 `192.168.142.n` 和 `192.168.142.n+1` 分别执行以下操作

   ```shell
   # 创建目录
   mkdir -p /usr/local/docker-redis/redis-cluster
   # 切换到指定目录
   cd	/usr/local/docker-redis/redis-cluster
   # 编写 redis-cluster.tmpl 文件
   vi redis-cluster.tmpl
   ```

   【192.168.142.132 机器的 `/usr/local/docker-redis/redis-cluster ` 目录结构如下】

   ```tmpl
   .
   ├── 6371
   │   ├── conf
   │   │   └── redis.conf
   │   └── data
   ├── 6372
   │   ├── conf
   │   │   └── redis.conf
   │   └── data
   ├── 6373
   │   ├── conf
   │   │   └── redis.conf
   │   └── data
   └── redis-cluster.tmpl
   ```

   - 各目录redis.conf文件如下cat 637{1..3}/conf/redis.conf

   ```conf
   root@ins:/usr/local/docker-redis/redis-cluster# cat 6371/conf/redis.conf 
   port 6371
   requirepass 1234
   masterauth  1234
   protected-mode no
   daemonize no
   appendonly yes
   cluster-enabled yes
   cluster-config-file nodes.conf
   cluster-node-timeout 15000
   cluster-anounce-ip 192.168.142.132
   cluster-anounce-port 6371
   cluster-anounce-bus-port 16371
   
   root@ins:/usr/local/docker-redis/redis-cluster# cat 6372/conf/redis.conf 
   port 6372
   requirepass 1234
   masterauth  1234
   protected-mode no
   daemonize no
   appendonly yes
   cluster-enabled yes
   cluster-config-file nodes.conf
   cluster-node-timeout 15000
   cluster-anounce-ip 192.168.142.132
   cluster-anounce-port 6372
   cluster-anounce-bus-port 16372
   
   root@ins:/usr/local/docker-redis/redis-cluster# cat 6373/conf/redis.conf 
   port 6373
   requirepass 1234
   masterauth  1234
   protected-mode no
   daemonize no
   appendonly yes
   cluster-enabled yes
   cluster-config-file nodes.conf
   cluster-node-timeout 15000
   cluster-anounce-ip 192.168.142.132
   cluster-anounce-port 6373
   cluster-anounce-bus-port 16373
   ```

   

   【192.168.142.132 机器的 `/usr/local/docker-redis/redis-cluster ` 目录结构如下】

   ```tmpl
   .
   ├── 6374
   │   ├── conf
   │   │   └── redis.conf
   │   └── data
   ├── 6375
   │   ├── conf
   │   │   └── redis.conf
   │   └── data
   ├── 6376
   │   ├── conf
   │   │   └── redis.conf
   │   └── data
   └── redis-cluster.tmpl
   ```

   - 各目录redis.conf文件如下 【cat 637{4..6}/conf/redis.conf】

   ```
   root@ins:/usr/local/docker-redis/redis-cluster# cat 6374/conf/redis.conf 
   port 6374
   requirepass 1234
   masterauth  1234
   protected-mode no
   daemonize no
   appendonly yes
   cluster-enabled yes
   cluster-config-file nodes.conf
   cluster-node-timeout 15000
   cluster-anounce-ip 192.168.142.133
   cluster-anounce-port 6374
   cluster-anounce-bus-port 16374
   
   root@ins:/usr/local/docker-redis/redis-cluster# cat 6375/conf/redis.conf 
   port 6375
   requirepass 1234
   masterauth  1234
   protected-mode no
   daemonize no
   appendonly yes
   cluster-enabled yes
   cluster-config-file nodes.conf
   cluster-node-timeout 15000
   cluster-anounce-ip 192.168.142.133
   cluster-anounce-port 6375
   cluster-anounce-bus-port 16375
   
   root@ins:/usr/local/docker-redis/redis-cluster# cat 6376/conf/redis.conf 
   port 6376
   requirepass 1234
   masterauth  1234
   protected-mode no
   daemonize no
   appendonly yes
   cluster-enabled yes
   cluster-config-file nodes.conf
   cluster-node-timeout 15000
   cluster-anounce-ip 192.168.142.133
   cluster-anounce-port 6376
   cluster-anounce-bus-port 16376
   ```

   3: 启动redis 【192.168.142.132机器】

   ```shell
   for port in $(seq 6371 6373); do \
   	docker run -di --restart always --name redis-${port} --net host \
   	-v /usr/local/docker-redis/redis-cluster/${port}/conf/redis.conf:/usr/local/etc/redis/redis.conf \
   	-v /usr/local/docker-redis/redis-cluster/${port}/data:/data \
   	redis redis-server /usr/local/etc/redis/redis.conf; \
   done
   
   ------------
   for port in $(seq 6374 6376); do \
   	docker run -di --restart always --name redis-${port} --net host \
   	-v /usr/local/docker-redis/redis-cluster/${port}/conf/redis.conf:/usr/local/etc/redis/redis.conf \
   	-v /usr/local/docker-redis/redis-cluster/${port}/data:/data \
   	redis redis-server /usr/local/etc/redis/redis.conf; \
   done
   
   # 不使用restart
   for port in $(seq 6371 6373); do \
   	docker run -di --name redis-${port} --net host \
   	-v /usr/local/docker-redis/redis-cluster/${port}/conf/redis.conf:/usr/local/etc/redis/redis.conf \
   	-v /usr/local/docker-redis/redis-cluster/${port}/data:/data \
   	redis redis-server /usr/local/etc/redis/redis.conf; \
   done
   
   
   # 6371启动
   docker run -idt --name redis-6371 --net host -v /usr/local/docker-redis/redis-cluster/6371/conf/redis.conf:/usr/local/etc/redis/redis.conf -v /usr/local/docker-redis/redis-cluster/6371/data:/data redis:latest redis-server /usr/local/etc/redis/redis.conf
   
   # 6372启动
   docker run -id --name redis-6372 --net host -v /usr/local/docker-redis/redis-cluster/6372/conf/redis.conf:/usr/local/etc/redis/redis.conf -v /usr/local/docker-redis/redis-cluster/6372/data:/data redis redis-server /usr/local/etc/redis/redis.conf
   
   # 6373启动
   docker run -id --name redis-6373 --net host -v /usr/local/docker-redis/redis-cluster/6373/conf/redis.conf:/usr/local/etc/redis/redis.conf -v /usr/local/docker-redis/redis-cluster/6373/data:/data redis redis-server /usr/local/etc/redis/redis.conf
   
   docker start redis-6371 redis-6372 redis-6373
   ```
   
   
   
   4: 启动redis 【192.168.142.133机器】
   
   ```shell
   for port in $(seq 6374 6376); do \
   	docker run -di --restart always --name redis-${port} --net host \
   	-v /usr/local/docker-redis/redis-cluster/${port}/conf/redis.conf:/usr/local/etc/redis/redis.conf \
   	-v /usr/local/docker-redis/redis-cluster/${port}/data:/data \
   	redis redis-server /usr/local/etc/redis/redis.conf; \
   done
   
   # 不使用restart
   for port in $(seq 6374 6376); do \
   	docker run -di --name redis-${port} --net host \
   	-v /usr/local/docker-redis/redis-cluster/${port}/conf/redis.conf:/usr/local/etc/redis/redis.conf \
   	-v /usr/local/docker-redis/redis-cluster/${port}/data:/data \
   	redis redis-server /usr/local/etc/redis/redis.conf; \
   done
   
   docker start redis-6374 redis-6375 redis-6376
   ```
   
   5: 进入到redis-6371容器里
   
   > docker exec -it redis-6371 bash
   
   6：通过如下命令创建redis-cluster
   
   ```
   redis-cli -a 1234 --cluster create 192.168.142.132:6371  192.168.142.132:6372  192.168.142.132:6373 192.168.142.133:6374 192.168.142.133:6375 192.168.142.133:6376 --cluster-replicas 1
   ```
   
   

