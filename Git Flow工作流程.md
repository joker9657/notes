# Git Flow
多人协作开发场景，需要一套工作流程规范。在 2010 年的時候，就有人提出了一套流程，或說是訂了一套規矩讓大家可以遵守：[A successful Git branching model](http://nvie.com/posts/a-successful-git-branching-model/)

// TODO：填充内容-2019-11-14
Git 相对于其他版本控制工具的一大特点：**分支管理**\
Git Flow这套流程主要依赖分支进行操作。
长期存在的分支：master、develop(dev)
临时分支：feature、release、hotfix

## master分支
主分支，用于产品发布，即稳定的线上版本。

## develop分支
开发分支，用于日常开发，所有开发的基础分支，当要新增功能时，所有的feature分支都应基于此分支。

## feature分支
功能分支，正如上面所说，开发新功能时，从dev分支切一个新分支，完成后再合并回dev分支。

## hotfix分支
紧急修复分支，当线上版本出现紧急问题时，会从master分支切一个hotfix分支出来进行处理，修复完成后再合并回去，同时会合并至dev分支。

## release分支
发布分支，主要用于发新版前的测试，可以在此分支上修复bug，然后同时合并到master和develop分支

### 基本流程图如下
![git flow](https://i.loli.net/2019/11/17/uUZtp1D7rQ9On3q.jpg)

参考文章：\
[Git Flow 入门](https://juejin.im/post/5c3e9b6df265da616c65d685)\
[Git Flow 是什麼？為什麼需要這種東西？](https://gitbook.tw/chapters/gitflow/why-need-git-flow.html)\
[Understanding the GitHub flow](https://guides.github.com/introduction/flow/)\
[Git-Flow 规范和指令](https://segmentfault.com/a/1190000020885625)\
[Gitflow Workflow](https://www.atlassian.com/git/tutorials/comparing-workflows/gitflow-workflow)
