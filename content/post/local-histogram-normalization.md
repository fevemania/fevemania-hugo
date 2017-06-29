---
title: "Contrast Limited Adaptive Histogram Equalization"
date: 2017-06-28T17:36:07+08:00
draft: false 
---

今天嘗試寫中文版的Post，接續昨天的Po文，Histograms Equalization 可以用在深度學習中針對圖片資料對比度太低的問題進行預處理 (data prepocessing)。



以下舉一個例子，這個資料集是GTSRB (German Traffic Sign Recognition Benchmark)，裡面有43種不同的路標，被用來訓練機器辨識路標號誌。



下圖為  *限速60* 的號誌，如圖可知，在不同的光影變化下，對像素強度的影響很大，會影響機器的視覺 (機器看到的就是一個個像素，如果像素值都很接近，那機器怎麼會能正確分辨出`60`呢 ?)

![limit-60-(1)](img/global_hist_equalize/before.png)

若我們將圖片轉為灰階 (簡單講就是將彩色圖片轉成黑白圖片)，並應用上一篇Po文的Global Histogram Equalization，那麼輸出結果將透過把整張像素的值分散化，改善對比度。從人的視覺角度看就是圖片對比度增強，對機器來說則是 使其更易將特定特徵 (想識別的對象) 與其背景(即特徵以外的地方) 做分離。

![limit-60-(2)](img/global_hist_equalize/after_1.png)

然而，就像上篇Po文提到的，Global Histogram Equalization的做法就是盲目地平均化整張圖片的像素強度，所以可能會導致像5370圖的情形，發生資訊的遺失 (像素被平均化)，讓分佈在`60`的像素值相對變低。



所以今天要介紹另一個實務上更為常用的 Contrast Limited Adaptive Histogram Equalization。

用一句話解釋，就是改成把整個圖片切成一個個block做局部正規化。

![limit-70](img/global_hist_equalize/after_2.png)





更多資料可見[維基百科-AHE](https://en.wikipedia.org/wiki/Adaptive_histogram_equalization#Contrast_Limited_AHE)

實作細節可見[OpenCV](http://docs.opencv.org/3.2.0/d5/daf/tutorial_py_histogram_equalization.html)

