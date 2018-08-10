
## repo库迁移及批量创建gitlab库

### 取得外部服务器的镜像库 （your_url 为外部库地址）

因为整体代码由200多个代码库组成,直接使用repo获取镜像库,追加--mirror参数，将下面标红字体替换为你的repo路径

```
mkdir repo_mirror

cd repo_mirror

```
1.初始化版本库：

repo init --mirror -u IPofgitlab/manifests.git--repo-IPofgitlab/repo.git -mmanifest.xml

同步：

     repo sync

2.同步完成后，将在下面将所有repo代码库的镜像库放置到repo_mirror目录下。

3.取得所有代码库文件名,方便批量创建project

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
4.使用gitlab api批量在指定groups下创建project。

浏览器访问http://IPofgitlab/api/v4/projects 查找需要groups的ID ，对应为namespace的id，取得本次创建需要的组id值为9。

或者通过namespace接口获取其list

    http://IPofgitlab/api/v4/namespaces

5.创建gitlab的projects

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
6.执行结束后到登录到gitlab，确定库创建完成。

7.将镜像库上传到本地gitlab上

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
8.执行脚本，确定版本库push无误。


9.可以使用本地库进行repo代码取得 （your_local_url为本地gitlab库地址）

    repo init -u your_local_url/manifests.git--repo-urlyour_local_url/repo.git -mmanifest.xml

10.同步：

   repo sync

