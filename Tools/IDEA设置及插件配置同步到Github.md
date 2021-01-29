## IDEA设置及插件配置同步到github

### 为什么要同步？

每次重新安装IDEA时，之前的设置和插件都需要重新配置，很麻烦。自动同步可以解决重新配置设置的问题，不用担心配置丢失

### 如何配置同步？

#### 1.在 github 创建一个新的仓库用来存放配置

#### 2.创建一个 github 的 Token 给 IDEA 使用

#### 3.在 IDEA 中设置仓库地址

![image-20210129142712158](https://gitee.com/nfLJ/Pic/raw/master/blog/20210129142720.png)

#### 4.配置仓库地址和 Token

![image-20210129142840443](https://gitee.com/nfLJ/Pic/raw/master/blog/20210129142842.png)

三个按钮解释：

- **Merge**：比较合并
- **Overwrite Local**：覆盖本地
- **Overwrite Remote**：覆盖远程（初次配置选择这个）

> 到此，就配置完了同步。

github仓库就有会所更新：

![image-20210129143322956](https://gitee.com/nfLJ/Pic/raw/master/blog/20210129143324.png)

### 总结

今后如果再有重新安装 IDEA 的情况，配置直接从 github 导入即可，只需在 IDEA 中配置好远程仓库，直接同步即可。之后若有配置更新，也可以同步到 github 中，以免下次重新配置。

