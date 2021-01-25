SpringCloudAlibaba启动报错

`java.lang.IllegalStateException: Service id not legal hostname (gulimall_coupon)`
原因：服务名不能使用**下划线**

解决方法：

修改application.yml应用名称