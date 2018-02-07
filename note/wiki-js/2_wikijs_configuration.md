## Configuration
* 下 `node wiki configure`
- wiki.js 就會自動在 `localhost:3000` 上跑一個 configure gui server 起來
- 還蠻新鮮&漂亮的，用瀏覽器來設定
- click `START`

1. System Check:
```
Node.js v9.3.0 detected. Minimum is v6.9.0.
Node.js Crypto module is available.
Git v2.15.0 detected. Minimum is v2.7.4.
3949 MB of system memory available. Minimum is 768 MB.
config.yml is writable by the setup process.
Looks good! No issues so far.
```
可以看到值得注意的是 RAM 至少要 768MB

2. General
- SiteTitle
- Host
- Port
- Site UI Language, 有中文耶
- Public Access

3. Important Considerations
```
Is Wiki.js going to be behind a web server (e.g. nginx / apache / IIS) or proxy?
- Make sure the upload limit is sufficient. Most web servers have a low limit (e.g. 2 MB) by default.
- Make sure your web server is configured to allow web sockets. Wiki.js will fallback to standard XHR queries if not available.
- Do not rewrite URLs after the domain. This can cause unexpected issues in Wiki.js navigation.
- Do not remove or alter the client IP when proxying the requests. This can cause the authentication brute force protection to engage unexpectedly.
The site will not be using HTTPS?
The host URL you specified is not HTTPS. It is highly recommended to use HTTPS. You must use a web server / proxy (e.g. nginx / apache / IIS) in front of Wiki.js to use HTTPS. Wiki.js does not provide HTTPS handling by itself.

Are you sure you want to use localhost as the host base URL?
The host URL you specified is localhost. Unless you are a developer running Wiki.js locally on your machine, this is not recommended!
```
真貼心

4. **Database**
```
Wiki.js stores administrative data such as users, permissions and assets metadata in a MongoDB database. Article contents and uploads are not stored in the DB. Instead, they are stored on-disk and synced automatically with a remote git repository of your choice.

MongoDB Connection String

mongodb://localhost:27017/wiki
The connection string to your MongoDB server. Leave the default localhost value if MongoDB is installed on the same server.
You can also specify an environment variable as the connection string, e.g. $(MONGO_URI).
```
* Wiki.js 把 `使用者資料` 放在 `MongoDB` 資料庫裡
* `文章與上傳資料` 不存在資料庫，會存在某個 `remote git repository` 裡

5. Database Check
- 這邊我沒開 MongoDB 被回報:  
`Error: failed to connect to server [localhost:27017] on first connect [MongoError: connect ECONNREFUSED 127.0.0.1:27017]`
- 這時候就要回頭看 [
Start MongoDB.](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-ubuntu/)

```
sudo service mongod start
```

這時候回 wiki.js configuration 網頁 按 `Try Again`  
應該就要顯示 `Connected successfully!`

6. Path, `CONTINUE`
7. **Git Repository**
* 官方強烈建議使用 git 備份機制，我的直覺告訴我要 private 化
* 所以我先去創`bitbucket`帳號
* 創蠻快的，有1G容量限制，不確定是每個 repo 還是每個帳號
* 依照我玩 github 的經驗，沒記錯的話最好乖乖commit 第一次，有給指令:
```
git clone https://Izraeil@bitbucket.org/Izraeil/mywikijs.git
cd mywikijs
echo "# My project's README" >> README.md
git add README.md
git commit -m "Initial commit"
git push -u origin master
```
- git clone 之後會要求輸入你的 bitbucket 密碼
- git push 也會要求密碼

* 接著把 https://bitbucket.org/Izraeil/mywikijs 輸入 git repo URL 欄位
* 然後我卡在 `Verifying Git repository settings...`
> 應該是 我的 `Private Key location` 沒有另外設定的關係

* 先按 `BACK`，很好沒反應，把網頁 REFRESH 會全部重新開始 XD
* 上面六個步驟重跑一次，填上 repo URL
* 先偷懶改 authentication 方式，用傳統帳號密碼登入
* 填上email

成功會顯示：
```
Data directory path is valid.
Git directory path is valid.
Local git repository has been initialized.
Git Signature Name has been set successfully.
Git Signature Name has been set successfully.
Git SSL Verify flag has been set successfully.
Git Remote was added successfully.
Git Pull operation successful.
Git settings are correct!
```
yeah, 我們點繼續
8. administrator account
9. Finalizing  
Wiki.js was configured successfully and is now ready for use.  
Click the Start button below to start the Wiki.js server.

### 開始啦!!!

* 照理說 wiki.js server 已經跑起來了
* 預設設定是 `localhost 80 port`
* 沒猜錯的話 我的 `linux server 80port` 沒有設定好
* 編輯 `config.yml` , `port: 80` 改成 `port: 3000`
- 連進去會發現需要帳密:
```
Kuan's Wiki
Login required

Email / Username

Password
Log In
Or, log in using...Microsoft AccountGoogle ID
Powered by Wiki.js
```

* 輸入在 8. 設定的帳號密碼
* 恩...畫面都還很陽春，點了 Create Home Page 可以編輯內容，但是沒有SAVE的按鈕?

___
沒關係我們先設定 linux 啟動時自動開啟 wiki_js service  
`sudo npm install -g pm2`  
`pm2 startup`  
`pm2 update`  
`pm2 save`  
應該這樣就行了 吧

後來發現 node wiki start 這行就會自動被 pm2 抓到了  
不用再補wiki進去pm2  吧

多用指令觀察狀態
pm2 status
> 我曾經讓兩個 wiki 同時跑起來，互相干擾，太晚去看 log

___
### 遇到幾個問題：

* Q1:原本預設的`80 port`不能用，3000 port 可以用?  
用官方的 solution A 下兩行指令之後就能解了  

```bash
sudo apt-get install libcap2-bin
sudo setcap cap_net_bind_service=+ep `readlink -f \`which node\``
```

當然記得改完設定要下 node wiki restart 重啟 wiki  

* Q2:沒有樣式套用，顯示有點奇怪?  
這個現象就是下了 `node wiki configure` 之後  
有漂亮的樣板，但是我們下了(node wiki start)/架好的 wiki server 就沒有  
像是登入頁面就沒有漂亮的樣板了  
先貼原文:
```
Weird display, no styling
Cause: The value you entered for the host config parameter is wrong.

Solution: This value should correspond to the host/domain users are using to access your wiki, including the port if different than 80 / 443.
```

其實上面兩個在 trouble shooting 都有解，但是Q2寫得不夠詳細，要debug  
抱歉，是我英文太爛...，又自以為懂 XD

那串英文的意思是
你的 `host設定` 有問題。  
問題是甚麼呢？問題就是使用者輸入的`URL/網址`，要與 `host 設定` 完全一樣
貼一下我出問題的`config.yml`設定：

```yml
title: Kuan's Wiki
host: 'http://localhost:80'
port: 80
```

環境描述:
我的 LINUX 是架在 virtual machine 上的，上面的設定  
在LINUX內 用瀏覽器開 'http://localhost:80' 是沒問題的有漂亮的樣板顯示  
但是我從WIN PC連過去LINUX http://192.168.56.88/ 就沒有樣板了，雖然能連進去  
原因就是 `URL/網址`，要與 `host 設定` 完全一樣，才能套到樣板
同理: `host: 'http://127.0.0.1:80'` 也不行  
要設定成 `host: 'http://192.168.56.88:80'` 才可以

##### 終於有樣板啦 YAAAAAAAAAAAAAAAAAAAAAAAAAAAA, Let's go
