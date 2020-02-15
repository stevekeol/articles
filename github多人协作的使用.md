# 多人协作中git和github的使用


### 本文解决的问题
+ 如何创建一个Organization?
+ 如何创建一个开源共建项目？
+ 如何创建master和development两个分支?
+ 在每个分支上如何工作？何时切换分支？
+ 如何向该项目组中添加开发成员，这些成员拥有哪些权限？
+ 如何code Review成员提交的代码？
+ 如何合并成员提交的代码？
+ 成员如何建立自己的分支？
+ 成员如何提交自己的代码？
+ code review的流程应该在什么操作节点？使用什么工具？有哪些标准？
+ 本地协作者/开发者应该在何时同步远程仓库的代码？
+ 查看代码差异的diff用法
+ 工作取半成品时需要紧急切换分支处理别的任务的处理（如fix bug等）

### 一. 创建Organization
##### 简介
organization是拥有仓库的用户的集合，其具有一个或多个所有者，他们拥有organization的管理权限。创建组织时，它不会有任何的关联仓库。

##### 创建
settings -> Organizations -> new organization

### 二. 创建多人协作开源项目
##### 创建repository
repository -> new -> ...

##### 添加小组成员
settings -> Manager access -> Invite a collaborator
小组成员在github对应邮箱中确认邀请链接。

+ 确认后，小组成员在其github均可看到该repository，无须额外fork;
+ 假如该repository是private，小组成员也可看到(settings -> repositories最底部可看到);

### 三. 创建分支

##### 下载主分支代码
+ 复制repository的路径
+ 在本地合适目录下（协作者应该git clone自己仓库中的repository）：
```
git clone git@github.com:stevekeol/rrg-pro.git
```
这样将主分支的代码下载到本地，并自动建立了与远程仓库的连接

##### 创建本地分支
(在该项目文件夹下)
+ 查看所有分支（本地+远程）
```
git branch -a //本地有master分支(本地默认分支)，远程也有master分支
```
+ 创建分支
```
git branch  xxx //xxx为分支名称
```
+ 删除分支
```
git branch -D xxx

```

### 四. 在各分支上的工作

+ (写代码前)切换到某一分支
```
git checkout xxxc  //工作分支建议: git checkout work
```
+ (写好代码，确定没问题后)添加修改代码到缓存
```
git add .
```
+ (写好代码，确定没问题后)添加提交代码的备注
```
git commit -m 'xxx' //xxx为备注
```
+ (提交代码前)切换到本地主分支
```
git checkout master //在切换分支前，一定确保当前分支的修改已经提交或者缓存。
```
+ (提交代码前)获取远程最新的修改(即将远程仓库可能有变动的代码pull到本地仓库干净的主分支master上)
```
git pull
```
+ (提交代码前)回到工作分支，并修复冲突(如果有冲突)
```
git checkout work
git rebase master //相对于master执行'变基'操作，将工作分支上的commit缀连到master分支上。
```
+ (提交代码前)回到主分支
```
git checkout master //在切换分支前，一定确保当前分支的修改已经提交或者缓存。
```
+ (提交代码前)合并工作分支的修改，此时不会产生冲突
```
git merge work //将work分支的修改合并到master分支上
或者: git merge --no-ff develop //--no-ff指不使用默认的fast-farward merge
```
+ (提交代码)提交到远程分支
```
git push
```

> 以上操作流程的好处是，远程主分支的历史永远是线性的。每个协作者在本地分支解决冲突，不会在主干上产生冲突。 

+ 提交代码到指定分支
```
git push origin xxx //xxx为要提交代码的分支
//git push origin <local_branch>：<remote_branch> //可将本地任一分支推送到远程任一分支。最好是永远只同步本地的master分支，以避免master落后于远程分支。
//直接使用git push。必须是当前分支已经有关联分支，且两者同名。
//
```

> [疑问]: 在本地localhost分支上，写好代码后，意图提交代码到该分支，git push origin localhost。
结果却是：
remote: Create a pull request for 'localhost' on GitHub by visiting:
remote:      https://github.com/stevekeol/rrg-pro/pull/new/localhost。由于此时远程仓库默认分支是development，因此仓库自动提醒并创建了pull request。
仓库开启了一个新的PR,等待仓库管理员merge该PR。合并成功后。
+ 分支同步代码(将远程某个分支的代码同步到本地某个分支上)
```
git pull origin <remote_branch>：<local_branch>
//如： git pull origin master:master; git pull origin develop:master; 假如直接使用git pull，那么必须是本地分支已经和默认的远程分支建立了关联。
//建立关联：在本地合适分支下，git push --set-upstream origin develop/master/xxx
```

> [疑问]1. 是在某一分支下，添加修改代码到该分支缓存，再push到该分支？ 2.还是工作区是独立于多个分支的，添加修改代码到独立的缓存，再将该缓存提交到指定分支？ 个人感觉后者更合理

> [注]本地可以创建多个分支。假如当前分支已经可用，现在需要增加一个新的不确定是否可行的功能，那么新开一个分支，修改代码，假如不成功，则把该分支删掉；假如成功，则可以将该分支合并到主分支上。

### 五. 本地合并分支（具体流程参考上面）
分支上代码写好并确认没问题后，可以进行分支的合并。(假如把本地test分支合并到本地master分支上)
+ 查看所有分支（本地+远程）
```
git branch -a
```
+ 切换到master分支
```
git checkout master //在切换分支前，一定确保当前分支的修改已经提交或者缓存。
```
+ 合并分支
```
git merge test
```
+ 将master分支上的内容提交到线上
```
git push
```
> [疑问]是不是各个分支创建时都应该从本地master创建？各个分支写好代码后是不是应该立即更新到master？假如有master,branch_a,branch_b三个分支，后两者均从属于master，假设branch_a有一部分修改，branch_b有一部分修改，那么其中一个分支提交时，是不是应该先同步master，再提交本分支的修改代码？
【回答】:参考本文临时性分支！

### 六. 远程仓库创建master和development/dev分支？
##### 背景原因
假如远程仓库有master和develop两个分支，master作为一个稳定版分支，可用于直接发布产品，日常的开发则push到develop分支。
建议代码库的常设分支只有：master和develop;
临时性的分支（使用完应该立即删除）:
+ feature（功能）:可以采用```feature-*```的形式命名；相对于develop创建分支！
+ release（预发布）：可以采用```release-1.2```的形式命名；相对于develop创建分支！
+ fixbug（修复bug）：可以采用```fixbug-xxx```的形式命名；相对于master创建分支！

> [疑问]: 本地各成员开发，是否要从dev分支拉取并创建一个本地分支，然后在这个分支的push动作是默认推到远程dev分支上？、

> [解释]: 
##### 默认分支
+ 默认分支是仓库中的基本分支，除非另外指定，否则将基于默认分支自动生成所有拉取请求和代码提交。
+ 创建repository时，默认分支是master

##### 仓库创建新分支
+ 仓库主页 -> branch:master按钮 -> 输入新的branch名称即可创建新的分支 -> 回车

##### 仓库设置默认分支
settings -> Branches -> Default branch -> 选择默认的分支 -> update即可。

##### 默认分支development如何同步到master？


### 七. 本地同步远程仓库的代码
##### 查看远程分支
```
git remote -v //fork某个项目后，至少可以看到一个origin的远程库，Git默认使用该名字标识被fork的仓库
```
##### 本地同步远程仓库最新版
```

```


### 八、Code Review的时机和节点

+ 【自我理解】？
远程代码库常设分支:master和develop
本地各个协作者提交的代码，(1)本地确定无误后，提交到develop; (2)提交到develop的时候github的action功能给予初步的代码审核; (3)develop分支管理员利用工具code review该分支的代码，没问题后向远程仓库master发起pull request; (4)远程仓库master管理员审核该pull request，没问题后merge。并更新develop。
+ 【自我理解】？
本地新开一个review分支，拉取develop分支代码，在本地review。再删除该分支。那么如何compare code呢？
+ 【他人方案】？
使用gitlab，而非面向开源的github。后者对开发者开放了所有的代码。前者可以设置权限范围。且有代码审查工具？
+ 【他人方案】 (最佳方案)
利用github的pull request和action。
不直接对master分支进行提交或合并，而是通过提交 Pull Request，并且进行审阅和讨论后最终合并到master。
通过Pull Request @提及团队成员 让对方审阅自己的代码，指定成员跳转到指定分支后可以对代码进行评论，提出改善建议。并且帮助改善逻辑及缺陷。还可以通过触发器触发CI(持续集成服务器)自动进行集成测试。
通过ci确保所有测试用例都通过测试，并且有2名以上的审阅人对代码进行审查后才能进行最终合并。在此之前，通过讨论和评论的方式对代码进行不断的重构和改善。


### 九. 成员如何提交自己的代码（？）
##### 协作者在本地直接向远程仓库提交
```
...
```
##### 协作者先提交到自己fork的github仓库，再在自己的Github仓库上new pull request
```
...
```

### 十. 工作取半成品时需要紧急切换分支处理别的任务的处理
##### 场景
开了新分支开发新的功能，此时有紧急的bug需要修复，此时进行代码提交是没有实际意义的commit。
##### 条件
代码没有commit（即使执行了add也可以）
##### 操作
```git stash```
将当前分支所有没有commit的代码暂存起来
+ 暂存起来后git status会发现当前分支很干净
+ git stash list查看暂存区记录
+ 回到该分支时，git stash pop 还原代码并删除该stash记录
+ 验证是否清除记录：git stash list
+ 清除所有的stash记录: git stash clear

---
### END. 备注
###### 1.rebase和merge的区别
[参考文章-merge和rebase的区别](https://juejin.im/post/5af26c4d5188256728605809)
![merge图例](https://user-gold-cdn.xitu.io/2018/5/9/16342fbc3161f98e?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)
【merge】
+ 特点 | 将一个分支(交叉)合并到另一分支
+ 操作 | 在master分支下，```git merge feature``` (将feature分支合并到master分支)
+ 合并 | merge有一次合并的历史记录
+ 冲突 | 合并的时候只需处理一次冲突（？）
+ commit | 合并后的两分支的所有commit会按照时间先后顺序排列
+ 使用场景 | 当需要保留详细的合并信息时，建议使用```git merge```，特别是需要将分支合并入master分支时


![rebase图例](https://user-gold-cdn.xitu.io/2018/5/9/16342fc20a6c6c8f?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)
【rebase】
+ 特点 | 改变当前分支从master上拉出分支的位置
+ 操作 | 在feature分支下，```git rebase master```（将feature分支相对于master分支'变基'）
+ 合并 | rebase没有合并的历史记录
+ 冲突 | 可能会多次解决同一个地方的冲突（？）
+ commit | 合并后的两分支的所有commit会按照时间先后顺序排列
+ 使用场景 | 当发现自己修改某个功能，进行频繁的git commit提交时，发现过多的提交信息是没有必要的时候，用```git rebase```

###### 2.创建一个功能分支
[参考文章-阮一峰-git分支管理策略](https://www.ruanyifeng.com/blog/2012/07/git.html)
```
git checkout -b feature-x develop //作何解释？
```
###### 3.git pull和git fetch的区别

###### 4.疑惑：
假如远程仓库设置默认的分支是develop，那么本地fork下来时，本地默认的主分支是develop，这样会造成语义混乱(因为理想中本地应该是master、develop、其它临时分支)。
理想的模式是什么样呢？


###### 5.github上工作流工具
[github工具市场](https://github.com/marketplace)

###### 6.pull request规范模板

```
### 这个变动的性质是

[x] 新特性提交
[ ] 其他改动（日常 bugfix、文档、站点改进、分支合并等，不需要填写余下的模板）

### 需求背景
  
> 1. 描述相关需求的来源。
> 2. 要解决的问题。
> 3. 相关的 issue 讨论链接。
  
### 实现方案和 API
  
> 1. 基本的解决思路和其他可选方案。
> 2. 列出最终的 API 实现和用法。
> 3. 涉及UI/交互变动需要有截图或 GIF。
  
### 对用户的影响和可能的风险

> 1. 这个改动对用户端是否有影响？影响的方面有哪些？
> 2. 预期的 changelog 要怎么写？
> 3. 是否有可能隐含的 break change 和其他风险？

### 请求合并前的自查清单

- [x] 文档已补充或无须补充
- [x] 代码演示已提供或无须提供
- [x] TypeScript 定义已补充或无须补充
- [x] Changelog 已提供或无须提供

### 后续计划

> 如果这个提交后面还有相关的其他提交和跟进信息，可以写在这里。
```
###### 7.新特性提交规范
![](https://user-images.githubusercontent.com/227713/50533140-b7cce280-0b5e-11e9-99f4-a35958020e52.png)
###### 8.commit message规范模板
```
【格式】
type: description

【type 类型】
feat: 新的功能
fix: 修复 bug
docs: 只是文档的更改
style: 不影响代码含义的更改 (例如空格、格式化、少了分号)
refactor: 既不是修复 bug 也不是添加新功能的代码更改
perf: 提高性能的代码更改
test: 添加或修正测试
chore: 对构建或者辅助工具的更改，例如生成文档
test: 增加测试代码
revert: 撤销上一次的commit
build: 构建工具或构建过程等的变动，如：gulp 换成了 webpack，webpack 升级等

【description】
description 是对本次提交的简短描述。
不超过50个字符。
推荐以动词开头，如： 设置、修改、增加、删减、撤销等
```
###### 9.美化commit日志
```
git config --global alias.lg "log --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit --date=relative"
//
//以后查看日志：git lg即可
```
###### 10. 综上，多人协作流程
[多人协作流程](https://www.cnblogs.com/onelikeone/p/6857910.html)