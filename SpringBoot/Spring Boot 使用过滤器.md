## Spring Boot 使用过滤器

### 方式一：使用 Spring Boot 提供的 FilterRegistrationBean 注册 Filter

1.先自定义 Filter 

```java
@Slf4j
public class MyFilter implements Filter {

    @Override
    public void init(FilterConfig filterConfig) throws ServletException {
        log.info("init被调用");
    }

    @Override
    public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
        // do something 处理request 或response
        log.info("filter被调用");
        // 调用filter链中的下一个filter
        filterChain.doFilter(servletRequest,servletResponse);
    }

    @Override
    public void destroy() {
        log.info("destroy被调用");
    }
}
```

2.注册自定义 Filter

```java
@Configuration
public class FilterConfig {
    @Bean
    public FilterRegistrationBean registrationBean() {
        FilterRegistrationBean filterRegistrationBean = new FilterRegistrationBean(new MyFilter());
        filterRegistrationBean.addUrlPatterns("/*");
        // 指定过滤器顺序，数值越小越先执行
        filterRegistrationBean.setOrder(1);
        return filterRegistrationBean;
    }
}
```

### 方式二：使用原生 Servlet 注解定义 Filter

```java
@Slf4j
// 定义 filterName 和过滤的 url
@WebFilter(urlPatterns = "/*",filterName = "Myfilter2")
public class MyFilter2 implements Filter {
    @Override
    public void init(FilterConfig filterConfig) throws ServletException {
        log.info("init被调用");
    }

    @Override
    public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
        // do something 处理request 或response
        log.info("filter2被调用");
        // 调用filter链中的下一个filter
        filterChain.doFilter(servletRequest,servletResponse);
    }

    @Override
    public void destroy() {
        log.info("destroy被调用");
    }
}
```

在启动类上添加注解告诉 Spring 要扫描的路径

```java
@SpringBootApplication
@ServletComponentScan(basePackages = "com.nflj.filter")
public class Application {
    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
}
```

### 总结

Spring Boot 使用过滤器有两种方式：

1. 使用 Spring Boot 提供的 FilterRegistrationBean 注册 Filter
2. 使用原生 Servlet 注解定义 Filter

> 两种方式的本质都是一样的，都是去 FilterRegistrationBean 注册自定义 Filter

