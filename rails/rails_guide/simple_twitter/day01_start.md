在開始之前，先把專案檔案準備好：
1. fork
2. clone 到 本地
3. 本地檔案初始化
  - `bundle install`
  - rails db:migrate

- 更新 Github
- Pull Request 提交專案

*注意*
自動化程式碼都在 /spec 底下。
不能用 `rails g model` or `rails g controller` 指令。

---
OK，一開始先給前端設計稿以及 User stories

### User stories (功能要求)
- 除了註冊和登入頁，使用者一定要登入才能使用網站
  - devise 大大

- 使用者能創建帳號、登入、登出
  - 除了信箱和密碼，使用者在註冊時還能設定自己的名稱
    - 教材有教過，還有印象
  - 使用者的名稱不能重覆，若有重覆會跳出錯誤
    - 還有印象，名稱不能重複我之前好像沒設定過
  - 使用者能編輯自己的名稱、介紹和大頭照
- 使用者能瀏覽所有的推播 (tweet)

- 管理者登入網站後，能夠進入後台頁面
  - 教材有
  - 管理者可以瀏覽所有的推播與推播回覆內容
  - 管理者可以刪除使用者的推播
  - 管理者可以瀏覽站內所有的使用者清單
  - 該清單會列出他們的活躍程度（包括推播數量、關注人數、跟隨者人數、like 過的推播數）
  - 管理者可以瀏覽站內所有的使用者清單，該清單按`推播文數排序`

- 使用者能在首頁看見根據跟隨者 (followers) 數量排列前 10 的使用者推薦名單

- 點擊其他使用者的名稱時，能瀏覽該使用者的個人資料及推播

- 使用者能新增推播（限制在 140 字裡）
  - 教材有

- 使用者能回覆別人的推播
  - 新功能

- 使用者可以追蹤/取消追蹤其他使用者（不能追蹤自己）
  - 教材有

- 使用者能對別人的推播按 Like/Unlike
  - 教材有

- 任何登入使用者都可以瀏覽特定使用者的以下資料：
  - Tweets：排序依日期，最新的在前
  - Following：該使用者的關注清單，排序依照追蹤紀錄成立的時間，愈新的在愈前面
  - Follower：該使用者的跟隨者清單，排序依照追蹤紀錄成立的時間，愈新的在愈前面
  - Like：該使用者 like 過的推播清單，排序依 like 紀錄成立的時間，愈新的在愈前面

> `恩。。。依照前面的思維，admin 頁面要先做吧？`  
> 其實看完感覺還好，沒有到覺得非常麻煩的地方(除了前端)

---
### 接著是 Data Structure
不過我覺得這時候的第一要務應該是要先把專案載下來

因爲這個部分就是 Model
原來基本版都已經都弄好了

有需要新增欄位自己再加

code：followers_count
教材：follower_count

下一個網址設計就是 Routes

---
### Routes 設定
devise 已經弄了 XD

有一段比較不一樣的 /tweets/:tweet_id/replies
我沒印象這邊 ID 還能自己取的，而且 controller action 還是 replies#index

~~啊，難道說就是自訂網址，跟 後台一樣，不對！~~

先放在 tweet member 裏面，之後再處理。

哦，再一個 resources 就能改變 controller 了

```rb
member do
  resources :replies, only: [:index, :create]
end
```

共17個新路由

也就是總共有17個動作

---
### 本機測試 `rspec`
Github 測試就是到 github 發 PR

測試項有 55 個，目前 52 failed，所以是 3/55 pass

可惜 routes 弄完沒增加。

---
### Pull Request 測試
一個人只能有一個 PR

不過之後有新 commit 後應該可以追加 PR

---
### 要規劃一下，有哪些功能(controller)跟view，或是驗證要求(model)
大致上會照著之前跑過的流程來
所以會先弄 admin

要大概列一下要弄的東西跟清單
目前 RMCV ，R 好了， M 剩一半，大概都剩 C 跟 V。

然後推播就當成推文就好。

要先把任務拆小。

不過說實在，還真的有點模糊。

照著筆記跑一次吧。

> admin index 頁面 要放在 view/admin/tweets，不是 view/admin

# **瀏覽需要認證程序：在 ApplicationController 以及 BaseController 補上 before_action**

 admin BaseController 都繼承好了，直接寫

# **name 欄位已經被設定成必填欄位：客製化 devise**
 但是現在的 name 欄位就要輸入了，但是 Devise 還沒有 name 的輸入欄位。。。=\_=
 OK，邏輯順序都被改變，尷尬
 本來還想順便 美化 註冊頁面，結果發現 container 也是 bootstrap 的，也還沒裝 XD
 有印象了，主題叫做`客製化 devise`
看了筆記才回想起來並發現原來官方那些註解是提示，但是我早就忘了
原來是要在 controller 修改

= =，我傻眼了，官方做太多了啦

各種卡：undefined method `admin?'`

```log
app/views/layouts/application.html.erb where line #13 raised:
      <% if current_user&.admin? %>
```

阿就還沒做齁，急屁，這感覺真的超怪，那我要不要乾脆從，RSPEC 測試那邊反推回來做算了= =，傻眼

function 名稱還已經指定了好了，等等 那個 `&` 還是故意的？

我記得叫做 is_admin? 吧

然後在 model 那邊寫 method 對吧

# **檢查登入的使用者是否爲管理員：is_admin?**

# **先手動讓我們的帳號有後台權限**

# **官方提示，讓 name 不會有重複的**
哦，當初是讓 friendship 不能有重複的記錄
```rb
# 這樣似乎是能讓網頁跳出 error，但是似乎不太理想
# validates :name, uniqueness: {scope: :id}
# 這個是到 api.rubyonrails.org 查到的
validates_uniqueness_of :name
```

# **更正 admin root route**
# **更新導覽列後台網址**

此時 5/55 PASS，多過了兩個！ YA

# **新增後台瀏覽使用者身份檢查**
7/55 PASS
