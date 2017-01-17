---
layout:     post
title:      "Web加上Disqus留言功能"
date:       2017-01-17
author:     "John Wu"
catalog:    true
tags:
    - Web
    - Disqus
    - javascript
---

既前篇簡單架設自己網站後，現在要內遷一個[Disqus](https://disqus.com/)留言功能

### 先註冊一個Disqus帳號
[Universal Embed Code官方教學](https://help.disqus.com/customer/portal/articles/472097-universal-embed-code)

### 選擇Disqus內遷平台
我先創了一個叫做johnwu的Disqus site
下圖為選擇內遷的平台，繼上篇我是利用jeklly+github page製作的網站，等於是自寫網站，所以選擇最下面那個`I don't see my platfotm...`
<img src="/img/post/2017-01-17-disqus-comment/disqus-platform.png" style="width: 800px;"/>

因為我創建johnwu的site所以`s.src = '//johnwu.disqus.com/embed.js';`這裡要為自己的名稱，完整程式碼如下
複製貼上到自己的post畫面就好了

```
<div id="disqus_thread"></div>
<script>

/**
*  RECOMMENDED CONFIGURATION VARIABLES: EDIT AND UNCOMMENT THE SECTION BELOW TO INSERT DYNAMIC VALUES FROM YOUR PLATFORM OR CMS.
*  LEARN WHY DEFINING THESE VARIABLES IS IMPORTANT: https://disqus.com/admin/universalcode/#configuration-variables*/
/*
var disqus_config = function () {
this.page.url = PAGE_URL;  // Replace PAGE_URL with your page's canonical URL variable
this.page.identifier = PAGE_IDENTIFIER; // Replace PAGE_IDENTIFIER with your page's unique identifier variable
};
*/
(function() { // DON'T EDIT BELOW THIS LINE
var d = document, s = d.createElement('script');
s.src = '//johnwu.disqus.com/embed.js';
s.setAttribute('data-timestamp', +new Date());
(d.head || d.body).appendChild(s);
})();
</script>

```

### 修改Disqus的Trusted Domains
以上客戶端都完成了，剩下的就是Disqus的後端，登入自己的[Disqus](https://disqus.com/)並且進入Admin模式
- 點選navigation的setting選單
- 下滑至Trusted Domains
- 新增自己網站的domain
<img src="/img/post/2017-01-17-disqus-comment/disqus-domain.png" style="width: 800px;"/>

### 完成
看看自己網站有沒有留言功能吧，如果創建了很多sites記得要去Disqus(Admin)確定哪個是上線的，才能確保有遷入成功。




