Rime 輸入法安裝方式：

### MAC

#### 在 mac 當然乖乖用 homebrew 裝
我有用官方安裝檔裝過測試一次(移除很麻煩)
乖乖用 homebrew 裝。

`brew cask install squirrel`
安裝過程會要你輸入密碼。

移除 Rime：
`brew cask uninstall squirrel`


#### 裝完之後，這一步最重要
登出MAC  
登出MAC  
登出MAC  
原因好像是因為MAC使用者要註冊。

#### 登出之後，打開鍵盤偏好設定，
把鼠鬚管輸入法加入選單

#### `command + space` 切換到 Rime 之後，

按 command + \` (tab上面那顆按鍵)
看看能不能跳出選單

不行的話請debug

#### 安裝洋蔥注音設定檔
符號跟洋蔥版注音的設定檔跟WINDOWS的可以共通

請至`客製化開始`觀看

* WINDOWS10
  - 要裝 PRIME，[下載](https://github.com/osfans/PRIME/releases)
  - 網友前輩們似乎[不推薦沒在更新的小狼毫](https://mind.momok.xyz/archives/replace-rime-ime-with-prime.html)
  - 裝預設的那個就行，中州韻輸入法（RIME）
  - 設定檔會在 `C:\Users\Administrator\PIME\rime`
  - Users 後是你的使用者名稱

##客製化開始!!
### 下載L’Étranger(deltazone)大大的洋蔥注音設定檔：
* https://goo.gl/T3Epk4 RIME注音設定檔（20171217更新）

### 修改兩個檔案：
#### 改 default.yaml 裡的輸入法選單 `schema_list:`
只留下這行 `- schema: bopomo_onion`  
測試有沒有成功，右下角輸入法選單右鍵，`重新佈署`，記得先切到別的輸入法  
按 ctrl + \`，應該就能看到洋蔥輸入法啦

#### 改 bopomo_onion.schema.yaml

```
    half_shape:
      "<" : { commit: "，" }
      ">" : { commit: "。" }
      "?" : { commit: "？" }
      ":" : { commit: "：" }
      "'" : { commit: "'" }
      "\"" : { commit: "\"" }
      "\\" : { commit: "\\" }
      "|" : { commit: "|" }
      "`": { commit: "`" }
      "~" : { commit: "~" }
      "!" : { commit: "！" }
      "@" : { commit: "@" }
      "#" : { commit: "#" }
      "%" : { commit: "%" }
      "$" : { commit: "$" }
      "^" : { commit: "^" }
      "&" : { commit: "&" }
      "*" : { commit: "*" }
      "(" : { commit: "(" }
      ")" : { commit: ")" }
      "_" : { commit: "_" }
      "+" : { commit: "+" }
      "=" : { commit: "=" }
      "[" : { commit: "[" }
      "]" : { commit: "]" }
      "{" : { commit: "{" }
      "}" : { commit: "}" }
    import_preset: bopomo_symbols
```

`重新佈署`
搞定!! Enjoy it !!
