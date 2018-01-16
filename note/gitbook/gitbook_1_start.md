#### 應該有幾個大目標
1. 本機建立與編輯 一本 gitbook
2. 發佈 gitbook 網頁版本
___

### 建立一本新的gitbook 與確認編輯環境
1. `mkdir my_gitbook`, `cd my_gitbook`
2. `gitbook init`
   會建立 README.md and SUMMARY.md 必要檔案
3. `gitbook serve`, 接著去瀏覽器輸入 `localhost:4000`, 就能看到書了！
4. 基本編輯模式完成！！！
5. 預設就有夜間模式了，超讚，按'A'，接著點`Night`，就能看到樣式直接換啦
___

### 編輯gitbook目錄 `SUMMARY.md`
[參考](https://wastemobile.gitbooks.io/gitbook-chinese/content/format/chapters.html)

目錄就是透過 tab 縮排跟 markdown 超連結語法構成。
直接貼上例子：

```md
# Summary

* [ch1](part1/README.md)
    * [ch1-1 寫作是美好的](part1/writing.md)
    * [ch1-2 GitBook 也不錯](part1/gitbook.md)
* [ch2](part2/README.md)
    * [ch2-1 我們歡迎讀者回饋](part2/feedback_please.md)
    * [ch2-2 對作者更好的工具](part2/better_tools.md)
```

來把舊筆記搬過來吧！  
複製到 my_gitbook 底下之後，就可以仿造上面的例子把你想顯示的筆記都放上去
> 我有把整個資料夾都用 dropbox 同步備份，這樣我的筆記很容易就可以在任意一臺電腦上編輯

編輯目錄跟筆記資料夾完成之後就可以上架了
