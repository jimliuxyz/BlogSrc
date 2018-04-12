---
title: ShellCmd
date: 2018-04-04 16:04:44
tags:
categories: Linux
---

[ref](https://www.openfoundry.org/foss-programs/8628)
[ref](http://rodney2009.pixnet.net/blog/post/21465471-%E5%B7%A5%E4%BD%9C%E5%B8%B8%E7%94%A8%E5%88%B0%E7%9A%84linux%E6%8C%87%E4%BB%A4)


```sh

# 搜尋執行檔路徑 (依環境path)
which git
whereis git

# 依檔名尋找檔案
find / -name git

find grep awk

# 資料夾中搜尋文件內容
grep -r '導航' ~/GoogleDrive/

grep -r --include="*.java" "service" .

```