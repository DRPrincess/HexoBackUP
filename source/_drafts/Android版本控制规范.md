## Git

### 分支：

Git 工作流程采用 Git Flow  工作流

长期分支:  
- master
- develop

短期分支:  
- 功能分支（feature branch）
- 补丁分支（hotfix branch）
- 预发分支（release branch）

|分支|描述|
|---|---|
|develop |主要开发分支 |
|master  |稳定分支，版本上线后推送此分支，并打上版本标签|
|feature/ |功能开发分支,从develop开辟分支，开发完毕，合并到develop 分支，并删除自己|
|hotfix/  |补丁分支 ，在版本发布后出问题，从 master 开辟分支，解决问题后，会合并和 develop 分支 和 master 分支|
|release/  |此分支是为了版本提测后，不影响后续开发。版本提测后 ，从develop开辟分支，在release 分支上做测试修改，完毕之后，会合并和 develop 分支 和 master 分支|

### 标签

版本标签要使用附注标签，说明版本信息：
```
git tag -a <name> -m <message>

```

### 提交
至少一天保证一次提交，防止代码误丢失。
其他规范待制定。

### 推荐阅读

[Git 系列专栏](https://blog.csdn.net/column/details/17721.html)
