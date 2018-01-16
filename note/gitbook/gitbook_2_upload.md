```
git init
git add README.md SUMMARY.md
git commit -m "first commit"
git remote add gitbook https://git.gitbook.com/{{UserName}}/{{Book}}.git
```

`git push -u gitbook master`  
這時候push會遇到error：

```
Username for 'https://git.gitbook.com': azraeil
Password for 'https://azraeil@git.gitbook.com':
To https://git.gitbook.com/azraeil/my-note.git
 ! [rejected]        master -> master (fetch first)
error: failed to push some refs to 'https://git.gitbook.com/azraeil/my-note.git'
hint: Updates were rejected because the remote contains work that you do
hint: not have locally. This is usually caused by another repository pushing
hint: to the same ref. You may want to first integrate the remote changes
hint: (e.g., 'git pull ...') before pushing again.
hint: See the 'Note about fast-forwards' in 'git push --help' for details.
```

* gitbook 指令還很不熟，不過我們先完成任務，這個狀況之後再解
* google 之後，下 `git pull gitbook master`  
會遇到：

```
git pull gitbook master
warning: no common commits
remote: Counting objects: 6, done.
remote: Compressing objects: 100% (5/5), done.
remote: Total 6 (delta 0), reused 6 (delta 0)
Unpacking objects: 100% (6/6), done.
From https://git.gitbook.com/azraeil/my-note
 * branch            master     -> FETCH_HEAD
 * [new branch]      master     -> gitbook/master
fatal: refusing to merge unrelated histories
```

我記得這情況讓我有點印象  
沒錯就是要在pull的時候加上 `--allow-unrelated-histories`  
因爲我本機的檔案跟github伺服器上的 repo 不同(我沒有先用clone抓github上的repo先抓下來)  

`git pull gitbook master --allow-unrelated-histories`

```
From https://git.gitbook.com/azraeil/my-note
 * branch            master     -> FETCH_HEAD
Auto-merging SUMMARY.md
CONFLICT (add/add): Merge conflict in SUMMARY.md
Auto-merging README.md
CONFLICT (add/add): Merge conflict in README.md
Automatic merge failed; fix conflicts and then commit the result.
```

修改 SUMMARY.md 與 README.md  
git add 他們  
rm chapter1.md  
git commit  

解決衝突之後：  
`git push -u gitbook master`

push 成功了  
就去你的 gitbook 頁面看看吧：)  
https://azraeil.gitbooks.io/my-note/
