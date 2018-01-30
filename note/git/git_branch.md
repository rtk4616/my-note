# Git Branch 分支
```bash
# 新增分支
git branch new_branch

# 分支改名字
git branch -m new_branch new_branch_name

# 觀察目前分支
git branch

# 觀察目前分支含 log
git branch -v

# 刪除分支
git branch -d branch_name

# 切換分支
git checkout branch_name

# 合併分支
# 先切回要留下來的分支，例如 master
git checkout master

# 接著合併指定分支
git merge branch_name

# 可以從某個 commit 裡，貼一個分支貼紙
# 手動版本：  
# 回到 <SHA1> 時的狀態
git checkout <SHA1>
# 貼 分支貼紙
git checkout -b branch_name

# 快速版本
git branch branch_name <SHA1>

git checkout -b branch_name <SHA1>
兩個指令效果一樣，checkout 的指令還會切換過去。
```
