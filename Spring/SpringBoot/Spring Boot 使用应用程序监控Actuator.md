## Spring Boot 2.2.x 之 Actuator 

### 简介

Actuator 是 Spring Boot 提供的对应用系统的自省和监控功能，通过 Actuator，可以使用数据化的指标去度量应用的运行情况，比如查看服务器的磁盘、内存、CPU等信息，系统的线程、gc、运行状态等等。

### Actuator的使用

#### 引入依赖

```xml
<!-- 实现对 Actuator 的自动化配置 -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
<!-- 实现对 Spring MVC 的自动化配置 -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>
```

#### 配置application.yml

```yaml
# actuator监控
management:
  server:
    # 设置监控服务端口，没有配的话默认是应用端口
    port: 8081
```

#### 启动应用

访问  **localhost:8081/autuator** ，结果如下图

![](https://gitee.com/nfLJ/Pic/raw/master/Spring/20201021112709.png)

可以看出此时只显示了  **/health** 和  **/info** 两个端点 ，这是为了程序的安全性考虑，默认只开启这两个端口， 如果想显示所有端口， 可以进行如下配置：

```yaml
management:
  server:
    port: 8081
  endpoints:
    web:
      exposure:
        include: '*'
```

如果想自定义端口， 配置如下：

```yaml
management:
  server:
    port: 8081
  endpoints:
    web:
      exposure:
        include: info,beans,health,env
```

下面表格列举了Actuator的端点说明：

| 端点             | 描述                                                         |
| ---------------- | ------------------------------------------------------------ |
| auditevents      | 获取当前应用暴露的审计事件信息                               |
| beans            | 获取应用中所有的 Spring Beans 的完整关系列表                 |
| caches           | 获取公开可以用的缓存                                         |
| conditions       | 获取自动配置条件信息，记录哪些自动配置条件通过和没通过的原因 |
| configprops      | 获取所有配置属性，包括默认配置，显示一个所有 @ConfigurationProperties 的整理列版本 |
| env              | 获取所有环境变量                                             |
| flyway           | 获取已应用的所有Flyway数据库迁移信息，需要一个或多个 Flyway Bean |
| liquibase        | 获取已应用的所有Liquibase数据库迁移。需要一个或多个 Liquibase Bean |
| health           | 获取应用程序健康指标（运行状况信息）                         |
| httptrace        | 获取HTTP跟踪信息（默认情况下，最近100个HTTP请求-响应交换）。需要 HttpTraceRepository Bean |
| info             | 获取应用程序信息                                             |
| integrationgraph | 显示 Spring Integration 图。需要依赖 spring-integration-core |
| loggers          | 显示和修改应用程序中日志的配置                               |
| logfile          | 返回日志文件的内容（如果已设置logging.file.name或logging.file.path属性） |
| metrics          | 获取系统度量指标信息                                         |
| mappings         | 显示所有@RequestMapping路径的整理列表                        |
| scheduledtasks   | 显示应用程序中的计划任务                                     |
| sessions         | 允许从Spring Session支持的会话存储中检索和删除用户会话。需要使用Spring Session的基于Servlet的Web应用程序 |
| shutdown         | 关闭应用，要求endpoints.shutdown.enabled设置为true，默认为 false |
| threaddump       | 获取系统线程转储信息                                         |
| heapdump         | 返回hprof堆转储文件                                          |
| jolokia          | 通过HTTP公开JMX bean（当Jolokia在类路径上时，不适用于WebFlux）。需要依赖 jolokia-core |
| prometheus       | 以Prometheus服务器可以抓取的格式公开指标。需要依赖 micrometer-registry-prometheus |

### 端点的安全性

对于正在生产环境上运行的程序来说端点是比较敏感的，我们并不希望别人恶意的去访问端点，特别是

**/shutdown** 这样的端点。

当我们使用 **POST** 方式请求 **/shutdown** 时，可以直接将应用关闭，如下图

![](https://gitee.com/nfLJ/Pic/raw/master/Spring/20201021120607.png)

因此，我们需要通过`management.endpoint.shutdown.enabled = false`来关闭此端点。

