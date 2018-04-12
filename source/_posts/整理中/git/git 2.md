---
title: __
---

動作循環:
修改檔案 => 加入stage (git add) => 提交( git commit )=> 繼續修改其他檔案

每次'提交'後若再修改檔案,就需要再'加入stage'
'加入stage'的用意相當於決定稍後要'提交'的檔案

stage area相當於是user folder與repository間的一個'等待提交暫存區'

---

重新取回最後的commit
git checkout file.js
git checkout *

---

git rest --soft ... 取消commit,不回朔檔案
git rest --hard ... 取消commit,也回朔檔案

---

git可看成單機版,與gitHub結合變成多人開發版

git pull 將遠端最新commit抓回
git push 將本地端最新commit上傳

git rebase 將branch的master base更新到最新的master (可避免merge時的碰撞)