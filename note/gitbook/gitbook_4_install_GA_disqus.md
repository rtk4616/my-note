# Gitbook Plugins 插件踩雷筆記
1. 版本很重要，不論是 Gitbook 版本或是 plugins 的版本，差異比我想像中要大太多了。
2. plugin 版本很重要，不然 debug 會找不完，還是踏着這位欸巨人的肩膀吧：[這個 Gitbook 教學讚](http://gitbook.zhangjikai.com/plugins.html)。
3. plugin 版本很重要，想省時間請直接 fork 他的 github repo 來改，想玩 plugin 請務必指定 gitbook 與 plugins 的版本，不然會 debug 到崩潰，而且我試到後來才發現那位大大的 gitbook 版本就是最新的 3.2.3 了。
4. plugin 很敏感，除了版本要 debug 之外，plugin 載入的順序也有差異，json 語法也偏嚴格，非常建議要在本機上測過效果之後再 git push 上去，不然直接上線測試來來回回會弄很久。

官方網頁：https://plugins.gitbook.com/   
老實說不算好用，還是 直接 google 或是看那位巨人的 gitbook 比較快。

---

## 常用 gitbook 指令：

```bash
# 本機安裝 plugins
gitbook install
# gitbook 本機測試
gitbook serve
```

## gitbook 設定檔
- `book.json` 在你的 gitbook project 資料夾底下，沒有的話就建一個吧。  
- .json 裡，逗號 `,` 位置要特別注意。
- .json 檔案裡要寫註解似乎會很麻煩，我沒找到方法。  
- 測試 book.json 語法是否正確：http://jsonlint.com/  

> `.gitbook\`：這個資料夾在你的家目錄底下，有些教學會去裏面操作 ~/.gitbook/，主要都是改 book.json。  

先貼一下我目前的 book.json ，最新的請看 [我的 book.json](https://github.com/Azraeil/my-note/blob/master/book.json)。
```json
{
  "gitbook" : "3.2.3",
  "links": {
    "sidebar": {
        "Home": "你的首頁網址"
    }
  },
  "plugins": [
    "-lunr",
    "-search",
    "search-plus@^0.0.11",
    "simple-page-toc@^0.1.1",
    "github@^2.0.0",
    "github-buttons@2.1.0",
    "edit-link@^2.0.2",
    "disqus@^0.1.0",
    "anchors@^0.7.1",
    "splitter@^0.0.8",
    "ga",
    "tbfed-pagefooter@^0.0.1",
    "todo@^0.1.3",
    "anchor-navigation-ex@0.1.8"
  ],

  "pluginsConfig": {
    "simple-page-toc": {
      "maxDepth": 5,
      "skipFirstH1": true
    },

    "github": {
      "url": "https://github.com/你的github帳號"
    },

    "github-buttons" :{
      "repo": "你的github repo: UserName/RepoName",
      "types": ["star"],
      "size": "small"
    },

    "edit-link": {
      "base": "https://github.com/githubID/gitbook_repo/edit/master"
    },

    "disqus":
    {
      "shortName": "申辦填的 shortname"
    },

    "ga":
    {
      "token": "UA-申辦後給的 ID"
    },

    "tbfed-pagefooter": {
      "modify_label": "Last update：",
      "modify_format": "YYYY-MM-DD"
    },

    "anchor-navigation-ex": {
      "isRewritePageTitle": true,
      "isShowTocTitleIcon": true,
      "tocLevel1Icon": "fa fa-hand-o-right",
      "tocLevel2Icon": "fa fa-hand-o-right",
      "tocLevel3Icon": "fa fa-hand-o-right"
    },


    "fontsettings": {
      "theme": "night",
      "family": "sans",
      "size": 2
    }
  }
}
```

book.json 以外跟設定有關的其他檔案我都沒改，gitbook 版本是 3.2.3。

你可能會有的疑問是："蛤？plugin 要注意順序就算了，每個 plugin 的版本都要特別設定？"。  
我踩過雷的回答是："YES"，gitbook 成功跑起來 (gitbook serve) 與 所有的 plugins 都成功跑起來是兩件事...。

接下來簡單介紹一些要額外設定的 plugin。

---

#### Part1. 用 npm 替 gitbook 安裝 Google Analystics
`npm install -g gitbook-plugin-ga` 本機安裝。

---
#### Part2. 讓 gitbook 載入 Google Analystics
這時候要編輯 gitbook 的設定檔 `book.json`，[book.json 介紹](https://wastemobile.gitbooks.io/gitbook-chinese/format/configuration.html)。

```json
{
  "plugins":["ga"],
  "pluginsCongig":{
    "ga":{
      "token":"填入你的GA追蹤ID：GA-xxxxxxxx"
    }
  }
}
```

這邊我們會需要 GA token，我也沒申請過，來研究一下。  
要到 https://analytics.google.com 登入你的 google 帳號後。
1. [開一個GA帳戶](https://analytics.google.com/analytics/web/?authuser=0#provision/CreateAccount/)。
2. 把你的 gitbook 網址丟進去。
3. 點選`取得追蹤 ID`，我的 chrome 頁面有點太大，導致點不到 服務條款合約 的接受，這時候就要按 `command` + `-` 將整個頁面縮小一點。
4. 接下來就會顯示你的`追蹤ID`了，的確是 `UA-xxxxxx-x`。
5. 將 GA token 填入你的 book.json。
6. 把 book.json git push 至 gitbook。
7. Debug，gitbook 蠻厲害的還會自動偵測你的 git push 後的設定檔有沒有成功。  
舉例：[我的 gitbook error](https://www.gitbook.com/book/azraeil/my-note/activity)。
8. 去 GA 確認你的設定是否成功。

[[推薦]寫完要驗證 json 語法是否正確](https://jsonlint.com/)，json 對於逗號判定很嚴格，所以逗號後面也不能有其他東西，包含空白鍵。

> 備註：  
> 如果你的瀏覽器有裝擋廣告 addon，會把 GA 的 封包也擋掉 XD，我是裝 chrome addon `Google Tag Assistant`來確認的。

---

#### Part3. 讓 gitbook 載入 Disqus 討論區
如果你不需要 GA，單純只要一個 plugin，其實結果會跟上面的GA設定很像。

```json
"plugins": ["disqus"],
"pluginsConfig": {
   "disqus": {
       "shortName": "填入你的disqus shortName"
   }
}
```

1. 這邊一樣要去 [disqus](https://disqus.com/) 申請帳號並且進行設定，登入後按 `GET STARTED`。
2. 接著選 I want to install Disqus on my site，然後照著設定就可以了。
3. 只需要留意你設額定的 Shortname 是什麼，把它填進你的 disqus 設定。
4. 上傳 book.json，去你的 gitbook 測試。

##### 如何寫 安裝兩個以上的 plugin 語法？
你可以自己先試試看，反正失敗 gitbook 也不會承認新的設定 XD。  
[設定範例](http://gitbook.zhangjikai.com/bookjson.html)。

```json
{
  "plugins":
  [
    "ga",
    "disqus"
  ],

  "pluginsConfig":
  {
    "ga":
    {
      "token": "填入你的GA追蹤 ID：GA-xxxxxxxx"
    },

    "disqus":
    {
      "shortName": "填入你的 disqus shortName"
    }
  }
}
```

---

#### Part4, 修改 gitbook 預設的主題色調，change gitbook default theme
2018/1/25, 這個我研究很久，沒猜錯的話關鍵可能還跟 plugin 的載入順序有關。  

```log
info: loading plugin "search-plus"... OK
info: loading plugin "simple-page-toc"... OK
info: loading plugin "github"... OK
info: loading plugin "github-buttons"... OK
info: loading plugin "edit-link"... OK
info: loading plugin "disqus"... OK
info: loading plugin "anchors"... OK
info: loading plugin "splitter"... OK
info: loading plugin "ga"... OK
info: loading plugin "tbfed-pagefooter"... OK
info: loading plugin "todo"... OK
info: loading plugin "anchor-navigation-ex"... OK
info: loading plugin "livereload"... OK
info: loading plugin "highlight"... OK
info: loading plugin "sharing"... OK
info: loading plugin "fontsettings"... OK
info: loading plugin "theme-default"... OK
```

關鍵應該是 fontsettings 跟 theme-default，這兩個 default plugin 的載入順序。  
猜對啦，弄老半天...：

要放在 pluginsConfig 的最後面，json 不會回頭找。

You should put the fontsettings in the final place because the load order of plugin.

```json
  "fontsettings": {
    "theme": "night",
    "family": "sans",
    "size": 2
  }
```

**注意上面的 log 載入順序，跟主題有關的 "fontsettings" 是倒數第二個才載入，我之前試的跟主題有關的 pluginsConfig 都太早寫了。**
