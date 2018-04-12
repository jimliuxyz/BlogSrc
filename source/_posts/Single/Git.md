---
title: Git
date: 2018-04-08 17:34:13
tags: Git
---

![](https://az787680.vo.msecnd.net/user/chris%20chen/007d76f8-8773-444a-858f-608b6ef2fa2e/1461940246_55914.png)

- working space : 工作區 即源碼開發的目錄
- commit history(repo) : 即提交歷史紀錄區
- stage(index) : 緩存區 介於工作區與歷史區之間 add進了stage之後commit才會進repo 而不是讓工作區所有檔案都進repo

- patch : 即commit產生的紀錄點
- master : git最初預設的分支
- origin : 慣用的預設的遠端別名 用來表示某一個遠端的路徑

- HEAD : 指向當下所處的base patch (可以用@符號)
- HEAD^ : 指HEAD的前一個patch 等同HEAD~或HEAD~1
- HEAD@{2} : 歷史紀錄(reflog)的指向簡寫
- detached HEAD : 脫離的HEAD(斷頭) 即HEAD未處於分支的末端(git checkout HEAD^) 之後若再建新的patch將難以合併回分支
- ORIG_HEAD : 當merge或rebase時會留下的一個記錄點 用於合併出錯時的返回點

參考文章
======
[git reflog](https://ithelp.ithome.com.tw/articles/10138150)

查詢
======

```sh
# 列出設定
git config -l
git config --global -l

# 查訊patch
git show HEAD
git log --oneline	# 當下repo版本的歷史
git reflog			# 所有操作歷史 (不會進遠端)

# 比對
git diff	# 比對工作區not staged的檔案
git diff --cached	# 比對工作區與repo

git status
git branch

```

程式碼還原
======
[ref](https://github.com/geeeeeeeeek/git-recipes/wiki/5.2-%E4%BB%A3%E7%A0%81%E5%9B%9E%E6%BB%9A%EF%BC%9AReset%E3%80%81Checkout%E3%80%81Revert-%E7%9A%84%E9%80%89%E6%8B%A9)
還原前須注意當下工作區與緩存區是否有工作尚未commit 可以先commit或stash做暫存patch

## checkout
> 切換patch(branch) 直接影響working space/stage/repo

> 在任何動前 最好都先知道自己在哪個branch下作業 或直接用checkout確保一定在某個branch下
> 可針對個別檔案還原

## reset
[ref](https://dotblogs.com.tw/wasichris/2016/04/29/225157)
> 重置回某patch版本
> 可針對個別檔案還原

> commit流程 : 修改檔案 -> add進stage -> commit進repo
> reset流程  : `(hard)`還原檔案 <- `(mixed)`stage重置回未add <- `(soft)`repo重置回某版本

- 將HEAD移到某patch(即變更repo的版本) 且之後的patch將於數十天後被GC刪除
- reset的所有操作都被記錄在reflog中 最算reset沒有起任何作用
- 最好只在`私有的本地分支`進行 不要影響到遠端及其他開發者的base
- 模式
  - soft : 保留當下的stage與working space
  - mixed(預設) : 保留當下的working space 但stage退回(如同未add)
  - hard : 完全重置

## revert
> 將某個patch逆向重做後當成一個新的patch 如同撤銷某個patch 雖會多一個patch但不會影響既有的history


暫存patch
======
當工作到一半若要切換到其他patch或branch 但又不想commit當下的修改時 可以此法暫存之後再取回

## stash
> 將`工作區與緩存區`暫存起來 再硬重置回HEAD
> pop動作如同cherry-pick也會有conflict 所以要先回到正確的patch再pop
```sh
git stash	# 暫存
git stash save "mywork"	# 給定暫存訊息	
git stash list
git stash pop stash@{1} # pop出來後就移出list了
```

合併
======
[ref](https://zlargon.gitbooks.io/git-tutorial/content/branch/rebase.html)
首先要切到要進行合併的分支

## cherry-pick
> 將其他分支中想要的patch`複製`成新的patch加到當下分支

## rebase
> 將該分支的起點(交叉點)換到新的patch或branch (以cherry-pick實現)
> 達人推薦的合併方式

## merge --no-ff
> 將兩個分支合併產生一個merge patch
> 被併的分支仍能保留末端節點

#### Fast-Forward
指合併的兩個分支原本就重疊在一起 此時合併不需產生merge patch (就看不出分支的起點了)

```sh
# rebase或merge出錯時 可以此返回合併前狀態
git reset --hard ORIG_HEAD
```

解衝突
======
[ref](https://zlargon.gitbooks.io/git-tutorial/content/patch/cherry_pick_conflict.html)

```sh
# 完成解衝突
git cherry-pick --continue

# 取消pick
git cherry-pick --abort
```

同步遠端分支
======
要push前或每日工作前先pull確保本地端base與遠端相同 一方面及時察覺新版本的變動 二方面確保push時順利

## fetch
> 下載

## pull
> 下載並merge

## pull --rebase
> 下載並以rebase合併