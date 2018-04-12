---
title: __
---


View

 <-> DataService(ServiceCache)
 
 <-> DataServer(DataAccess/DBCache/DBAccess)

>主要分成DataService與DataServer兩大塊

# DataService
>提供資料服務

- 向View或任何需要資料者提供資料服務
- 貼近應用的資料結構
- 提供有邏輯功能性的操作 (例如新增使用者)

## ServiceCache
>存放使用中的DataService 避免資料服務四散各處

- 透過ServiceCache存取已建立的資料服務 確保同一來源的資料僅有一個服務 便於資料同步
- 避免無限制的cache可用WeakRef(weak value)控制 但js只有WeakMap.
    只能用 counter 或 時間+數量來workaround

# DataServer
>處理資料讀寫 (僅是一個目錄)

## DataAccess
>提供資料讀寫介面 處裡讀寫機制(本地/遠端)

## DBCache
>本地資料快取 用於離線操作

## DBAccess
>遠端資料庫讀寫