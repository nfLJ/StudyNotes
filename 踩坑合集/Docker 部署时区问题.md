## Docker 部署时区问题

### 发现问题

在使用 Docker 部署若依项目时，启动镜像后，发现日志的的时间不对，相差了8个小时

### 解决过程

#### 一、开始怀疑是数据库时区问题

我在本机连接远程数据库运行应用，发现本机时间正确，远程应用时间相差8个小时

![](https://gitee.com/nfLJ/Pic/raw/master/blog/20210126151329.png)

![](https://gitee.com/nfLJ/Pic/raw/master/blog/20210126151348.png)

于是查询远程数据库时区是否正确

```
/**
 * 可查看全局的时区设置和会话的时区设置
 * global.time_zone负责java连接数据库时的时区设置
 * session.time_zone负责Navicat客户端连接数据库时的时区设置
 * /
mysql> select @@global.time_zone,@@session.time_zone;

/**
 * 还有一种方法
 * System代表采用系统时区
 * CST是一种很乱的时区，它包括了4个时区
 * /
mysql> show variables like '%time_zone%'; 

// 验证时区，这行命令可以检测session.time_zone是否正确配置
mysql> select now();
```

我使用 `select now()` 显示时间是正确的，使用 `date` 也是 `CST`，证明数据库时区是没有问题的

#### 二、找到真相，容器时区问题

##### 1.首先查询服务器时间是否正确

```bash
date
```

发现系统时间正确，时区为 `CST`

##### 2.查询容器时间是否正确

进入容器

```bash
docker exec -it 容器名字 /bin/bash
```

执行命令：

```bash
date
```

发现容器内时间不对，相差8小时，且时区为`UTC`

##### 3.修改容器时区，保证和系统时区相同

删除原有镜像，重新制作。这次我们需要挂载 Docker 的 `/etc/localtime `到系统的 `/etc/localtime` 

在 Dockerfile 中新增命令：

```dockerfile
RUN /bin/cp /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
```

##### 4.重新制作并启动发现时间还是相差8个小时

##### 5.网上查阅资料发现可能是 JVM 时区不对

JVM是通过` /etc/timezone `文件获取时区的，需要在容器中映射或写入时区文件。

在 Dockerfile 中新增命令：

```dockerfile
RUN echo "Asia/Shanghai" > /etc/timezone
```

##### 6.再次制作启动发现时间正确

完整 Dockerfile：

```dockerfile
FROM java:8

VOLUME /jiang

RUN /bin/cp /usr/share/zoneinfo/Asia/Shanghai /etc/localtime

RUN echo "Asia/Shanghai" > /etc/timezone

ADD nflj-admin.jar app.jar

EXPOSE 7777

ENTRYPOINT ["java","-jar","app.jar"]
```

