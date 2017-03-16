---
layout:     post
title:      "從網路上下載ipa檔並且安裝在iPhone or iPad(Download and install an ipa from url on iOS)"
date:       2017-03-07
author:     "John Wu"
catalog:    true
tags:
    - iOS
    - ipa
---

原本這篇要介紹GoogleMobileVision，不過這為了自己要記錄，所以請原諒我的自私，然後這關鍵字真的不好下，所以我加上英文希望大家可以搜尋的到中文版教學。

### stack overflow發問原網址
 
[Download and install an ipa from url on iOS](http://stackoverflow.com/questions/23561370/download-and-install-an-ipa-from-url-on-ios) 上面的解法必須要有自己的server並且要申請ssl，因為.plist檔規定只能放在https底下，另外他也有介紹可以放置在dropbox那就方便許多，至於放置dropbox該如何放置他只有粗略介紹，像我這種初心者是看不太懂的，所以以下介紹使用dropbox的方法<br>

### stack overflow發問原網址(Dropbox)
[Over The Air (OTA) iOS IPA File Distribution For Public?](http://stackoverflow.com/questions/26042508/over-the-air-ota-ios-ipa-file-distribution-for-public)這篇有詳細介紹如何把.plist檔放上dropbox

### 開始
前情提要上面都很清楚地介紹了，需要檔案有三
- .ipa(ad-Hoc)
- .plist
- index.html

使用[BetaBuilder](http://www.hanchorllc.com/2010/08/24/introducing-ios-beta-builder/)創建index.html以及manifest.plist。[BetaBuilder下載](https://s3-ap-southeast-1.amazonaws.com/problem-arc/Beta-Builder/BetaBuilder.zip)
<img src="/img/post/2017-03-07-install-ipa-from-url/betaBuilder.png" style="width: 200px; height: 300px;"/>
輸入的網址是待會你要把產生出的index.html及manifest.plist以及ipa檔上傳的位置例如 
```
https://myWeb.com/MY_TEST_APP
```
Generate後他會自己產生index.html以及manifest.plist

#### 正常版
index.html
```
<a href="itms-services://?action=download-manifest&url=https://myWeb.com/MY_TEST_APP/manifest.plist">Install App</a>
```
manifest.plist
```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>items</key>
    <array>
        <dict>
            <key>assets</key>
            <array>
                <dict>
                    <key>kind</key>
                    <string>software-package</string>
                    <key>url</key>
                    <string>http://YOUR_SERVER_URL/YOUR-IPA-FILE.ipa</string>
                </dict>
            </array>
            <key>metadata</key>
            <dict>
                <key>bundle-identifier</key>
                <string>com.yourCompany.productName</string>
                <key>bundle-version</key>
                <string>1.0.0</string>
                <key>kind</key>
                <string>software</string>
                <key>title</key>
                <string>YOUR APP NAME</string>
            </dict>
        </dict>
    </array>
</dict>
</plist>
```
把ipa,.plist,index.html上傳就完成了～

#### Dropbox版
正常版我們是使用以下網址
```
https://myWeb.com/MY_TEST_APP
```
會使用Dropbox都是因為沒有申請網站的ssh所以只有http，所以我們的網址應該會變這樣
```
http://myWeb.com/MY_TEST_APP
```
這邊你的ipa可以放在你自己的網站或是dropbox，都可以<br>
如果我的ipa檔是放在[http://myWeb.com/MY_TEST_APP]()底下<br>
那manifest.plist就會跟正常版長一樣<br>
manifest.plist
```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>items</key>
    <array>
        <dict>
            <key>assets</key>
            <array>
                <dict>
                    <key>kind</key>
                    <string>software-package</string>
                    <key>url</key>
                    <string>http://YOUR_SERVER_URL/YOUR-IPA-FILE.ipa</string>
                </dict>
            </array>
            <key>metadata</key>
            <dict>
                <key>bundle-identifier</key>
                <string>com.yourCompany.productName</string>
                <key>bundle-version</key>
                <string>1.0.0</string>
                <key>kind</key>
                <string>software</string>
                <key>title</key>
                <string>YOUR APP NAME</string>
            </dict>
        </dict>
    </array>
</dict>
</plist>
```

OK，可以把manifest.plist上傳到dropbox了
##### 修改index
需要修改的會是index.html<br>
上傳manifest.plist檔後，從dropbox分享出manifest.plist的網址，大概長以下這樣
```
https://www.dropbox.com/s/5lcnp2lhrefawv7/manifest.plist?dl=0
```
修改成
```
https://dl.dropbox.com/s/5lcnp2lhrefawv7/manifest.plist
```

所以最後我們的index會變成

```
<a href="itms-services://?action=download-manifest&url=https://dl.dropbox.com/s/5lcnp2lhrefawv7/manifest.plist">Install App</a>
```

修該完成就把index.html上傳至自己的server吧。
這部分我只有manifest.plist檔案在dropbox上，剩下的ipa以及index.html都在自己server上。

### 分享你的index.html
最後就給別人index.html的網址，但必須要在手機上開啟才能夠下載安裝<br>


### 結語
如果是個人帳號包出的ad-hoc的話必須要註冊硬體id才能夠安裝，企業帳號的話就隨便安裝。<br>
流程是這樣的index.html->manifest.plist->.ipa