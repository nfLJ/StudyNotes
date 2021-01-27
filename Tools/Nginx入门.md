## Nginx入门

### Nginx是什么

Nginx是一个高性能的 HTTP 和反向代理服务器，特点是占有内存少，并发能力强，事实上 nginx 的并发能力确实在同类型的网页服务器中表现较好。

### Nginx作用

#### 1.正向代理，反向代理

简单来说正向代理就是代理了客户端，而反向代理是代理了服务端

![](https://gitee.com/nfLJ/Pic/raw/master/blog/20210127220029.jpg)

Nginx在做反向代理时，提供性能稳定，并且能够提供配置灵活的转发功能。Nginx可以根据不同的正则匹配，采取不同的转发策略，比如图片文件结尾的走文件服务器，动态页面走web服务器，只要你正则写的没问题，又有相对应的服务器解决方案，你就可以随心所欲的玩。并且Nginx对返回结果进行错误页跳转，异常判断等。如果被分发的服务器存在异常，他可以将请求重新转发给另外一台服务器，然后自动去除异常服务器。

#### 2.负载均衡

Nginx提供的负载均衡策略有2种：内置策略和扩展策略。内置策略为轮询，加权轮询，Ip hash。另外还可以自定义策略。以下是内置策略的示意图：

轮询和加权轮询：

![](https://gitee.com/nfLJ/Pic/raw/master/blog/20210127220307.jpg)

Ip hash算法，对客户端请求的ip进行hash操作，然后根据hash结果将同一个客户端ip的请求分发给同一台服务器进行处理，可以解决session不共享的问题。

![](https://gitee.com/nfLJ/Pic/raw/master/blog/20210127220412.jpg)

#### 3.动静分离

Nginx本身也是一个静态资源的服务器，当只有静态资源的时候，就可以使用Nginx来做服务器，同时现在也很流行动静分离，就可以通过Nginx来实现，首先看看Nginx做静态资源服务器。

动静分离是让动态网站里的动态网页根据一定规则把不变的资源和经常变的资源区分开来，动静资源做好了拆分以后，我们就可以根据静态资源的特点将其做缓存操作，这就是网站静态化处理的核心思路。

### Nginx配置文件

### Nginx常用命令

```bash
cd /usr/local/nginx/sbin/
./nginx  启动
./nginx -s stop  停止
./nginx -s quit  安全退出
./nginx -s reload  重新加载配置文件
ps aux|grep nginx  查看nginx进程
```

### 推荐阅读：

[Nginx详细介绍](https://www.cnblogs.com/muhy/p/10528543.html)

[Nginx 配置详解](https://www.runoob.com/w3cnote/nginx-setup-intro.html)