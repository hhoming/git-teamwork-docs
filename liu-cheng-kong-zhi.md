# 3.制定工作流
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;现在需要定义一套工作流来对项目进行管理呢，这一节我们会给出实际方案并结合Git进行实际应用演示。在本章节，选用了Gitlab推荐的工作流模型--Gitlab Flow，结合了gitflow与GitHubFlow的Gitlab Flow针对于持续发布与版本发布型项目都有相应的分支策略，来看看在不同的分支策略下工作流程的流转。

## 3.1. 持续发布

适用于web等可以无缝更新的项目。

### 3.1.1 分支策略


| 分支类型 | 类型名称 |	创建自 | 合并至 | 
| ------ | ------ | ------ | ------ | 
| feature|  功能分支 | master | master |  
| fix | 修复分支 | master | master |  
| **master** | 开发分支 | - | -|  
| **pre-production** | 预生产分支 | master | production | 
| **production** | 生产分支 | pre-production| - |


**临时分支(_开发完成会被删除_)**：
feature：功能分支，用于新功能的开发，建议以issue/feature/name命名
fix：修复分支，用户bug的修复，建议以issue/fix/name命名


**固定分支**：
master：开发分支，用于发布到测试环境，上游分支为 feature 和 fix，该分支为受保护分支
pre-production：预发分支，用于发布到预发环境，上游分支为 master
production：生产分支，用于发布到正式环境，上游分支为 pre-production


### 3.1.2 流程图
**（工作流程图）**
![](/assets/6e7e60de-e689-3380-b866-9d438458b450.png)

### 3.1.3 Git命令应用
克隆项目到本地

git clone git@example.com:project-name.git


检出分支

git checkout -b $issue-feature-name


提交并push到GitLab仓库

git commit -am "My feature is ready"
git push origin $issue-feature-name


运行GitLab CI
在GitLab上创建一个Merge Request
项目管理者进行代码审查，合并到master

运行第二次GitLab CI
进行产品测试
将master分支合并到pre-production

运行第三次GitLab CI
进行产品测试
将pre-production分支合并到prouction，并且为prouction打上tag，保持prouction与线上代码一致

### 3.1.2 命名规范




## 3.2 版本发布流程

适用于发布周期长、有版本规划的项目，传统IT项目开发十分适用。

### 3.1.1 分支策略

| 分支类型 | 类型名称 | 创建自 | 合并至 |
| ------ | ------ | ------ | ------ |
| feature | 功能分支 | master | master |
| fix | 修复分支 | master | master |
| **master** | 开发分支 | - | stable |
| **stable** | 稳定分支 | master | - | 

**临时分支(_开发完成会被删除_)**：
feature - 功能分支，用于新功能的开发，建议以issue/feature/name命名
fix - 修复分支，用户bug的修复，建议以issue/fix/name命名

**固定分支**：
master - 开发分支，用于发布到测试环境，上游分支为 feature 和 fix，该分支为受保护分支
stable - 用于发布稳定版本到预发环境，上游分支为 master，建议以version-stable命名，该分支要尽可能晚的创建，每次更新此分支都要更新一个小版本号

### 3.1.2 流程图
**（工作流程图）**


### 3.1.3 Git命令应用

**<<<< 开发环节 >>>>**

1.克隆项目到本地

    
    git clone git@example.com:project-name.git


2.检出分支

    git checkout -b $issue-feature-name

3.提交并push到GitLab仓库

    git commit -am "My feature is ready"
    git push origin $issue-feature-name

4.项目管理者进行代码审查，将各个功能分支合并到master


5.进行项目测试

6.将master分支合并到stable，如果是新版本，则创建新stable分支
，并为当前stable创建tag

**<<<< 测试环节 >>>>**


**<<<< 部署环节 >>>>**

**<<<< Bug修复环节 >>>>**









### 3.1.2 命名规范








## 3.1. Git命令的工作流程操作
1.别名配置(后续步骤基础)
git config –global alias.st status #设置完后 git st = git status
 git config –global alias.ci commit
 git config –global alias.co checkout
 git config –global alias.br branch
2.Clone（下载）项目
git clone project-url #按照gerrit上的提示操作即可
3.查看状态
git status #最常用的操作，会给出明确的提示
4.查看分支（tag）
git br #查看本地分支，当前分支颜色是绿色，前面有个 *
 git br -a #查看所有分支，其中的远程分支可用户切换分支使用
5.切换分支
git co branch-name #branch-name指已经存在的分支名称
6.新建分支
git br branch-name #从当前分支的当前commit新建一个分支
 git co branch-name #切换到新建的分支，切换的时候会携带未提交的修改
7.拉取远程修改
git pull #会拉去包括tag在内的信息
8.提交修改
git add . –A #将当前目录下的所有更改都添加到缓存区
 git ci #将缓存区的更改提交到本地库
9.打标签
git tag tag-name #在当前commit上打一个tag
 git tag tag-name commitId #在commitId上打一个tag
10.推送修改
git push #将当前分支的更改推送到远程分支
 git push –tags #将当前分支及所有新打的tag推送到远程服务器
11.合并分支
git co master #将当前分支切换到master
 git merge dev #将dev分支的修改切换到master
12.删除远程分支或者tag
git push origin –delete branch-name #删除远程分支，需要gerrit权限
 git push origin --delete tag tag-name #删除远程tag，需要gerrit权限
13.重写上一次注释
 如果提交后立即发现注释写错了，即只需要重写上一次的注释，在这些如下命令；
 git ci –amend
14.重写前几次注释
慎用git reset操作，尤其是reset已经推送到远程服务器上的commit。
如果发现之前的几次提交注释写错了，找到写错注释的前一个commitId，假设为abcdefg，执行
git reset –-soft abcdefg #类似将abcdefg之后的git ci命令取消，并且将git add命令合并得到的结果。然后执行
 git ci #和正常提交一样写注释。

15.取消发布（慎用！配合操作！）
场景：1.0.0已经发布，计划功能F1，F2，F3，以及不过B1，B2需要在1.1.0发布，
分支F1，F2，F3，B1，B2都已经合到dev上了，并且已经发布了1.1.0-b1，1.1.0-b2两个beta版。
此时F1功能因种种原因需要取消，此时需要执行如下操作：
 

git co dev #切换到dev分支。
 git tag -d 1.1.0-b1 #删除本地tag 1.1.0-b1
 git tag -d 1.1.0-b2 #删除本地tag 1.1.0-b2
 git reset –hard 1.0.0 #将所有合并撤销，恢复dev到1.0.0的状态。
 git merge F2 #重新合并F2
 git merge F3 #重新合并F3
 git merge B1 #重新合并B1
 git merge B2 #重新合并B2
 git tag 1.1.0-b1 #重新打tag 1.1.0-b1
 git push origin –delete tag 1.1.0-b1 #删除远程分支1.1.0-b1
 git push origin –delete tag 1.1.0-b2 #删除远程分支1.1.0-b2
 git push -f origin dev #使用本地dev分支强制更新远程dev分支
 git push -tags #推送本地tag到远程服务器
 
## 3.2. 工作流规范
能够独立部署的工程使用master和dev两个主干分支，master分支对应线上版本，dev分支对应开发版本； 作为依赖库使用的工作只需要一个master分支即可。master和dev分支只能由开发组长合并及提交，其余人员需要在各自分支上进行开发工作。
GIT提交尽量遵循单次提交的代码是对一个完整但是影响尽量小的功能的修改，不要把对几个功能的修改混在一起提交。
GIT注释的第一行必须以小写task或者bug，加禅道上对应的任务或者BUG的ID，再加半角冒号开头。 注释最少应该写清楚本次做的修改和达到的效果，越详细越好。 注释首行的字符数应该在15-120个之间。如果注释长度多于120字符，则首行写概要性信息，空一行，然后写详细信息，类似于邮件格式。
分支最好从正式版的tag拉出，正式版tag查看。 分支名字必须有明确的含义，以方便管理。功能上线后，对应的分支应该及时删除。分支名字推荐的命名方法为：以user/开头，加task或者bug，再加禅道上对应的task或者bug的id。其中以user/开头为强制性规定。
如果因注释而提交不成功，请按照常用操作的 重写上一次注释 和 重写前几次注释 的说明进行注释重写


