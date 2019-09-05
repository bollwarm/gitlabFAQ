
## 1 有用信息 & Url链接 📝

### 1.1 在线git教程

[Git简明教程](http://rogerdudler.github.io/git-guide/index.zh.html)

[图解Git](http://marklodato.github.io/visual-git-guide/index-zh-cn.html#commands-in-detail)

[Pro Git中文版](https://book.git-scm.com/book/zh/v2)

[交互式Git练习](https://learngitbranching.js.org/)

[Git飞行规则(git常见问题排查)](https://github.com/k88hudson/git-flight-rules/blob/master/README_zh-CN.md)

[:trollface:Git的奇技淫巧](https://github.com/521xueweihan/git-tips)

[GitHub 漫游指南](https://github.com/phodal/github)

### 1.2 Gitlab链接

Gitlab当前版本`12.1`,[功能介绍](https://www.toutiao.com/i6717106202813137412/)

#### 历史版本

[Gitlab 12.0](https://mbd.baidu.com/newspage/data/landingshare?context={%22nid%22:%22news_9558965903477159571%22})

[Gitlab 11.11](https://mbd.baidu.com/newspage/data/landingshare?context={%22nid%22:%22news_10069320671135253803%22})
[Gitlab 11.10](https://www.toutiao.com/i6683306619461173771/)
[Gitlab 11.9](https://mbd.baidu.com/newspage/data/landingshare?&context={%22nid%22:%22news_9951422023536597182%22})
[Gitlab 11.8](https://mbd.baidu.com/newspage/data/landingshare?&context={%22nid%22:%22news_9357882608472101547%22})
  [Gitlab 11.7](https://mbd.baidu.com/newspage/data/landingshare?&context={%22nid%22:%22news_9575778376970300115%22})
  [Gitlab 11.6](https://mbd.baidu.com/newspage/data/landingshare?&context={%22nid%22:%22news_10397293277319290255%22})
  [Gitlab 11.5](https://mbd.baidu.com/newspage/data/landingshare?&context={%22nid%22:%22news_9560230822959784969%22})
  [Gitlab 11.4](https://mbd.baidu.com/newspage/data/landingshare?&context={%22nid%22:%22news_9359524583066234852%22})
  [Gitlab 11.3](https://mbd.baidu.com/newspage/data/landingshare?&context={%22nid%22:%22news_9510042648375404911%22})
  [Gitlab 11.2](https://mbd.baidu.com/newspage/data/landingshare?&context={%22nid%22:%22news_9510042648375404911%22})
  [Gitlab 11.1](https://mbd.baidu.com/newspage/data/landingshare?&context={%22nid%22:%22news_8736339036171498903%22})

Gitlab 官网  www.gitlab.com

Gitlab QQ交流群 208598995

Gitlab-runner文档

https://gitlab.com/gitlab-org/gitlab-runner/tree/master/docs

Security安全文档

https://gitlab.com/help/security/README.md

Gitlab安装国内镜像(清华)

https://mirror.tuna.tsinghua.edu.cn/help/gitlab-ce/

## 2 Gitlab安装和配置 🚧

### 2.1安装Gitlab （以centos 6 为例）

只需要两步，官方说明的第一步实际上可以省略，如果后面发现有问题，在yum其他依赖包，其实上依赖包都安装了。

+ 添加Gitlab yum源

    curl https://packages.gitlab.com/install/repositories/gitlab/gitlab-ce/script.rpm.sh | sudo bash

+ yum安装

    sudo EXTERNAL_URL="http://gitlab.xxoo" yum -y install gitlab-ce

+ 首次访问 浏览器通过

如果虚拟域名比如 gitlab.xxoo，在你访问机器hosts增加一行

    GitlabIP gitlab.xxoo

然后浏览器用gitlab.xxoo访问，用户用root，并且任意8个字符登陆，然后修改密码。

### 2.2源码安装(`一般没必要`)

https://docs.gitlab.com/ce/install/installation.html

### 2.3 docker安装

https://docs.gitlab.com/omnibus/docker/

### 2.4 典型的Gitlab配置

```
external_url 'http://git.YOUdomain'
gitlab_rails['gitlab_shell_ssh_port'] = 8888
nginx['listen_port'] = 5580
gitlab_rails['smtp_enable'] = true
gitlab_rails['smtp_address'] = "smtp.qq.com"
gitlab_rails['smtp_port'] = 465
gitlab_rails['smtp_user_name'] = "git@YOUdomian"
gitlab_rails['smtp_password'] = "YOUpasswd"
gitlab_rails['smtp_authentication'] = "login"
gitlab_rails['smtp_enable_starttls_auto'] = true
gitlab_rails['smtp_tls'] = true
gitlab_rails['gitlab_email_from'] = 'gitYOUdomian'
```
说明：这就是几个需要自己配置的部分

第一个是external_url，那是显示在你gitlab页面链接那的url

第二，三分别是需要自定义的ssh和web端口

修改后重加载和重启服务（3.2）

## 3 Gitlab nginx配置（HTPPS支持等）🔨

### 3.1 Gitlab nginx配置

(https://docs.gitlab.com/omnibus/settings/nginx.html#configuring-gitlab-trusted_proxies-and-the-nginx-real_ip-module)

### 3.2 配置生效和重启服务

    gitlab-ctl reconfigure
    gitlab-ctl restart

注意gitlab任何配置都在主配置`/etc/gitlab/gitlab.rb`里面修改

修改好gitlab-ctl reconfig 会自动生成各个部分的配置，不要单独去改其他部分的配置，
比如为了修改web端口去修改nginx配置，那都是错误的方法，很多blog都是那样配的，
那是完全错误的方法。reconfig就会丢掉。

### 3.3 修改Gitlab管理员root密码

1.启动rails console

    gitlab-rails console production

2、依次输入执行以下操作

```
user = User.where(id: 1).first

user.password="你的密码"

user.save!

quit

```

## 4 Gitlab 使用API批量创建分组和项目 🐎

[gitlab 使用API批量创建分组和项目](batchAPI.md)

## 5 仓库库迁移及批量创建Gitlab库 ⚡️

[repo库迁移及批量创建gitlab库](batchADD.md)

## 6 Gitlab数据目录迁移 🚑

Gitlab代码数据默认目录：/var/opt/gitlab/git-data/repositories

数据目录，挂一个大磁盘到目录[目录按照你的喜好命名]

````
makedir  -p /data/gitlab-data
mount sdb  data/gitlab-data
````

### 操作步骤：

+ 停止相关数据连接服务

    gitlab-ctl stop unicorn   
    gitlab-ctl stop sidekiq

+ 数据迁移

    cp -r -p /var/opt/gitlab/git-data/repositories/ /home/gitlab-data/

这里CP一定要加上-p参数，不然会导致权限问题

+ 更改配置 /etc/gitlab/gitlab.rb

    vim /etc/gitlab/gitlab.rb

+ 指定数据目录

    git_data_dir "/home/gitlab-data"

+  使配置生效

     gitlab-ctl reconfigure

+  重启gitlab

     gitlab-ctl restart

## 7  Git & Gitlab常见错误 🐛

### 7.1 错误502解决办法

#### 8080 端口冲突

原因：由于unicorn默认使用的是 8080 端口。

解决办法：打开 /etc/gitlab/gitlab.rb ,去掉 # unicorn['port'] = 8080 的注释，
将 8080 修改为 9090 ，保存后运行 sudo gitlab-ctl reconfigure 即可。

#### 内存原因

如果内存较小，服务器硬件资源太小

则服务启动过程较慢，在此期间启动则会导致502，等一段时间，服务启动后，再访问就ok了

### 7.2 Git SSH认证问题

#### 很简单的，证书添加后：

+ ssh -T git@IP测试，测试成功说明证书添加成功。

比如测试github: `ssh -T git@github.com`

`Hi bollwarm! You've successfully authenticated, but GitHub does not provide shell access.`

注意测试建实例时候吧git@github.com换成`git@XXX`，XXX为你自己实例服务器的IP地址或者域名。

如果有问题，则你证书生成或者添加有问题。你可以`ssh -vv -T git@IP` 来看详细ssh通讯过程和报错，找出问题。

主要问题keys生成不对；

文件权限问题（linux下证书权限必须为600，如果是从其他地方复制来的需要注意）；

添加公钥时候添加不是用户账号下证书，而是部署证书（部署证书没有push权限）。 


+ 确保你项目目录下.git/config 文件中远程仓的URL链接是SSH链接：git或者ssh开头，而不是http/https开头。

+ https认证时候可以直接在配置url上配上用户名和密码，这样不用每次操作都输入用户名和密码，格式如下：

    https://用户名:密码@IP其他部分。

## 8 其他信息 🌐

更多信息欢迎[Pull Request](https://github.com/bollwarm/gitlabFAQ)

Gitlab QQ交流群 `208598995`,交流

本repo会持续更新
