參考頁面：https://wastemobile.gitbooks.io/gitbook-chinese/index.html

### 爲你自己寫筆記 - MAC 安裝 gitbook

1. 開始：  
到 gitbook 官網，登入你的 github 帳號

2. 選一個樣板 Create Book  
共有三個可以選，先選 `BOOK & MANUAL`，其他的有空再試

3. Title，取你的書名，描述則可有可無。

4. 有 public 跟 private 可以選，我想先選 `private`  
.....靠，連一本 private book 都不開放啦  
才沒過多久吶
只好選`public`惹

5. 安裝gitbook前置作業:  
   要裝不少東西 node.js via nvm, gitbook via npm, calibre

6. 透過 nvm(Node Version Manager) 安裝 node.js  
[很棒的攻略](http://icarus4.logdown.com/posts/175092-nodejs-installation-guide)  
先用 homebrew 裝 nvm，裝好再裝node.js  
裝好`nvm`之後  
我想裝的是官方推薦的版本`v8.9.4`  
`nvm install v8.9.4`

6. 安裝 calibre 裡的 ebook-convert  
先去官網下載 calibre.dmg  
接著還要確認 ebook-convert 能不能用  
`sudo ln -s ~/Applications/calibre.app/Contents/MacOS/ebook-convert /usr/bin`
    > ln: /usr/bin/ebook-convert: Operation not permitted

    看來這行指令在舊版的MAC應該是可以的  
但是稍微新版的 MAC OS X EI Capitan 就有鎖權限了 XD [說明在此](https://www.jianshu.com/p/22b89f19afd6)  
想說我都用 sudo (最高權限)了還不行，會是什麼情形？  
觀看說明後的討論可以發現有不少人建議放到 `/usr/local/bin`
來試試看吧，那行指令其實有兩個地方要改  
`sudo ln -s /Applications/calibre.app/Contents/MacOS/ebook-convert /usr/local/bin`  
成功會長這樣，你能夠直接執行ebook-convert：  
```bash
$ ebook-convert --version
ebook-convert (calibre 3.14.0)
Created by: Kovid Goyal <kovid@kovidgoyal.net>
```

8. 接下來要用homebrew裝 NPM，裝完再用npm裝 gitbook  
`brew install npm`  
`npm install -g gitbook-cli`  

- 終於裝完啦，可以開始碰 gitbook 了
還不想裝在 windows 上  
要裝這麼多東西...  
