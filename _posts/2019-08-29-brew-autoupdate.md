---
layout: post
title: '解決 Update Homebrew 過慢'
author: 'racterub'
date: '2019-08-29'
tags: 'homebrew macos'
---
Homebrew 在安裝套件前都會自行比對本地跟 GitHub 上的版本，然後 fetch 到最新版。
如果是全新的 homebrew 或許等待時間還可接受，但是如果新增了過多的 Source (tap 太多東西) 就會造成時間過度浪費。
<br/>
我有嘗試 code review 把 fetch 的地方拉出來寫成一個 cron 來執行，但是最後我太懶(? 決定直接讓他自己跑 `/usr/local/bin/brew update`
<br/>
以下會是我自行使用的設定
1. 建立 plist
- `~/Library/LaunchAgents/org.racterub.brewupdate.plist` **一定要放在 ~/Library/LaunchAgents 裡面，檔名為 org.{username}.{jobname}.plist**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
    <dict>
        <key>Label</key>
        <!-- 必須和檔名相同 -->
        <string>org.racterub.brewupdate</string>
        <key>ProgramArguments</key>
        <array>
            <string>/usr/local/bin/brew</string>
            <string>update</string>
        </array>

        <!-- Update every midnight -->
        <key>StartCalendarInterval</key>
        <dict>
            <key>Hour</key>
            <integer>0</integer>
        </dict>
    </dict>
</plist>
```

2. 執行
- `launchctl load ~/library/LaunchAgents/<jobfile>`
- `launchctl start ~/Library/LaunchAgent/<jobname>`

並且用 `launchctl list` 查看 job 的狀態。
如果是使用我的設定檔，應該會長得像`-   0   org.racterub.brewupdate`，如果不同可以查看 `/var/log/system.log` 來 debug
