## Spring Boot 使用拦截器

### 开发拦截器

自定义一个 `Interceptor` 实现 `HandlerInterceptor`

自定义拦截器的几个方法：

`preHandle`：调用Controller某个方法之前
`postHandle`：Controller之后调用，视图渲染之前，如果控制器Controller出现了异常，则不会执行此方法
`afterCompletion`：不管有没有异常，这个afterCompletion都会被调用，用于资源清理

```java
@Slf4j
public class MyInterceptor implements HandlerInterceptor {

    /**
     * controller方法调用前调用。
     * @return 往下执行则返回true，否则返回false
     */
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        log.info("[{}]执行{}方法！", this.getClass().getSimpleName());
        return true;
    }
    /**
     * controller方法调用后视图渲染前执行。
     */
    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
        log.info("[{}]执行{}方法！", this.getClass().getSimpleName(), "postHandle");
    }
    /**
     * controller方法调用且视图渲染完成后执行
     */
    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
        log.info("[{}]执行{}方法！", this.getClass().getSimpleName(), "afterCompletion");
    }
}
```

### 配置拦截器

创建配置类实现 `WebMvcConfigurer`，重写 `#addInterceptors` 方法

```java
@Configuration
public class InterceptorConfig implements WebMvcConfigurer {
    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(getMyInterceptor())
                .addPathPatterns("/**")
                // 指定执行顺序，数值越小越优先
                .order(1);
    }
    @Bean
    public MyInterceptor getMyInterceptor(){
        return new MyInterceptor();
    }
}
```

### 测试

启动服务，访问接口，后台日志如下图，发现拦截器正常运行，并且在过滤器之后

![](https://gitee.com/nfLJ/Pic/raw/master/blog/20201214114157.png)