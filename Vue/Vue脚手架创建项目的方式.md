## Vue脚手架创建项目的方式

> 脚手架版本 ：@vue/cli 4.5.8

### 1. 命令行

```bash
vue create 项目名
```

![](https://gitee.com/nfLJ/Pic/raw/master/blog/20201030170547.png)

官方给出了3个创建选择，分别是**Vue2默认**、**Vue3默认**和**自定义**

**我们选择自定义，即 `Manually select features`**

![](https://gitee.com/nfLJ/Pic/raw/master/blog/20201030171219.png)

各选项含义：

| 选项                              | 含义                         |
| --------------------------------- | ---------------------------- |
| Choose Vue version                | 选择Vue的版本                |
| Babel                             | 支持高阶语法转换             |
| TypeScript                        | 支持使用 TypeScript 书写源码 |
| Progressive Web App (PWA) Support | 支持PWA                      |
| Router                            | 路由                         |
| Vuex                              | store                        |
| CSS Pre-processors                | CSS 预处理器                 |
| Linter / Formatter                | 代码风格检查和格式化         |
| Unit Testing                      | 支持单元测试                 |
| E2E Testing                       | 支持E2E测试                  |

**选择：Babel，Router，Vuex，CSS Pre-processors，回车：**

![](https://gitee.com/nfLJ/Pic/raw/master/blog/20201030172044.png)**询问你是否使用历史路由，输入 `n` ，回车：**

![](https://gitee.com/nfLJ/Pic/raw/master/blog/20201030172326.png)

**询问你项目里需要支持何种动态样式语言，我们选择`Sass/SCSS (with node-sass)`，回车：**

![](https://gitee.com/nfLJ/Pic/raw/master/blog/20201030172642.png)

**询问你选择配置文件的位置，因为每个插件都有自己单独的配置文件，我们选择第一个，回车：**

![](https://gitee.com/nfLJ/Pic/raw/master/blog/20201030172853.png)

**询问你是否将刚才的配置保存起来，以便下次直接使用，输入`y`，回车：**

![](https://gitee.com/nfLJ/Pic/raw/master/blog/20201030173103.png)

**给你的配置命名，回车，开始创建项目**

### 2. 图形化界面

命令行输入`vue ui` 进入图形化界面

创建过程与命令行类似，这里就不再赘述了。

