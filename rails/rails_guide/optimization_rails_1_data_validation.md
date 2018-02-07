### 資料庫欄位資料驗證

情境：或許你有注意到，我們在之前的 Rails CRUD 練習，在建立新使用者資料的時候，不一定每個欄位都要填才能送出，留白也可以建立新使用者，但是這有時候會造成困擾，某些重要資料需要強制使用者一定要填寫。

說到資料我們能夠想到這可能跟 Model 有關，而 Model 又常跟 controller 有互動：  
因此，我們有幾個目標：
1. 對 Model 資料庫欄位的資料設定新條件。
2. 對 Controller 裡跟儲存有關的 method 要進行相應的操作。

---
#### Part1, 對 Model 資料庫欄位的資料設定新條件。
編輯 `app/models/users.rb` ，在這個檔案建立一些條件限制。

```rb
validates_presence_of(:name, :mail, :gender, :age)
```

[官方文件](http://api.rubyonrails.org/v5.1/classes/ActiveModel/Validations/HelperMethods.html#method-i-validates_presence_of)：
```rb
validates_presence_of(*attr_names)
Validates that the specified attributes are not blank (as defined by Object#blank?). Happens by default on save.

class Person < ActiveRecord::Base
  validates_presence_of :first_name
end
The first_name attribute must be in the object and it cannot be blank.

If you want to validate the presence of a boolean field (where the real values are true and false), you will want to use validates_inclusion_of :field_name, in: [true, false].
```

我們用 `validates_presence_of()` method 驗證指定欄位資料是否存在，如果指定欄位沒有資料就不會儲存成功。

---

說到資料儲存你應該還有點印象在我們 controller 的 create 與 update method 都有儲存資料的行爲。

#### Part2, 對 Controller 裡跟儲存有關的 method 要進行相應的操作。
編輯 controllers/users_controller.rb

這邊重新翻過我們之前寫的 code，但是當時我們沒有觸發。

```rb
def create
  @user = User.new(user_params)
  if @user.save
    # 儲存成功後，重新導向頁面
    redirect_to users_url
  else
    # 儲存失敗，重新渲染頁面給使用者繼續編輯
    render :new
  end
end

def update
  @user = User.find_by(id:params[:id])
  if @user.update_attributes(user_params)
    redirect_to user_path(@user.id)
  else
    render :edit
  end
end
```

我也是寫到這邊才注意到，我的 update method 忘了考慮儲存成功跟失敗的例子。

重新回顧一次：  
在 create method，當使用者建立新使用者資料時，每個欄位都有填，才能夠儲存成功，有欄位沒填的話就將頁面上的資料留住讓使用者回到建立使用者頁面再編輯一次，update method 同理。

實際測試觀察#1：我在 new 的頁面，把一些欄位資料全部留空，直接按 Create User，結果網址變成首頁的，而且沒有填寫的輸入欄位自動跳了一行，這個現象不知道是怎麼回事，不過換網址總覺得有些問題？

實際測試觀察#2：編輯使用者時，留着空欄位，按 Update User ，發現結果很類似，網址從 users/id/edit 會變成 users/id。

---

驗證資料時，發現欄位爲空或是格式錯誤時要回對應的訊息給使用者，讓使用者知道要往哪個方向跑。
<!-- AC教材的範例我覺得跟理想中的差太多，所以先不特地筆記，先把這個坑註記起來：驗證後顯示提示訊息給使用者 -->

猜對啦，到 `restaurant_4_admin_create.md` 補完。
