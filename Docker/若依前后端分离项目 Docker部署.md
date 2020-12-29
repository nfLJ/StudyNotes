## 若依前后端分离项目 Docker 部署

### 一、环境准备

#### 服务器

阿里云服务器 1核+2GB即可

#### 软件

本次部署采用的是 docker，因此软件环境都在 docker 上

我们需要 MySQL 8.0.x版本，Redis，Nginx，提前下好镜像即可

### 二、运行镜像

#### MySQL安装

MySQL我用的是8.0.x的版本，在部署过程中出现了一些问题，在这里跟大家分享一下

```bash
docker run \
-p 3306:3306 \
--name mysql \
--privileged=true \
--restart unless-stopped \
-v /home/mysql8.0.20/mysql:/etc/mysql \
-v /home/mysql8.0.20/logs:/logs \
-v /home/mysql8.0.20/data:/var/lib/mysql \
-v /home/mysql8.0.20/mysql-files:/var/lib/mysql-files \
-v /etc/localtime:/etc/localtime \
-e MYSQL_ROOT_PASSWORD=123456 \
-d mysql:8.0.20 \
--lower_case_table_names=1
```

**命令解释：**

```bash
 -p 端口映射
--privileged=true  挂载文件权限设置
--restart unless-stopped  设置 开机后自动重启容器
-v /home/mysql8.0.20/mysql:/etc/mysql    挂载配置文件
-v /home/mysql8.0.20/logs:/logs \      挂载日志
-v /home/mysql8.0.20/data:/var/lib/mysql \  挂载数据文件 持久化到主机
-v /home/mysql8.0.20/mysql-files:/var/lib/mysql-files	MySQL8之后需要同步这个文件夹
-v /etc/localtime:/etc/localtime    容器时间与宿主机同步
-e MYSQL_ROOT_PASSWORD=123456    设置密码
-d  mysql:8.0.20   后台启动,mysql
--lower_case_table_names=1	  让MySQL不区分大小写（0:大小写敏感;1:大小写不敏感）
```

> 在没有配置 `--lower_case_table_names=1` 前出现 `Table XX.QRTZ_LOCKS doesn‘t exist 的问题`
>
> 百度一番后发现 MySQL 5.x 和 8.x 的这个配置还有些不同

 具体错误忘记截图了，可以参考 https://blog.csdn.net/zhengliangs/article/details/105998941

**解决问题**

做好备份，删除原来的 MySQL 容器，重新运行 MySQL，在命令结尾加入 `--lower_case_table_names=1`，

因为 MySQL 8 之后，这一步需要在初始化的时候设置

#### Redis安装

```bash
docker run -p 6379:6379 --name redis -v /home/redis/data/:/data -d redis:3.2 redis-server --appendonly yes
```

**命令解释**

```bash
-v /home/redis/data/:/data		挂载数据目录
--appendonly yes	开启redis 持久化
```

#### Nginx安装

因为我需要挂载目录，所以我运行了下面的命令

```bash
docker run \
-d \
-p 80:80 \
--name nginx \
-v /home/nginx/conf:/etc/nginx \
-v /home/nginx/html:/usr/share/nginx/html \
-v /home/nginx/logs:/var/log/nginx \
nginx
```

运行完之后，发现总是会自动退出，查看日志信息后百度无果，一番搜索后发现 nginx 启动时需要先将要挂载的目录先创建出来，否则就会出现自动退出的情况。

因此，我们需**先创建挂载目录，然后再运行命令**

### 三、打包项目

#### 前端

- 修改`vue.config.js` 中 `devServer`节点映射端口，与后端端口一致

运行以下命令：

```bash
npm run build:prod
```

会在本地生成 `dist` 目录

#### 后端

- 修改 `application.yml` 端口、文件上传路径

- 修改`logback.xml` 日志生成路径`log.path`

- 修改 MySQL、Redis地址

运行以下命令：

```bash
mvn clean
mvn package
```

在 `ruoyi-admin`的 `target`下生成了一个 jar 包，那就是我们需要的

### 四、部署

#### 前端

配置 nginx，这里开始我在  `/nginx/conf/conf.d/default.conf`下配置，发现配置不生效。后来在`/nginx/conf/conf.d/nginx.conf`下配置就好了，具体配置如下：

```
 server {
        listen       80;
        server_name  localhost;		# 可以用服务器ip代替

        location / {
            root   /usr/share/nginx/html/dist/;
            index  index.html index.htm index login;
            try_files $uri $uri/ /index.html last;
        }
        location /prod-api/ {
            proxy_set_header Host $http_host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header REMOTE-HOST $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_pass http://localhost:7777/;		# 可以用服务器ip代替
        }
     }
```

> 在我部署完成后发现不是在根目录下 **刷新** 就会出现 `404 Not Found`，找到以下解决方案：

在配置 location 的时候加上`try_files $uri $uri/ /index.html last;`

#### 后端

我使用的是 `Dockerfile + jar` 打包成**镜像**的部署方式

**Dockerfile**

```dockerfile
FROM java:8

VOLUME /jiang

ADD ruoyi-admin.jar app.jar

EXPOSE 7777

ENTRYPOINT ["java","-jar","app.jar"]
```

在服务器里创建一个文件夹，放入 `Dockerfile 和 jar包`，运行如下命令生成镜像

```bash
docker build -t ruoyi-vue .
```

> 注意：最后还有一个  `.`

此时，我们只需运行生成的镜像即可

```bash
docker run -d -p 7777:7777 --name nflj-vue ruoyi-vue 
```

至此，我们就完成了前后端分离 使用Docker部署若依项目

