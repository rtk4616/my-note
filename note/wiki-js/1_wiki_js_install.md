### A note for wiki.js

[wiki.js Prerequisites](https://docs.requarks.io/wiki/prerequisites)

Requirements:
* need a linux server
* need install :
  - Node.js
    - `curl -sL https://deb.nodesource.com/setup_9.x | sudo -E bash -`
    - `sudo apt-get install -y nodejs`
    - `sudo apt-get install -y build-essential`

  - MongoDB
    - `sudo apt-get install -y mongodb-org`
    - 會說找不到要安裝啥: `E: Unable to locate package mongodb-org
`
    - > 不能直接下這行命令
    - 要乖乖照 [mongodb manual](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-ubuntu/) 跑

    - `sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv 2930ADAE8CAF5059EE73BB4B58712A2291FA4AD5`
    - `echo "deb [ arch=amd64 ] https://repo.mongodb.org/apt/ubuntu precise/mongodb-org/3.6 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-3.6.list`
    - `sudo apt-get update`
    - `sudo apt-get install -y mongodb-org`

  - **Git**
  - An empty Git repository...可以用 bitbucket 達成全部 private 化
  - 不過安裝階段還沒用到，應該是安裝之後再設定就行

## Install wiki.js
```bash
# 創一個新資料夾
mkdir wiki_js
cd wiki_js
# 安裝 wiki.js 在這個資料夾
curl -sSo- https://wiki.js.org/install.sh | bash
```
