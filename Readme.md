# 1 项目简介

`吉他曲谱搜索推荐系统`，实现 吉他曲谱展示、曲谱数据的爬虫、曲谱个性化推荐等功能的前后端分离的**WEB应用**。



## 1.1 系统架构和技术选型

整体采用`B/S架构`即浏览器-服务器架构：

前端采用基于MVVM模型的`Vue`框架，结合`ElementUI`组件库，采用`ECharts`进行图表可视化分析，采用`Axios`库来请求或响应数据。

后端整体基于`Node.js`框架，利用`express`框架搭建服务器，提供Restful风格的接口，数据库采用`SQLite3`并有csv格式文件用于保存推荐模块相关的数据，ORM对象关系映射层为`Sequelize`框架，网络爬虫功能采用基于JavaScript的静态爬虫库`cheerio`以及动态爬虫库`puppeteer`。

<img src="E:\projects\web projects\score-design\doc\img\Snipaste_2021-06-24_14-21-38.jpg" style="zoom: 80%;" />



## 1.2 系统功能

<img src="E:\projects\web projects\score-design\doc\img\Snipaste_2021-06-24_14-26-10.jpg" style="zoom:80%;" />



### 1.2.1 推荐模块

如图，用户评分信息分别计入用户评分信息文件和用户信息数据库，用户评分信息主要有三个字段：用户ID，曲谱ID，以及评分数据。通过曲谱信息数据库和用户评分信息就可以构建推荐算法关键的**用户-曲谱评分矩阵**，通过混合推荐策略为用户个性化推荐曲谱。

在系统初期，用户较少，评分信息也较少，所以通过爬取豆瓣网站相应的用户评分来构建可靠的评分数据集。随着用户增多，用户评分信息就更有价值，推荐结果就会更准确。

<img src="E:\projects\web projects\score-design\doc\img\Snipaste_2021-06-24_14-34-42.jpg" style="zoom:80%;" />



### 1.2.2 爬虫模块

各大吉他谱网站都有海量公开免费的曲谱信息，例如吉他吧网站有609页数据，17吉他网站有677页数据……这些网站还在不断更新中。

但这些网站的曲谱信息并未作系统的分类处理，所以看上去十分纷杂。且单个曲谱的信息没有规则化，比如有的曲谱有海报，有些没有；有些曲谱有简介，有些却没有。同时，这些网站并没有曲谱的评分信息、标签分类等，但每个曲谱有对应的歌曲信息，豆瓣网站提供了歌曲的评分、分类、标签等信息，以及还有用户对歌曲的评分情况。

<img src="E:\projects\web projects\score-design\doc\img\Snipaste_2021-06-24_14-37-13.jpg" style="zoom:80%;" />

### 1.2.3 用户评分模块

用户进入曲谱详情页，会默认向后端发起get请求，查询该曲谱信息和用户本身的评分信息，随后在页面渲染该曲谱的详细信息以及该用户对曲谱的评分。

倘若还未进行，则评分处默认为0。

当用户选择评分并提交评分的时候，向后端发出put请求来修改数据库中的评分信息，后端返回最新的用户评分信息随后重绘该详情页。

用户评分时序图如图：

<img src="E:\projects\web projects\score-design\doc\img\Snipaste_2021-06-24_14-39-14.jpg" style="zoom:80%;" />



## 1.3 数据库结构

系统以**CSV文件格式**来存储用户的评分信息，其格式为用户id、曲谱id、评分数据。

系统定义了曲谱和用户两个实体，其数据库表结构如下：



1. 曲谱信息表

| 字段名称    | 字段类型     | 是否为空 | 备注                   |
| ----------- | ------------ | -------- | ---------------------- |
| id          | integer      | 否       | 主键、曲谱ID、自动生成 |
| title       | varchar(255) | 否       | 曲谱标题               |
| name        | varchar(255) | 否       | 歌曲名称               |
| keys        | varchar(255) | 是       | 曲谱调号               |
| singer      | varchar(255) | 否       | 演唱歌手               |
| poster      | varchar(255) | 否       | 曲谱海报               |
| tags        | text         | 是       | 曲谱标签、以”; ”分割   |
| rating      | text         | 是       | 曲谱评分               |
| views       | varchar(255) | 是       | 曲谱浏览量             |
| spectrum    | text         | 否       | 图片谱、以”; ”分割     |
| description | text         | 是       | 曲谱简介               |
| created_at  | datetime     | 否       | 曲谱创建时间           |
| updated_at  | datetime     | 是       | 曲谱修改时间           |
| deleted_at  | datetime     | 是       | 曲谱删除时间           |



2. 用户信息表

| 字段名称   | 字段类型     | 是否为空 | 备注                   |
| ---------- | ------------ | -------- | :--------------------- |
| id         | integer      | 否       | 主键、用户ID、自动生成 |
| email      | varchar(255) | 否       | 用户登录和注册邮箱     |
| password   | varchar(255) | 否       | 用户密码(MD5加密后的)  |
| ratings    | text         | 是       | 用户评分、以”; ”分割   |
| created_at | datetime     | 否       | 曲谱创建时间           |
| updated_at | datetime     | 是       | 曲谱修改时间           |
| deleted_at | datetime     | 是       | 曲谱删除时间           |







# 2 项目启动

- 项目下载到本地

  ```shell
  git clone https://github.com/XiDieccc/score-design.git
  ```

- 进入后端开启服务器，后端地址：http://127.0.0.1:3000/api

  ```shell
  cd server
  npm install
  npm run dev
  ```

- 进入前端，访问前端地址：http://localhost:8080/

  ```shell
  cd web
  npm install
  npm run serve
  ```

- 数据库初始为空，注册账户后可进行爬虫操作来获取吉他谱

