### 阿里云使用Docker安装MySQL 8.0.20

#### 下拉镜像

```bash
docker pull msyql:8.0.20
```

#### 启动镜像

```bash
docker run -p 3306:3306 --name mysql -e MYSQL_ROOT_PASSWORD=123456 -d mysql:8.0.20
```

#### 查看是否启动成功

```bash
docker ps -a
```

启动成功后，进入容器内部拷贝配置文件到宿主机

```bash
docker cp mysql:/etc/mysql /home/mysql8.0.20
```

拷贝容器的 `/etc/mysql`目录到主机目录`/home/mysq8.0.20`

#### 删除mysql容器

```bash
docker stop mysql  # 停止容器
docker rm mysql    # 删除容器
```

#### 重新启动容器并挂载配置文件，持久化到宿主机

```bash
docker run \
-p 3306:3306 \
--name mysql \
--privileged=true \
--restart unless-stopped \
-v /home/mysql8.0.20/mysql:/etc/mysql \
-v /home/mysql8.0.20/logs:/logs \
-v /home/mysql8.0.20/data:/var/lib/mysql \
-v /etc/localtime:/etc/localtime \
-e MYSQL_ROOT_PASSWORD=123456 \
-d mysql:8.0.20
```

**命令解释：**

```bash
 -p 端口映射
--privileged=true  挂载文件权限设置
--restart unless-stopped  设置 开机后自动重启容器
-v /home/mysql8.0.20/mysql:/etc/mysql    挂载配置文件
-v /home/mysql8.0.20/logs:/logs \      挂载日志
-v /home/mysql8.0.20/data:/var/lib/mysql \  挂载数据文件 持久化到主机，
-v /etc/localtime:/etc/localtime    容器时间与宿主机同步
-e MYSQL_ROOT_PASSWORD=123456    设置密码
-d  mysql:8.0.20   后台启动,mysql
```

#### 踩坑：使用Navicat远程连接MySQL

> 连接时出现 Can't connect to MySQL server (10060)

导致这个问题的原因可以参考：

[阿里云无法远程连接数据库MySQL错误码10060解决办法](https://jianlong.blog.csdn.net/article/details/79244091?utm_medium=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-7.channel_param&depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-7.channel_param)

解决方法：

**配置阿里云安全组策略**

![](https://gitee.com/nfLJ/Pic/raw/master/blog/20201016114457.png)