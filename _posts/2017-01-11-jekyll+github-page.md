---
layout:     post
title:      "Jekyll + Github page 架設簡單個人網站(Mac)"
subtitle:   "Practical CoreBluetooth for Peripherals"
date:       2017-01-11
author:     "John Wu"
catalog:    true
tags:
    - Web
    - Jekyll
    - Github
---

本篇記錄如何使用Github page + Jekyll建置網站，如果對網頁有粗淺認識並且想架設自己的部落格的人希望本篇對你有些幫助

### Jekyll介紹
 
[Jekyll](https://jekyllrb.com/) 是一個Open Source的靜態網站產生框架，他會根據網頁原碼生成靜態網站。提供了模板，套件等功能。（Jekyll是基于Ruby的程序）

官方說明
>Jekyll is a simple, blog-aware, static site generator perfect for personal, project, or organization sites. Think of it like a file-based CMS, without all the complexity. Jekyll takes your content, renders Markdown and Liquid templates, and spits out a complete, static website ready to be served by Apache, Nginx or another web server. Jekyll is the engine behind GitHub Pages, which you can use to host sites right from your GitHub repositories.

### Github page介紹

[Github page](https://pages.github.com/) 可以把你的網站建置在github上，非常的方便且簡單操作。就是一個簡單的靜態網站託管。


### 開始

#### 新建new repository
申請Github帳號，新建new repository，然後為我們新建的repository命名，命名需要遵循規則：username/username.github.io。
username這邊是你的帳號名稱
#### clone 到自己本地端
```
mkdir MyWebsite
cd MyWebsite
git clone https://github.com/username/username.github.io
```
#### 安裝Jekyll
下面是我有使用到的套件，如果有缺其它套件再安裝就好，Mac自帶ruby所以就不用再安裝ruby，更新一下就好，確保為最新版本，因為本人是OSX 10.11所以上網查了一下需要這樣[安裝](http://stackoverflow.com/questions/31567029/how-can-i-install-jekyll-on-osx-10-11)

- OSX 10.11以上
```
gem update ruby
sudo gem install -n /usr/local/bin/ jekyll
sudo gem install -n /usr/local/bin/ bundler
sudo gem install -n /usr/local/bin/ rdiscount
```

- OSX 10.11以下
```
gem update ruby
sudo gem install jekyll
sudo gem install bundler
sudo gem install rdiscount
```

#### 使用Jekyll
Jekyll有個好處就是有自帶虛擬伺服器，可以在本地端測試，先創建新的專案，在本地端測試執行。
```
jekyll new MyBlog
cd MyBlog
jekyll server
```
成功的話會顯示在這網址 http://127.0.0.1:4000/

#### Jekyll目錄結構
```
.
├── _config.yml
├── _data
|   └── members.yml
├── _drafts
|   ├── begin-with-the-crazy-ideas.md
|   └── on-simplicity-in-technology.md
├── _includes
|   ├── footer.html
|   └── header.html
├── _layouts
|   ├── default.html
|   └── post.html
├── _posts
|   ├── 2007-10-29-why-every-programmer-should-play-nethack.md
|   └── 2009-04-26-barcamp-boston-4-roundup.md
├── _sass
|   ├── _base.scss
|   └── _layout.scss
├── _site
├── .jekyll-metadata
└── index.html 
```
- config.yml:配置文件，主要用於設置基本訊息。
- _includes:存放可覆用的模組，`{``% include foot.html %``}`調用。
- _layouts:html存放位置。
- _posts:文章名稱必須(年-月-日-文章名稱)照著規則命名。
- _site:jekyll生成的檔案，可以放在.gitignore文件中忽略他。

### Jekyll+Github page
剛剛新建專案跟他目錄結構相比少了很多東西，你可以到[Jekyll Theme](http://jekyllthemes.org/)下載你喜歡的主題，或者你可以上github找找其他人的網站clone下來再做修改，我是利用[Hux大大](https://github.com/Huxpro/huxpro.github.io)做修改，
接下來你只要把你下載的或clone下的專案複製貼上到你username.github.io資料夾裡在做push的動作，就完成囉～
```
git add --all
git commit -m "Initial commit"
git push -u origin master
```







