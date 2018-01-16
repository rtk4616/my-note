一樣是參考 [GitBook 中文解說 - 2.4
](https://wastemobile.gitbooks.io/gitbook-chinese/content/github/transferring_to_github.html)

1. 先至 gitbook - setting - github 設定 integration

2. 至 github 開 repo

3. 至 gitbook 上某一本書的 settings - github 選擇 你剛才創的 repo

4. 會有 sync error 的問題，網頁預設也是用 force 解決  
那這邊似乎是在terminal用指令比較好，因爲透過網頁sync似乎要跑很久

```
# 複製你的 GitBook 倉儲（需要輸入帳號密碼）
$ git clone https://git.gitbook.com/MyName/MyBook.git ./mybook

# 切換到目錄
cd ./mybook

# 推送到 GitHub 的倉儲
$ git push https://github.com/username/repo.git master --force
```

果然用指令上傳快多了。

5. 備註  
似乎還有一些細節會讓 github 與 gitbook 的檔案不會完全一致：  
像是 `.gitignore` 兩邊似乎就不太一樣。  
所以拉書的時候可能還是從`根源` gitbook 拉比較好。
