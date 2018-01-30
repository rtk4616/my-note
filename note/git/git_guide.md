https://gitbook.tw/

# Git 筆記
## 常用指令：

```bash
# 請時時注意精神狀態
# 啊，刪錯東西了 rm file_name
# 請注意是刪到有在 git commit 記錄裡的才能用下列方式
`git checkout .`
```

```bash
# git config 設定
git config --global user.name "Name"
git config --global user.email "Email"
git config --list

# 產生 .git/ 資料夾
git init

# 觀察檔案追蹤狀態
git status

# 將檔案從 working area 移至 staging area
git add

# git add 之後，在 commit 前反悔，重置 staging area
git reset

# 觀察檔案差異，要在 add/commit 之前？
git diff

# 將檔案從 staging area 移至 local repository
git commit

# 檔案三種狀態:
# 1. working area：untracked，unstaged
# 2. git add 到 staging area 之後：staged
# 3. git commit 至 local repository：commited

# 還原 git 該<SHA1>版本記錄，但是保留檔案內容。
# Reset 這個英文單字的翻譯是「重新設定」，但事實上 Git 的 Reset 指令用中文來說比較像是「前往」或「變成」，也就是「go to」或「become」的概念。
git reset <SHA1>

# git reset mode：
# default mode = mixed mode，commit 資訊不保留，staging area 的檔案也不保留，working area 的還會在。
git reset --mixed <SHA1>

# soft mode = commit 資訊不保留，staging area 的檔案也保留，working area 的還會在。
git reset --soft <SHA1>

# hard mode = commit 資訊不保留，staging area 的檔案不保留，working area 的檔案也刪除。
git reset --hard <SHA1>


# SHA1 碼，可以用 git log 查詢
git log

# 已經被 reset 的 commit SHA1 碼 怎麼查？
# 1.
git log -g

# 2.
git reflog

# 刪除該 <SHA1> 版本的檔案內容更動，並新增一個版本記錄
git revert <SHA1>

# git reset 與 git revert 的使用情境：
# 如果該 <SHA1> 的改動全部都要還原再用 git revert，其餘請使用 git reset 與 git diff。
# 沒事別用 revert ，git revert 指令輸入下去進 commit message 畫面，連 !q 都還是會執行。

# push to Remote Repository
# 新增一個叫 origin 的 遠端 repository 書籤
git remote add origin <remote path>

# 顯示遠端書籤列表，照上面的例子會顯示：origin <remote path>
git remote -v

# 移除遠端書籤
git remote remove origin

# 第一次 push 要加 -u
git push -u origin master

# 將 master 專案推到 origin 遠端節點去
git push origin master

# 第一次，下載 git 專案
git clone <remote path>

# 後續更新專案
git pull [options] [...]

# 關於 先 github fork 再 git clone 專案 與直接 git clone 專案的差異
1. 有一說是 git clone 下來的 remote address 是該專案所有人的 address。
2. 而先 github fork 再 git clone 自己的 repo，remote address 就會是自己的，之後就能直接 push。
github fork 比較省事這件事吧。

# 建立分支書籤，概念跟 git remote add <address> 遠端書籤相同
git branch <branch_name>

# 切換 branch
git checkout <branch_name>

# git checkout 還有另一個功能？救回誤刪的檔案
git co <file_name>

# 觀察 brach 清單
git branch
# 觀察詳細 branch 清單列表
git branch -v

# 重新命名 branch，--move 可簡寫爲 -m
git branch --move branch_name_A branch_name_B

# 刪除 branch，--delete 可簡寫爲 -d，不能刪除你所在的分支，git checkout master 後再去刪
git branch --delete <branch_name>

# 用你所在的 branch 把 指定 branch 合併
git merge <branch_name>

# 當衝突發生時，編輯衝突檔案，重新 commit
<<<<<<<HEAD
# 目前分支的內容改動
=======
# 要被合併的 branch 內容改動
>>>>>>>

# 拉取 remote repo branch
git pull <遠端分支書籤> <更新哪一個 branch_name>
```

> 實際測試後覺得好可怕，還是找時間重新跑一次龍哥的`爲你自己學 git` 吧...。  
果然一知半解是最恐怖的。

---
### 刪除已經commit檔案
rm file_name

砍完之後還要將`改動(剛才刪除 file_name)`加入 staging area，稍嫌不夠直覺。  
git add file_name
git commit

#### 推薦的刪除commit檔案的方法
請 git 幫你刪除：  
`git rm file_name`  
git commit

---
### 讓被commit的檔案不再被 git 控管並保留下來
`git rm file_name --cached`

---
### 變更檔名
`mv file_name1 file_name2`

git 會先把 file_name1 刪除 (git rm filename1)  
而 file_name2 這時候是在 working area  
因此要先把該檔案加到 staging area  
`git add file_name2`
git commit

#### 推薦的變更commit檔名的方法
`git mv file_name1 file_name2`

---
### 修改 commit 記錄
`改 commit 記錄都會重新 commit 一次，就算是 commit 檔案內容都一樣，所以不要再 push 出去之後修改，會造成其他人的困擾。`

```bash
# 修改 git commit 歷史記錄
git rebase

# git reset 拆掉 commit 重新 commit
git reset

# 直接修改最後一次 commit 內容
git commit --amend
```

> amend 的意思就是修改！

---
### 追加檔案到最近一次的 commit
有兩種方式，一個是 reset，另一個是透過 --amend。

一樣先把追加檔案加到 staging area。

```bash
git add file_name
# --no-edit 要加不加都可以
git commit --amend --no-edit
```

這一樣會改變 commit 歷史，所以 push 出去的不要用。

---
### 修改提交歷史訊息
git log --oneline  
觀察 SHA1 history，然後進入 git rebase 互動模式：  
```bash
git rebase -i a_sha1
```

接著會跳出 vim 編輯頁面，會秀出從你現在到 a_sha1 的所有 commit 內容。

```vim
pick bc5d483 Update README img size.
pick 74fcecc Upload the note of deploy rails app to heroku.
pick aa91d56 Fix markdown syntax.
pick 7ebe9b3 Fix markdown syntax error.

# Rebase db0fe3d..7ebe9b3 onto db0fe3d (4 commands)
#
# Commands:
# p, pick = use commit
# r, reword = use commit, but edit the commit message
# e, edit = use commit, but stop for amending
# s, squash = use commit, but meld into previous commit
# f, fixup = like "squash", but discard this commit's log message
# x, exec = run command (the rest of the line) using shell
# d, drop = remove commit
#
# These lines can be re-ordered; they are executed from top to bottom.
#
# If you remove a line here THAT COMMIT WILL BE LOST.
#
# However, if you remove everything, the rebase will be aborted.
#
# Note that empty commits are commented out
```

上面是你的 commit log 從 舊的(你指定的SHA1) 到最新的。

下面有指令對照表，pick 就是保留 commit 內容不修改。

reword 就是保留 commit ，但是修改 commit 資訊。

要特別注意的是就算只改 commit message ，也算是全新 commit 了。

> 如何取消剛才做的 git rebase？  
> git reset ORIG_HEAD --hard
看到 reset --hard 就能瞭解，沒事別用 rebase。

---
### 如何合併 commit ？
還記得 git rebase 的互動模式嗎？當中有個指令 `squash`。  
squash = use commit, but meld into previous commit

只要在 vim 編輯器把 commit 前面的指令改成 squash ，那該 commit 就會跟他上一個 commit (比較舊的)合併！

> push 出去之後就別動了吧

### 那把 commit 拆散呢？
一樣透過 rebase，指令換成 `edit`。  
edit = use commit, but stop for amending

存檔之後，git 會開始跑 commit 直到`HEAD`跑到你指定的那個 commit。  
這時候就能下 git reset HEAD^ 將狀態還原至上一次 commit 了。

修改好，git add，git commit 完成之後要記得下：  
`git rebase --continue`  
讓 git 把後面的 commit 都跑完。

### 在某些 commit 之間增加 commit
方式就與把 commit 拆散的方法一樣。

### 刪除 commit ？
也是用 git rebase 然後用指令 `drop`，或是把那行 commit 刪除。

### 調整 commit 順序？
這個功能照理說不該被使用的才是，不然修改檔案的前後順序一變很可能發生不可預期的災難。  
一樣用 git rebase ，直接在 vim 內調整順序就可以了。

## git reset, revert, rebase 的差異
`git revert HEAD`  
git revert 是用一個新 commit 去抵消指定的 commit。

根據情況，單人開發的話不太會介意你用 reset 還是 revert，團隊開發可能就有要求了。  
因爲 reset 跟 rebase 都是會改變 commit 歷史記錄的。

---
### 不想被 git 控管的檔案
touch .gitignore

---
### 檢視特定檔案 commit log
我過去是比較習慣去GUI看該檔的整體改動。  
`git log <file_name>`

觀察 log 的同時顯示 diff  
`git log -p <file_name>`

---
### 確定該行程式碼是誰寫的
`git blame file_name`

指定行數  
`git blame -L 5,10 file_name`

---
### commit 空目錄？
空目錄是無法被 git add 成功的，依照慣例，可以在該空資料夾底下放 .keep 與 .gitkeep。

---
### 只 commit 檔案的部分內容
`git add -p file_name`  
接著選 `e`，選 `y` 會整個檔案都加進去。

---
### git 標籤 tag 是什麼？
當開發到一定階段就可以貼個標籤，像是 v1.0.0 或是 beta-release 之類的。

git log --oneline

找尋想貼標籤的 SHA1。

#### 貼上標籤
`git tag tag_name <SHA1>`

這樣就貼好了，git log --oneline 觀察。  
這是最單純的輕量標籤(lightweight tag)。

#### 貼上標籤 with 附註，通常用這個，可以註明時間，誰，爲什麼
`git tag tag_name <SHA1> -a`

> 標籤與分支的差異？  
> 分支在 commit 之後，HEAD就會跟著移動，但是標籤不會移動。

---
### 手邊工作到一半，臨時要切換到別的任務
這個時候有幾種做法：  
1. 就放著，但是沒備份，怕。
2. 先快速 commit 一次，之後回來再用 git reset HEAD^。
3. 使用 Stash

#### git stash
要先把編輯過的檔案加入 staging area:
`git add .`  

接著下 `git stash`

用 `git status` 觀察

若是不想 git add 的(untracked)檔案，可以用 `git stash -u`

##### git stash 清單
`git stash list`

把 stash 撿回來繼續工作，pop 會在檔案套用回來之後把該 stash 刪除：  
`git stash pop stash@{stash_number}`

想保留 stash 的話就是用 `apply`

把 stash 刪除：  
`git stash drop stash@{stash_number}`

我覺得使用時機要看工作分類：  
1. 待會回來馬上接著做，那我覺得用 commit 跟 reset 就行。
2. 不過要留到之後有機會再做又不想開分支，或許可以用 stash。
