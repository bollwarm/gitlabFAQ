
## gitlab 使用API批量创建分组和项目

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
