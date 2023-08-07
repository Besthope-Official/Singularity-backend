# Python 项目

{% hint style="warning" %}
项目的完成度**事关训练营的考核**, 决定了你**是否能够加入奇点工作室**! 请务必认真对待.

在暑训结束的时候, 我们会要求大家把自己的 Github 仓库链接公开在我们训练营仓库的一个 Issue 里, 以此作为评定的依据.
{% endhint %}

## 项目要求

实现一个社区交流平台 (类似贴吧) 的后端部分

编写符合 RESTful 风格, 以及合理 Flask 项目的接口代码

技术栈: Flask 2.3.2 + MySQL 8.0

{% hint style="info" %}
我们不会过多地透露设计的细节, 目的是为了让大家参与进入**设计**的这一重要环节. 如果你对某个项目需求感到困惑, 大胆地提问. 我们会尽可能地将文档完善, 以帮助大家完成初次的 Flask 项目.

此外, 在编写接口代码的过程中, 你势必会感到阻力重重. 一部分原因是: 会有内容在我们本次的训练营中没有涵盖. 但这**并不意味着你没有能力去写对应的部分**.
{% endhint %}

## 设计数据库

设计以下几个表:

* 用户 users
  * 下面是一些供参考的字段(类型大家自己去决定):
  * email, username, password, subscribed, created
* 评论 comments
  * content, user, created
* 吧 subvues
  * name, permalink, description, created, moderators(吧务)
* 帖子 posts
  * title, subvue, user, content, comments, created, image, upvotes, downvotes

## 接口开发

### 用户的注册和登录 (Hint: `werkzeug.security`)

* POST /api/signup
* POST /api/login

### 文章的CRUD

* GET /api/posts 返回帖子主页
* POST /api/posts 创建帖子
* GET /api/posts/id/\<string:id> 返回指定 id 的帖子
* GET /api/posts/user/\<string:username> 返回指定用户名的帖子
* DELETE /api/posts/id/\<string:id> 删除指定 id 的帖子

### 用户互动

* POST /api/posts/\<string:id>/comments 指定 id 帖子下创建评论
* POST /api/posts/\<string:id>/upvote 指定帖子下好评
* POST /api/posts/\<string:id>/downvote 指定帖子下差评

### 创建吧

* GET /api/subvues/\<string:permalink> 获取吧数据
* GET /api/subvues/\<string:permalink>/posts 获取某个吧下的帖子
* POST /api/subvues 创建吧
* POST /api/subvues/\<string:permalink>/subscribe 订阅某个吧
* POST /api/subvues/\<string:permalink>/unsubscribe 不订阅某个吧

## 用户页面

* GET /api/users/username

## 错误页面返回

* 页面未找到 404
* 内部服务器错误 500 405
* 请求实体过大（>= 1 MB）413

