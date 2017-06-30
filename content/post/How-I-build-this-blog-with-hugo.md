---
title: "How I Deploy This Blog With Hugo"
date: 2017-06-30T10:55:49+08:00
draft: true
---

這篇文章留給自己與需要的人作為筆記與參考用。(貼心提示: 請務必先了解Github的操作唷！)

用Hugo建部落格的優點在於簡潔，相對於Jeykll (自己折騰了好幾天)，Hugo的目錄結構簡單許多。然而，在部署上，它卻較Jeykll來得費功夫。本篇文章分享我試誤一整天後成功的做法。

本篇文章做法基於[官方文件—如何部署Hugo到Github上](https://gohugo.io/tutorials/github-pages-blog/)做自己實作上的補充。該文提供三種部署的做法，這邊我選用第三種做法，分別建立 **存網站源碼的repo (你所編輯的部分)** 以及 **放實際編譯後的html, css, js的repo** 。這種做法的優點是使其他人容易記住你的網址。他只要打上http://<username>.github.io，即可找到你的網站！

那接下來就開始部署流程吧 ~（本篇文章預設讀者已安裝hugo工具與git工具)



**Step1.** 在Github頁面下，建兩個Repository

一個命名為**\<your username\>-hugo**用來存你編輯的部落格的資訊，另一個命名為**\<your username\>.github.io**用來存編譯後實際顯示的頁面。

**重點提示: repo1和repo2都不要在創建時預設任何檔案，包含.gitignore和README.md，否則後面會出錯。**

e.g.

![repo1](img/build_blog_with_hugo/repo1.png)

![repo2](img/build_blog_with_hugo/repo2.png)

**Step2.** 在本機端，於command line輸入以下指令，請把**fevemania**改成你自己的username

```bash
mkdir fevemania-hugo # 建立資料夾存放網站源碼
cd fevemanai-hugo
hugo new site . # 於目錄下創建hugo網站 (注意: 這個指令只能在空目錄下達)
				# 指令的Output會給你一些Hint, 包含選擇你想要的themes
				# 這邊我選了Minimo這個theme
git clone https://github.com/MunifTanjim/minimo themes/minimo
```

