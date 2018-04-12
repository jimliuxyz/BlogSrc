---
title: Git
---

[***Git的好習慣](https://www.jianshu.com/p/a980fc1eb626)

[Git版本控制视频教程](http://v.youku.com/v_show/id_XNzgwOTk0NDQw.html?f=22842023)
## SVN(subversion) 與 Git

cvs:集中式

svn:集中式:所有版本都在svn server上,client端僅存有某一特定版本,所以版本切換時很慢,server一掛就全部死光. 紀錄diff patch,每個版本透過patch還原,所以換版本時也很慢.

git:分散式:本地端含有所有版本,遠端僅進行與各個用戶端同步. 紀錄個別檔案

---

```
//產生公私鑰
ssh-keygen -t rsa -b 4096 -C jimliuxyz@gmail.com

//將pub key貼到gitbut網站的setting>SSH keys>new key

//針對目錄設定就不要加global
git config --global user.name jimliuxyz
git config --global user.email jimliuxyz@gmail.com

git config --list

git remote add origin https://github.com/jimliuxyz/wovo.git
```

ssh機制與私人repo較有關聯
[ssh config](http://riny.net/2014/git-ssh-key/)

[常用指令](https://blog.exfast.me/2016/05/git-instructions-instructions/)

```
//取得remote repo變數設定
git remote -v

//取得所有branch與當下使用的branch (與本地追蹤狀況)
git branch -a

//取得遠端分支資訊
git ls-remote

//取得當下branch名稱與其版本狀態
git status
```

```
//測試
git clone ... //會自建一個目錄
touch file.txt
git add file.txt
git commit -a -m comment...
git push
```

```
//建立project (wovo)
git init
git add <...>
git commit -m "first commit"

//建立origin(remote repo var)並對應到remote url
//origin是慣用預設對remote repo的命名
git remote add origin https://github.com/jimliuxyz/wovo.git

//將本地版本推送到 origin/master (remote repo/branch)
//master是慣用預設對主要branch的命名
//-u | --set-upstream設定master的remote repo是origin
git push -u origin master
```

```
//建立新分支newfeature,從master分出
git branch newfeature master

//切換分支
git checkout newfeature

//推送分支到remote
git push -u origin newfeature

//之後的push動作
git push origin

//本地端分支:遠端分支
git push -u origin master:master
```

```
//更新local repo,但不合併
git fetch

//合併
git merge origin/master

// (fetch + merge)
git pull

```

### 重置.git目錄
```
rm -rf .git
git init

//重新commit
git commit -a -m "first commmit"

//增加遠端origin路徑
git remote add origin https://github.com/jimliuxyz/...

//push到遠端origin
git push -u origin master
```

[參數-u](https://zlargon.gitbooks.io/git-tutorial/content/remote/upstream.html)
設定本地端master追蹤遠端origin/master,設定一次後以後可以直接使用git push上傳

### 標籤
感覺標籤像一個release的紀錄點,至少在github上確實會產生release的內容

```
git tag -a 1.0 -m "first release"

git push origin 1.0
```