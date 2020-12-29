## Spring Boot 使用 Quartz 定时任务

### Quartz简介

Quartz是一个开源的作业调度框架，它完全由Java写成，并设计用于J2SE和J2EE应用中。它提供了巨大的灵 活性而不牺牲简单性。你能够用它来为执行一个作业而创建简单的或复杂的调度。它有很多特征，如：数据库支持，集群，插件，EJB作业预构 建，JavaMail及其它，支持cron-like表达式等。

在Quartz中的主要概念：

- Scheduler：调度任务的主要API
- ScheduleBuilder：用于构建Scheduler，例如其简单实现类SimpleScheduleBuilder
- Job：调度任务执行的接口，也即定时任务执行的方法
- JobDetail：定时任务作业的实例
- JobBuilder：关联具体的Job，用于构建JobDetail
- Trigger：定义调度执行计划的组件，即定时执行
- TriggerBuilder：构建Trigger

### Spring Boot 整合 Quartz

#### 1. 引入Quartz 的依赖

```xml
 <dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>

<!--quartz定时调度依赖-->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-quartz</artifactId>
</dependency>
```

#### 2. 定义定时任务业务逻辑

该类继承 QuartzJobBean 重写 #executeInternal 方法即可

```java
public class QuartzJob1 extends QuartzJobBean {
    @Override
    protected void executeInternal(JobExecutionContext jobExecutionContext) throws JobExecutionException {
        SimpleDateFormat sdf=new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
        System.out.println("QuartzJob1----" + sdf.format(new Date()));
    }
}
```

#### 3. 配置定时任务

创建 Quartz 定时任务配置类（QuartzConfig.java）编写相关配置信息

```java
@Configuration
public class QuartzConfig {
    // JobDetail由JobBuilder构建，同时关联了任务QuartzJob1
    @Bean
    public JobDetail jobDetail() {
        return JobBuilder.newJob(QuartzJob1.class).storeDurably().build();
    }
    // 定时调度Trigger，简单实现类SimpleScheduleBuilder用于构建Scheduler
    // TriggerBuilder则用于构建Trigger
    @Bean
    public Trigger trigger() {
        SimpleScheduleBuilder scheduleBuilder = SimpleScheduleBuilder.simpleSchedule()
                // 每5秒执行一次
                .withIntervalInSeconds(5)
                // 永久重复，一直执行下去
                .repeatForever();
        return TriggerBuilder.newTrigger()
                .forJob(jobDetail())
                .withSchedule(scheduleBuilder)
                .build();
    }
}
```

#### 4. 运行测试

![](https://gitee.com/nfLJ/Pic/raw/master/blog/20201213135329.png)