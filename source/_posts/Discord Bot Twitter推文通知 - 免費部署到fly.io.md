---
title: Discord Bot Twitter推文通知 - 免費部署到fly.io
categories: DiscordBot
tags: [Discord, python, Twitter(x)]
photos: https://i.imgur.com/v7YbXXy.png
date: 2023-11-20 21:27:15
---
自從Twitter執行長Musk修改api後，各大Discord伺服器的Twitter轉發bot應該都無法繼續運作，除非付費，但我們還是有免費的方法可以用，感謝Github上大神弄得逆向工程 [tweety-ns](https://github.com/mahrtayyab/tweety)，接下來介紹使用該逆向工程製作的discord bot。



Tweetcord
===
Repository Link: https://github.com/Yuuzi261/Tweetcord

原理: 每隔一定的時間就去抓Twitter的通知，如果發現設置的轉推對像有新的推文，那就會即時發送到discord頻道。

![Tweetcord result](https://i.imgur.com/SXITM0a.png)

設置
===
## 前置作業:
1. 沒有[fly.io](https://fly.io/)帳號的先去辦一個，需要綁定信用卡才能有免費的額度可以使用，以下圖片是免費額度的詳細介紹，是足以完全支撐該Tweetcord bot的運作，不需要擔心被收費(除非政策改變)。

   ![flyio free allowances](https://i.imgur.com/JFl4Hh8.png)

2. 需要一個discord bot token，以下連接有關於獲取token的教學和一些關於discord bot的常識。

    > https://hackmd.io/@smallshawn95/python_discord_bot_base

3. 要取得你twitter帳號的auth_token，登入twitter後找到以下圖片裡的cookie name，它的value 就是我們需要的，看你是要用插件還是其他方法都行，能找到該cookie就好。

   <img src="https://i.imgur.com/vHDLfjc.png" width="50%" height="50%">

## step 1
電腦按下 win+R，輸入 cmd 會跳出命令提示字元，接著再輸入以下指令安裝 flyctl，並且等待安裝完成。(如果出現 'pwsh' 不是內部或外部命令、可執行的程式或批次檔 錯誤的話，把前面的pwsh改成powershell就可以了)

```powershell
pwsh -Command "iwr https://fly.io/install.ps1 -useb | iex"
```

![step1](https://i.imgur.com/TevTHdd.png)

## step 2
在 cmd 輸入 `flyctl auth login` 登入你的 fly.io 帳戶。

![step2](https://i.imgur.com/2K2Htjx.png)

## step 3
打開 https://github.com/Yuuzi261/Tweetcord/releases 挑最新的版本下載 Source code (zip)，並解壓縮，裡面有2個檔案可以刪掉，分別是 .gitignore 、 LICENSE。

<img src="https://i.imgur.com/2uKxprA.png" width="70%" height="70%">

## step 4
新建一個名叫 Dockerfile 的檔案，把以下內容複製貼到進去(記事本編輯即可)。

```Dockerfile
FROM python:3.10.9
WORKDIR /bot
COPY requirements.txt /bot/
RUN pip install -r requirements.txt
COPY . /bot/
CMD python bot.py
```

完成後如下圖:

![Dockerfile result](https://i.imgur.com/bkiFY2s.png)

## step 5
編輯config.yml(記事本編輯即可)，以下是每一行參數的解釋，可以直接用預設，等熟悉了再回來更改。

```yml
prefix: ''                         # 機器人命令的前綴。
activity_name: ''                  # 機器人顯示的活動名稱。
tweets_check_period: 10            # 檢查推文的頻率（不建議將此值設置得太低，以避免速率限制）。
tweets_updater_retry_delay: 300    # 當Tweets Updater遇到異常（例如速率限制）時的重試間隔。
tasks_monitor_check_period: 60     # 檢查每個任務是否正常運行的間隔，如果某個任務停止了，嘗試重新啟動。
tasks_monitor_log_period: 14400    # 將當前運行中的任務列表輸出到執行日誌的間隔。
```

## step 6
確保目前的路徑是在 tweetcord 資料夾的位置，然後在上方輸入 cmd 按下 Enter 鍵就會出現命令提示字元。

<img src="https://i.imgur.com/PvdjQkp.png" width="70%" height="70%">
<br></br>

接著輸入`fly launch`，app name 你想要取什麼都行。

![fly launch](https://i.imgur.com/RtCQpz8.png)

取完 app name 後按 Enter 繼續執行，選取要架設的伺服器位置，沒特別偏好就隨便挑一個，挑完 Enter 繼續執行。

![choose server](https://i.imgur.com/GL61NCz.png)

然後會遇到3個問題，全部都輸入 N 就好，之後確認一下 tweetcord 資料夾內有沒有一個名為 fly.toml 的檔案，有的話就繼續下一步。

![three questions](https://i.imgur.com/KRBeMEb.png)

## step 7
在 cmd 裡面輸入 `flyctl volumes create <volume_name> -a <your_app_name>`，<volume_name> 隨便取個名子就行，然後 <your_app_name> 是輸入剛剛上一步驟的 app name，最後的結果如下圖。這一步驟主要是以後更新bot的時候，防止儲存資料的database不會被重置。

#### 2023/11/20補充: 
這樣分配了3GB的空間，但理論上不會用到這麼多，所以可以改成`flyctl volumes create <volume_name> -a <your_app_name> --size 1`，1GB就夠用了。

![volume](https://i.imgur.com/SrWyTh9.png)

## step 8
來到 fly.io 的 DashBoard 就會看到剛剛創建的 app。

![dash board](https://i.imgur.com/DPotIE3.png)

點進去後在右列清單找到 Secrets，然後按下 New Secret 按鈕，我們要創建2個 Secrets，Name 的部分必須完全跟圖片上一樣，然後 Secret 分別是輸入你的 discord bot token 和 Twitter 的 auth_token。

![](https://i.imgur.com/ly5ZszJ.png)
![](https://i.imgur.com/0SGpODQ.png)

## step 9
打開 tweetcord 資料夾內的 fly.toml 檔案進行編輯(記事本編輯即可)，將我選取起來的部分刪除。

![delete](https://i.imgur.com/sg4EHci.png)

把下方的內容都貼到 fly.toml，source 是輸入你的 volume_name，其他都不需要改動。

```toml
[env]
    DATA_PATH = "/data/"    
[mounts]    
    source = "input_your_volume_name"    
    destination = "/data"
```

最後結果如下圖:

![result](https://i.imgur.com/qaGn0AY.png)

## final step
回到 cmd 輸入 flyctl deploy ，這個指令會開始部署 bot，如果以後常常更新 bot 的話，一樣是用這個指令部署，第一次使用該指令的話會要點時間。
   
看到這個就代表你至少部署成功了，但是還不能確定bot是不是成功啟動，連接點進去可以到看bot在後台運行的狀況。

![succesful](https://i.imgur.com/zUoCndN.png)

這樣代表機器人已經設置成功了，回到discord看，bot也已經顯示在線。

![online](https://i.imgur.com/ZKrErtz.png)
![online](https://i.imgur.com/4Gft4yh.png)

最後就是用 slash command 設置的部份了，指令部份的相關說明都在以下連接裡面。

> https://github.com/Yuuzi261/Tweetcord/blob/main/README_zh.md

結語
===
步驟應該算是很完整了，有不清楚的部份或是跟discord bot相關的問題都可以問，我會盡快回覆。祝大家都可以順利完成自己的 discord bot 部署。題外話，由於該 discord bot 的架構是 cog，所以可以很輕易地擴充其他功能或是指令，有興趣的可以自行研究。

>cog相關教學: https://hackmd.io/@smallshawn95/python_discord_bot_cog