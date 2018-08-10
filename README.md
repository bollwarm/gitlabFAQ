
## 1.有用信息 & Url链接

### 1.1在线git教程

[git简明教程](http://rogerdudler.github.io/git-guide/)

[图解Git](http://marklodato.github.io/visual-git-guide/index-zh-cn.html#commands-in-detail)

[Pro Git中文版](https://book.git-scm.com/book/zh/v2)

### 1.2 gitlab链接

gitlab 官网  www.gitlab.com

gitlab QQ交流群 208598995

gitlab-runner文档

https://gitlab.com/gitlab-org/gitlab-runner/tree/master/docs

Security安全文档

https://gitlab.com/help/security/README.md


## 2.gitlab安装和配置

### 2.1安装gitlab （以centos 6 为例）

只需要两步，官方说明的第一步实际上可以省略，如果后面发现有问题，在yum其他依赖包，其实上依赖包都安装了。

#### 添加gitlab yum源

    curl https://packages.gitlab.com/install/repositories/gitlab/gitlab-ce/script.rpm.sh | sudo bash

#### yum安装

    sudo EXTERNAL_URL="http://gitlab.xxoo" yum -y install gitlab-ce

#### 首次访问 浏览器通过

如果虚拟域名比如 gitlab.xxoo，在你访问机器hosts增加一行

    GitlabIP gitlab.xxoo

然后浏览器用gitlab.xxoo访问，用户用root，并且任意8个字符登陆，然后修改密码。

### 2.2源码安装 [一般没必要]

https://docs.gitlab.com/ce/install/installation.html

### 2.3 docker安装

https://docs.gitlab.com/omnibus/docker/

### 2.4 典型的gitlab配置

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

第一个是url，那是显示在你gitlab页面链接那的url

第二，三分别是需要自定义的ssh和web端口

## 3 gitlab nginx配置（HTPPS支持等）

[gitlab nginx配置](https://docs.gitlab.com/omnibus/settings/nginx.html#configuring-gitlab-trusted_proxies-and-the-nginx-real_ip-module)

### 3.1 配置生效和重启服务

    gitlab-ctl reconfig
    gitlab-ctl restart

注意gitlab任何配置都在主配置`/etc/gitlab/gitlab.rb`里面修改

修改好gitlab-ctl reconfig 会自动生成各个部分的配置，不要单独去改其他部分的配置，
比如为了修改web端口去修改nginx配置，那都是错误的方法，很多blog都是那样配的，
那是完全错误的方法。reconfig就会丢掉。
`
## 4 gitlab 使用API批量创建分组和项目

###  创建分组

创建一个文件，将分组名称写入，一行一个：

    vim group

使用gitlab的API批量创建群组：

```
for i in `cat group`; do 

echo $i": ";
curl --request POST --header "PRIVATE-TOKEN: 9koXXXXOOOOOs5t" \
 --data "name=${i}&path=${i}" http://IPofgitlab/api/v4/groups;
done

```
### 创建项目

使用gitlab的API批量创建项目仓库:

写入所有群组XXX的所有项目到文件repo中：

vim repo

获得分组的命名空间id，比如为9，使用gitlab的API批量创建分组命名空间ip为9的仓库：

```
for i in `cat repo`; do
curl --request POST --header "PRIVATE-TOKEN: 9koXXXXOOOOOs5t" \
  --data "name=${i}&namespace_id=9" http://IPofgitlab/api/v4/projects;
done

```
## 5 repo库迁移及批量创建gitlab库

### 取得外部服务器的镜像库 （your_url 为外部库地址）

因为整体代码由200多个代码库组成,直接使用repo获取镜像库,追加--mirror参数，将下面标红字体替换为你的repo路径

```
mkdir repo_mirror

cd repo_mirror

```
初始化版本库：

repo init --mirror -u IPofgitlab/manifests.git--repo-IPofgitlab/repo.git -mmanifest.xml

同步：

    repo sync

同步完成后，将在下面将所有repo代码库的镜像库放置到repo_mirror目录下。

取得所有代码库文件名,方便批量创建project

repo_mirror目录执行：ls >../projects_list.txt

打开projects_list.txt将projects_list.txt 内所有.git 替换为 \ 

    perl  -i -lpe '#\.git# \#'  projects_list.txt

````
替换前：

pro1.git
pro2.git
pro3.git

替换后：

pro1 \
pro2 \
pro3 \
````
使用gitlab api批量在指定groups下创建project。

浏览器访问http://IPofgitlab/api/v4/projects 查找需要groups的ID ，对应为namespace的id，取得本次创建需要的组id值为9。

或者通过namespace接口获取其list

    http://IPofgitlab/api/v4/namespaces

创建gitlab的projects

````
    projects="pro1 \
    pro2 \
    pro3 "
     
    for project in $projects
    do
        info="name=$project&path=$project&wiki_enabled=no&public_jobs=true&public=true&namespace_id=2&default_branch=master&private_token=your_private_token"
        curl -d $info "http://your_ip/api/v3/projects"
    done

````
执行结束后到登录到gitlab，确定库创建完成。

将镜像库上传到本地gitlab上

```
    dir_name="$PWD"

    all=`ls ${dir_name}`
    for i in $all
    do
    if [ -d $i ]
    then
    echo "[$i]"
    cd $i
    git push --mirror git@your_ip:git/$i
    cd ..
    fi
    done 

```
执行脚本，确定版本库push无误。


可以使用本地库进行repo代码取得 （your_local_url为本地gitlab库地址）

    repo init -u your_local_url/manifests.git--repo-urlyour_local_url/repo.git -mmanifest.xml

同步：

   repo sync

## 6 gitlab数据目录迁移

gitlab代码数据默认目录：/var/opt/gitlab/git-data/repositories

数据目录，挂一个大磁盘到目录[目录按照你的喜好命名]

````
makedir  -p /data/gitlab-data
mount sdb  data/gitlab-data
````

### 操作步骤：

++ 停止相关数据连接服务

    gitlab-ctl stop unicorn   
    gitlab-ctl stop sidekiq

++ 数据迁移

    cp -r -p /var/opt/gitlab/git-data/repositories/ /home/gitlab-data/

这里CP一定要加上-p参数，不然会导致权限问题

++ 更改配置 /etc/gitlab/gitlab.rb

    vim /etc/gitlab/gitlab.rb

++ 指定数据目录

    git_data_dir "/home/gitlab-data"

++  使配置生效

     gitlab-ctl reconfigure

++  重启gitlab

     gitlab-ctl restart

## 7  git & gitlab常见错误

### 7.1 错误502解决办法

8080 端口冲突

原因：由于unicorn默认使用的是 8080 端口。

解决办法：打开 /etc/gitlab/gitlab.rb ,去掉 # unicorn['port'] = 8080 的注释，
将 8080 修改为 9090 ，保存后运行 sudo gitlab-ctl reconfigure 即可。

如果内存较小，服务器硬件资源太小

则服务启动过程较慢，在此期间启动则会导致502，等一段时间，服务启动后，在访问就ok了

### 7.2 git SSH认证问题

#### 很简单的，证书添加后：

+ ssh -T git@IP测试，测试成功说明证书添加成功。

比如测试github: ssh -T git@github.com

    Hi bollwarm! You've successfully authenticated, but GitHub does not provide shell access.

如果有问题，则你证书生成或者添加有问题。你可以ssh -vv -T git@IP 来看详细ssh通讯过程和报错，找出问题。

主要问题生成不对；文件权限问题（linux下证书权限必须为600，如果是从其他地方复制来的需要注意）；添加公钥时候
添加不是用户账号下证书，而是部署证书（部署证书没有push权限）。 

+ 确保你项目目录下.git/config 文件中远程仓deurl是ss链接：git或者ssh开头，而不是http/https开头

+ https认证时候可以直接在配置url上配上用户名和密码，这样不用每次操作都输入用户名和密码，格式如下：

    https://用户名:密码@IP其他部分。

## 8 其他信息

更多信息欢迎[Pull Request](https://github.com/bollwarm/gitlabFAQ)

gitlab QQ交流群 208598995,交流

本repo会持续更新
