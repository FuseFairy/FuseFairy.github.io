---
title: Discord Bot AutoFx - 自動產生Embed
categories: Discord Bot
tags: [Discord, python, Twitter(x)]
photos: https://i.imgur.com/Le4bHGQ.png
date: 2023-12-23 14:50:44
---
上次介紹了[Tweetcord 部屬到 fly.io 的方法](https://fusefairy.github.io/2023/11/20/Discord%20Bot%20Twitter%E6%8E%A8%E6%96%87%E9%80%9A%E7%9F%A5%20-%20%E5%85%8D%E8%B2%BB%E9%83%A8%E7%BD%B2%E5%88%B0fly.io/)，現在再來推薦一個能自動把twitter連結生成Embed的方式，一樣是[Tweetcord](https://github.com/Yuuzi261/Tweetcord)作者寫的。

AutoFx
===
Repository Link：https://github.com/Yuuzi261/AutoFx

![demo](https://i.imgur.com/cnzWJYi.gif)

設置
===
如果不想自己設置，Repository Link 裡作者有提供bot邀請連結，可以直接用。

上篇教學結尾說到Tweetcord是cog架構，所以擴充性不錯，我們現在就來把AutoFx的功能合併進來。

## step 1
打開上次製作的Tweetcord專案資料夾，在cogs資料夾內新增一個新的python檔案，用來放AutoFx的code。

![file](https://i.imgur.com/xDYq8TL.png)

## step 2
打開剛新增的python檔，一樣用記事本編輯即可，將以下連結的code複製進去。
> https://github.com/Yuuzi261/AutoFx/blob/main/cogs/event.py

![file2](https://i.imgur.com/IZCwuNG.png)

## final step
這邊假設是要更新到fly.io上，所以以一樣的方式啟動cmd，直接輸入`flyctl deploy`就會自動更新到fly.io上了。

結語
===
應該是很簡單啦! ~~寫來純粹來水一篇的~~

有其他discord bot相關功能想知道如何製作的或是有其他問題都可以提出。