## Spring Boot 使用 dev-tools 实现热部署

> 在开发 Spring Boot 项目的时候，由于需要经常进行代码的改动，如果我们频繁地重启项目，这毫无疑问是极其浪费时间和电脑性能的，所以我们可以使用 dev-tools 工具来实现 Spring Boot项目的热部署效果，代码改动后即时生效，节省大量的开发时间。

### 一、引入依赖

```xml
<!--devtools热部署-->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-devtools</artifactId>
    <optional>true</optional>
    <scope>true</scope>
</dependency>
```

### 二、配置 dev-tools 工具参数（可选）

```yaml
# 默认就是开启的，这里可以不用配置
spring:
  devtools:
    restart:
      enabled: true  #设置开启热部署
```

### 三、修改 IDEA 设置

#### 1.勾选Build project automatically

![](https://gitee.com/nfLJ/Pic/raw/master/blog/20201213232002.png)

#### 2.勾选 compiler.automake.allow.when.app.running 

快捷键 **Ctrl+Shift+Alt+/** ，选择Registry

![](https://gitee.com/nfLJ/Pic/raw/master/blog/20201213231952.png)

![](https://gitee.com/nfLJ/Pic/raw/master/blog/20201213231918.png)

