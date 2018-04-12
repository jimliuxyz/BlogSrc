---
title: OAuth
date: 2018-04-06 12:13:59
tags: OAuth
---

[youtube](https://www.youtube.com/watch?v=wgU4pApYOjU&index=46&list=PLHOqLxXumAI-hCYIzsnn6LCmZHzEStuuh)

## 意義
user => client app(consumer) <=> server(service provider)

- app取得provider的service
- user不必向app透露帳號密碼

## 過程
![](https://docs.microsoft.com/en-us/vsts/integrate/get-started/authentication/_img/oauth-overview.png)

1. client請求request token
2. client將主導權導向server
3. server向用戶詢問是否授權(導向server提供的畫面)
4. server將主導權導向client
5. client請求access token

## Signature 簽名
>server透過signature驗證用戶身份

Signature = Signature Base String (AES with) secret key

Signature Base String大概是由url的特徵產生

secret key僅有加密者與server知曉,故透過Signature就能讓server辨試身份

## Request Token
>與provider對話權的令牌
>>初步驗證app的權限後取得

client提供app key與signature向server請求request token

## Access Token
>存取用戶資料授權的令牌
>>驗證用戶身份與同意授權項目後取得



