---
title: "How I Deploy This Blog With Hugo"
date: 2017-06-30T10:55:49+08:00
draft: false
---

這篇文章留給自己與需要的人作為筆記與參考用。(貼心提示: 請務必先了解Github的操作唷！)

用Hugo建部落格的優點在於簡潔，相對於Jeykll (自己折騰了好幾天)，Hugo的目錄結構簡單許多。然而，在部署上，它卻較Jeykll來得費功夫。本篇文章分享我試誤一整天後成功的做法。

本篇文章基於[官方文件—如何部署Hugo到Github上](https://gohugo.io/tutorials/github-pages-blog/)，並在其之上做自己實作上的補充。該文提供三種部署的做法，這邊我選用第三種做法，分別建立 **存網站源碼的repo (你所編輯的部分)** 以及 **放實際編譯後的html, css, js的repo** 。這種做法的優點是使其他人容易記住你的網址。他只要打上http://\<your-username\>.github.io，即可找到你的網站！

那接下來就開始部署流程吧 ~（本篇文章預設讀者已安裝hugo工具與git工具)



**Step1.** 在Github頁面下，建兩個Repository

一個命名為**\<your-username\>-hugo**用來存你編輯的部落格的資訊，另一個命名為**\<your username\>.github.io**用來存編譯後實際顯示的頁面。

**重點提示: repo1和repo2都不要在創建時預設任何檔案，包含.gitignore和README.md，否則後面會出錯。**

e.g.

![repo1](img/build_blog_with_hugo/repo1.png)

![repo2](img/build_blog_with_hugo/repo2.png)

**Step2.** 在本機端，於command line輸入以下指令，請把**\<your-username\>**改成你自己的username

```bash
mkdir <your-username>-hugo # 建立資料夾存放網站源碼
cd <your-username>-hugo
hugo new site . # 於目錄下創建hugo網站 (注意: 這個指令只能在空目錄下達)
				# 指令的Output會給你一些Hint, 包含選擇你想要的themes
				# 這邊我選了Minimo這個theme
git init

# 根據Minimo建議的方法，minimo視為本repo的依賴，且不會儲存在自己的repo中
git submodule add https://github.com/MunifTanjim/minimo themes/minimo
git submodule init
git submodule update

# 把configuration template從themes/minimo複製到本專案root目錄
cp themes/minimo/exampleSite/config.toml .

# 接下來就可以測試一下自己的網站囉
hugo server  
# 此指令下達後, 可在瀏覽器開localhost:1313觀看網頁的雛形
# 確認無錯誤後, 按Ctrl-C關閉server

# 將fevemania.github.io也作為submodule，
# 注意，此指令要連續執行兩次，第一次他會說，
# <your-username>.github.io是空的而有error, 第二次會成功
git submodule add -b master git@github.com:<your-username>/<your-username>.github.io.git public
```

​	

**Step3.** 這一步超級重要，沒做的話，你的網頁會在沒辦法被讀取到，請用編輯器修改**config.toml**，將baseurl改成:

```vim
baseURL = "https://<your-username>.github.io/"
```

​	

**Step4.** 再來就是部署網頁到\<your-username\>.github.io，這邊用的是官方提供的deploy.sh腳本做自動化部署。

+ deploy.sh:

```bash
#!/bin/bash

echo -e "\033[0;32mDeploying updates to GitHub...\033[0m"

# Build the project.
hugo # if using a theme, replace by `hugo -t <yourtheme>`

# Go To Public folder
cd public
# Add changes to git.
git add -A

# Commit changes.
msg="rebuilding site `date`"
if [ $# -eq 1 ]
  then msg="$1"
fi
git commit -m "$msg"

# Push source and build repos.
git push origin master

# Come Back
cd ..
```

這個腳本會透過hugo指令將你的網站源碼編譯成html, css, js等實際顯示的檔案，並通過覆寫在public目錄下，這個目錄正是連接\<your-username\>.github.io的目錄，永遠不需要手動修改裡面的任何檔案。**所以，如果最前面你在Github頁面建立\<your-username\>.github.io時，自動添加.gitignore或者README.md都會導致這個腳本在push階段產生本機檔案與遠端檔案不一致，而發生錯誤喔！**

**Note:** 要用chmod +x deploy.sh把它改成可執行檔。

+ 執行deploy.sh，這時, 你已經可以在\<your-username\>.github.io看到你自己的網頁囉! (可能延遲一兩分鐘)

  ```bash
  ./deploy.sh
  ```

  ​

**Step5.** 加入.gitignore

```bash
public # 務必加入這個, public不屬於網站源碼，不需要存在<your-username>-hugo的Repo中

# 可以加入其他你不想要加入版控的檔案
```

​	

**Step6.** 是時候做Initial Commit了！

```bash
touch README.md
git add .
git commit -m "first commit"
git remote add origin git@github.com:<your-username>/<your-username>-hugo.git
git push -u origin master
```

​	

大功告成~~



接下來，你可以修改config.toml去客製一些設定。

之後，每當你改變網站源碼後，可以透過**\<your-username\>-hugo**提交，

而如果要改變網頁實際顯示，只需執行deploy.sh就ok了！

