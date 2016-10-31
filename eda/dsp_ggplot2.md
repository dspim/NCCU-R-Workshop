---
title       : EDA with R
subtitle    : 智庫驅動
author      : Ben Chen
job         : 
framework   : io2012-dsp
highlighter : highlight.js
hitheme     : zenburn
widgets     : [mathjax]            # {mathjax, quiz, bootstrap}
mode        : selfcontained # {standalone, draft}
knit        : slidify::knit2slides
--- 
## 今日重點!!!

### 畫圖～

### ggplot2

### 需要的套件
    library(ggplot2)
    library(data.table)
    library(dplyr)
    library(reshape2)




--- .dark .segue

## ggplot2 簡介

--- &vcenter .largecontent

## ggplot2簡介

- 2015年，最受歡迎的R套件之一
- R環境下的繪圖套件
- 取自 “The Grammar of Graphics” (Leland Wilkinson, 2005)
- 設計理念
  - 採用圖層系統
  - 用抽象的概念來控制圖形，避免細節繁瑣
  - 圖形美觀

--- &vcenter .largecontent

## The Anatomy of a Plot 

<img src='./img/anatomy.png' width=900 align='center'></img>



--- &vcenter .largecontent

## ggplot2核心

- 注意事項
  - 使用 data.frame 儲存資料 (不可以丟 matrix 物件)
  - 使用 long format (利用reshape2將資料轉換成 1 row = 1 observation)
- 基本語法
  - ggplot 描述 data 從哪來
  - aes 描述圖上的元素跟 data 之類的對應關係
  - geom_xxx 描述要畫圖的類型及相關調整的參數
  - 常用的類型諸如：geom_bar, geom_points, geom_line, geom_polygon


--- &vcenter .largecontent


## 一切從讀檔開始 (CSV)

```r
# 讀檔起手式
ubike = read.csv('ubikeweatherutf8.csv') #請輸入正確的檔案路徑
# 讀檔進階招式
ubike = read.csv('檔案路徑', 
          colClasses = c("factor","integer","integer","factor","factor",
                         "numeric","numeric","integer","numeric","integer",
                         "integer","numeric","numeric", "integer","integer",
                         "numeric","numeric","numeric", "numeric","numeric",
                         "numeric"))
# 讀檔大絕招
ubike = fread('檔案路徑',
          data.table = FALSE,
          colClasses = c("factor","integer","integer","factor",
                        "factor","numeric", "numeric", "integer",
                        "numeric", "integer","integer","numeric",
                        "numeric", "integer","integer","numeric",
                        "numeric","numeric", "numeric","numeric",
                        "numeric"))
```

--- .dark .segue

## 請輸入正確的檔案路徑

--- &vcenter .largecontent
## 將欄位名稱換成中文 

```r
colnames(ubike) <- 
  c("日期", "時間", "場站代號", "場站區域", "場站名稱", 
  "緯度", "經度", "總停車格", "平均車輛數", "最大車輛數", 
  "最小車輛數", "車輛數標準差", "平均空位數", "最大空位數", 
  "最小空位數", "空位數標準差", "平均氣溫", "溼度", 
  "氣壓", "最大風速", "降雨量")
```

--- .dark .segue

## 單一數值：Histogram

--- &vcenter .largecontent

## Histogram

```r
thm <- theme(text=element_text(size=20,family="STHeiti")) # 控制字體與大小
# STHeiti是只有Mac才有的字體
ggplot(ubike) +
  geom_histogram(aes(x = 最大風速, y=..count..))+thm
```

<img src="assets/fig/wind1-1.png" title="plot of chunk wind1" alt="plot of chunk wind1" style="display: block; margin: auto;" />

--- &vcenter .largecontent

## Histogram

```r
ggplot(ubike) +
  geom_histogram(aes(x = 最大風速, y=..density..))+thm
```

<img src="assets/fig/wind2-1.png" title="plot of chunk wind2" alt="plot of chunk wind2" style="display: block; margin: auto;" />

--- &vcenter .largecontent

## Histogram

```r
ggplot(ubike) +
  geom_histogram(aes(x = 最大風速, y=..density..,fill=..count..))+thm
```

<img src="assets/fig/wind3-1.png" title="plot of chunk wind3" alt="plot of chunk wind3" style="display: block; margin: auto;" />

--- &vcenter .largecontent

## Histogram + Density

```r
ggplot(ubike,aes(x = 最大風速)) +
  geom_histogram(aes(y=..density..,fill=..count..))+
  geom_density()+thm
```

<img src="assets/fig/wind4-1.png" title="plot of chunk wind4" alt="plot of chunk wind4" style="display: block; margin: auto;" />

--- .dark .segue

## 量化 v.s. 量化：Scatter Plot

--- &vcenter .largecontent

## 繪圖之前的整理資料

### 文山區各站點在"2015-02"的平均溼度 vs. 平均雨量


```r
x3 <- filter(ubike, grepl("2015-02", 日期, fixed = TRUE), 場站區域 == "文山區") %>%
  group_by(場站名稱) %>% 
  summarise(平均降雨量 = mean(降雨量), 平均溼度 = mean(溼度))
```

--- .largecontent

## Scatter Plot


```r
ggplot(x3) +
  geom_point(aes(x = 平均溼度, y = 平均降雨量),size=5) + #size控制點的大小
  thm
```

<img src="assets/fig/ubike.site.wet.rainfall2-1.png" title="plot of chunk ubike.site.wet.rainfall2" alt="plot of chunk ubike.site.wet.rainfall2" style="display: block; margin: auto;" />

--- .largecontent

## Grouped Scatter Plot


```r
ggplot(x3) +
  # 放在aes裡的colour和size可依資料調整顏色和大小
  geom_point(aes(x = 平均溼度, y = 平均降雨量, colour = 場站名稱,size=平均降雨量))+
  # 限制大小
  scale_size(range=c(5,10)) +  
  thm
```

--- .largecontent

## Grouped Scatter Plot

<img src="assets/fig/ubike.site.wet.rainfall3-1.png" title="plot of chunk ubike.site.wet.rainfall3" alt="plot of chunk ubike.site.wet.rainfall3" style="display: block; margin: auto;" />

--- .dark .segue

## 量化 v.s. 量化：Line Chart

--- 

## WorldPhones

```
##      N.Amer Europe Asia S.Amer Oceania
## 1951  45939  21574 2876   1815    1646
## 1956  60423  29990 4708   2568    2366
## 1957  64721  32510 5230   2695    2526
## 1958  68484  35218 6662   2845    2691
## 1959  71799  37598 6856   3000    2868
## 1960  76036  40341 8220   3145    3054
## 1961  79831  43173 9053   3338    3224
##      Africa Mid.Amer
## 1951     89      555
## 1956   1411      733
## 1957   1546      773
## 1958   1663      836
## 1959   1769      911
## 1960   1905     1008
## 1961   2005     1076
```

--- &vcenter .largecontent

## 每年亞洲的電話數量

    ggplot(WorldPhones,aes(x=?????,y=Asia))......

--- &vcenter .largecontent

## 哪裏不對？

```r
class(WorldPhones)
```

```
## [1] "matrix"
```

--- &vcenter .largecontent

## data.frame

```r
WP.df=as.data.frame(WorldPhones)
WP.df$year <- rownames(WP.df)
class(WP.df)
```

```
## [1] "data.frame"
```

--- &vcenter .largecontent

## Line Chart???

```r
ggplot(WP.df,aes(x=year,y=Asia))+geom_line()
```

```
## geom_path: Each group consist of only one observation. Do you need to adjust the group aesthetic?
```

![plot of chunk wp4 ](assets/fig/wp4 -1.png) 

--- &vcenter .largecontent

## Should be Number

```r
str(WP.df)
```

```
## 'data.frame':	7 obs. of  8 variables:
##  $ N.Amer  : num  45939 60423 64721 68484 71799 ...
##  $ Europe  : num  21574 29990 32510 35218 37598 ...
##  $ Asia    : num  2876 4708 5230 6662 6856 ...
##  $ S.Amer  : num  1815 2568 2695 2845 3000 ...
##  $ Oceania : num  1646 2366 2526 2691 2868 ...
##  $ Africa  : num  89 1411 1546 1663 1769 ...
##  $ Mid.Amer: num  555 733 773 836 911 ...
##  $ year    : chr  "1951" "1956" "1957" "1958" ...
```

```r
WP.df$year=as.numeric(WP.df$year)
```

--- &vcenter .largecontent

## Line Chart!!!

```r
ggplot(WP.df,aes(x=year,y=Asia))+
  geom_line()+thm
```

<img src="assets/fig/wp6-1.png" title="plot of chunk wp6" alt="plot of chunk wp6" style="display: block; margin: auto;" />

--- &vcenter .largecontent

## Line Chart and Scatter Ploet

```r
ggplot(WP.df,aes(x=year,y=Asia))+
  geom_line(size=2)+ #size控制線的寬度或點的大小
  geom_point(size=5)+thm
```

<img src="assets/fig/wp7-1.png" title="plot of chunk wp7" alt="plot of chunk wp7" style="display: block; margin: auto;" />

--- &vcenter .largecontent

## How to plot multiple line?

### Wide format
<!-- html table generated in R 3.2.2 by xtable 1.8-0 package -->
<!-- Mon Nov 30 08:10:28 2015 -->
<table border=1>
<tr> <th>  </th> <th> N.Amer </th> <th> Europe </th> <th> Asia </th> <th> S.Amer </th> <th> Oceania </th> <th> Africa </th> <th> Mid.Amer </th> <th> year </th>  </tr>
  <tr> <td align="right"> 1951 </td> <td align="right"> 45939.00 </td> <td align="right"> 21574.00 </td> <td align="right"> 2876.00 </td> <td align="right"> 1815.00 </td> <td align="right"> 1646.00 </td> <td align="right"> 89.00 </td> <td align="right"> 555.00 </td> <td align="right"> 1951.00 </td> </tr>
  <tr> <td align="right"> 1956 </td> <td align="right"> 60423.00 </td> <td align="right"> 29990.00 </td> <td align="right"> 4708.00 </td> <td align="right"> 2568.00 </td> <td align="right"> 2366.00 </td> <td align="right"> 1411.00 </td> <td align="right"> 733.00 </td> <td align="right"> 1956.00 </td> </tr>
  <tr> <td align="right"> 1957 </td> <td align="right"> 64721.00 </td> <td align="right"> 32510.00 </td> <td align="right"> 5230.00 </td> <td align="right"> 2695.00 </td> <td align="right"> 2526.00 </td> <td align="right"> 1546.00 </td> <td align="right"> 773.00 </td> <td align="right"> 1957.00 </td> </tr>
  <tr> <td align="right"> 1958 </td> <td align="right"> 68484.00 </td> <td align="right"> 35218.00 </td> <td align="right"> 6662.00 </td> <td align="right"> 2845.00 </td> <td align="right"> 2691.00 </td> <td align="right"> 1663.00 </td> <td align="right"> 836.00 </td> <td align="right"> 1958.00 </td> </tr>
  <tr> <td align="right"> 1959 </td> <td align="right"> 71799.00 </td> <td align="right"> 37598.00 </td> <td align="right"> 6856.00 </td> <td align="right"> 3000.00 </td> <td align="right"> 2868.00 </td> <td align="right"> 1769.00 </td> <td align="right"> 911.00 </td> <td align="right"> 1959.00 </td> </tr>
  <tr> <td align="right"> 1960 </td> <td align="right"> 76036.00 </td> <td align="right"> 40341.00 </td> <td align="right"> 8220.00 </td> <td align="right"> 3145.00 </td> <td align="right"> 3054.00 </td> <td align="right"> 1905.00 </td> <td align="right"> 1008.00 </td> <td align="right"> 1960.00 </td> </tr>
  <tr> <td align="right"> 1961 </td> <td align="right"> 79831.00 </td> <td align="right"> 43173.00 </td> <td align="right"> 9053.00 </td> <td align="right"> 3338.00 </td> <td align="right"> 3224.00 </td> <td align="right"> 2005.00 </td> <td align="right"> 1076.00 </td> <td align="right"> 1961.00 </td> </tr>
   </table>

$$\Downarrow$$

--- &vcenter .largecontent

### Long format

```r
library(reshape2)
WP.long=melt(WP.df,id='year') #id是將保留的欄位名稱
colnames(WP.long)=c('year','area','number')
```
<!-- html table generated in R 3.2.2 by xtable 1.8-0 package -->
<!-- Mon Nov 30 08:10:28 2015 -->
<table border=1>
<tr> <th>  </th> <th> year </th> <th> area </th> <th> number </th>  </tr>
  <tr> <td align="right"> 1 </td> <td align="right"> 1951.00 </td> <td> N.Amer </td> <td align="right"> 45939.00 </td> </tr>
  <tr> <td align="right"> 2 </td> <td align="right"> 1956.00 </td> <td> N.Amer </td> <td align="right"> 60423.00 </td> </tr>
  <tr> <td align="right"> 3 </td> <td align="right"> 1957.00 </td> <td> N.Amer </td> <td align="right"> 64721.00 </td> </tr>
  <tr> <td align="right"> 4 </td> <td align="right"> 1958.00 </td> <td> N.Amer </td> <td align="right"> 68484.00 </td> </tr>
  <tr> <td align="right"> 5 </td> <td align="right"> 1959.00 </td> <td> N.Amer </td> <td align="right"> 71799.00 </td> </tr>
  <tr> <td align="right"> 6 </td> <td align="right"> 1960.00 </td> <td> N.Amer </td> <td align="right"> 76036.00 </td> </tr>
  <tr> <td align="right"> 7 </td> <td align="right"> 1961.00 </td> <td> N.Amer </td> <td align="right"> 79831.00 </td> </tr>
  <tr> <td align="right"> 8 </td> <td align="right"> 1951.00 </td> <td> Europe </td> <td align="right"> 21574.00 </td> </tr>
  <tr> <td align="right"> 9 </td> <td align="right"> 1956.00 </td> <td> Europe </td> <td align="right"> 29990.00 </td> </tr>
  <tr> <td align="right"> 10 </td> <td align="right"> 1957.00 </td> <td> Europe </td> <td align="right"> 32510.00 </td> </tr>
  <tr> <td align="right"> 11 </td> <td align="right"> 1958.00 </td> <td> Europe </td> <td align="right"> 35218.00 </td> </tr>
  <tr> <td align="right"> 12 </td> <td align="right"> 1959.00 </td> <td> Europe </td> <td align="right"> 37598.00 </td> </tr>
  <tr> <td align="right"> 13 </td> <td align="right"> 1960.00 </td> <td> Europe </td> <td align="right"> 40341.00 </td> </tr>
  <tr> <td align="right"> 14 </td> <td align="right"> 1961.00 </td> <td> Europe </td> <td align="right"> 43173.00 </td> </tr>
  <tr> <td align="right"> 15 </td> <td align="right"> 1951.00 </td> <td> Asia </td> <td align="right"> 2876.00 </td> </tr>
  <tr> <td align="right"> 16 </td> <td align="right"> 1956.00 </td> <td> Asia </td> <td align="right"> 4708.00 </td> </tr>
  <tr> <td align="right"> 17 </td> <td align="right"> 1957.00 </td> <td> Asia </td> <td align="right"> 5230.00 </td> </tr>
  <tr> <td align="right"> 18 </td> <td align="right"> 1958.00 </td> <td> Asia </td> <td align="right"> 6662.00 </td> </tr>
  <tr> <td align="right"> 19 </td> <td align="right"> 1959.00 </td> <td> Asia </td> <td align="right"> 6856.00 </td> </tr>
  <tr> <td align="right"> 20 </td> <td align="right"> 1960.00 </td> <td> Asia </td> <td align="right"> 8220.00 </td> </tr>
  <tr> <td align="right"> 21 </td> <td align="right"> 1961.00 </td> <td> Asia </td> <td align="right"> 9053.00 </td> </tr>
  <tr> <td align="right"> 22 </td> <td align="right"> 1951.00 </td> <td> S.Amer </td> <td align="right"> 1815.00 </td> </tr>
  <tr> <td align="right"> 23 </td> <td align="right"> 1956.00 </td> <td> S.Amer </td> <td align="right"> 2568.00 </td> </tr>
  <tr> <td align="right"> 24 </td> <td align="right"> 1957.00 </td> <td> S.Amer </td> <td align="right"> 2695.00 </td> </tr>
  <tr> <td align="right"> 25 </td> <td align="right"> 1958.00 </td> <td> S.Amer </td> <td align="right"> 2845.00 </td> </tr>
  <tr> <td align="right"> 26 </td> <td align="right"> 1959.00 </td> <td> S.Amer </td> <td align="right"> 3000.00 </td> </tr>
  <tr> <td align="right"> 27 </td> <td align="right"> 1960.00 </td> <td> S.Amer </td> <td align="right"> 3145.00 </td> </tr>
  <tr> <td align="right"> 28 </td> <td align="right"> 1961.00 </td> <td> S.Amer </td> <td align="right"> 3338.00 </td> </tr>
  <tr> <td align="right"> 29 </td> <td align="right"> 1951.00 </td> <td> Oceania </td> <td align="right"> 1646.00 </td> </tr>
  <tr> <td align="right"> 30 </td> <td align="right"> 1956.00 </td> <td> Oceania </td> <td align="right"> 2366.00 </td> </tr>
  <tr> <td align="right"> 31 </td> <td align="right"> 1957.00 </td> <td> Oceania </td> <td align="right"> 2526.00 </td> </tr>
  <tr> <td align="right"> 32 </td> <td align="right"> 1958.00 </td> <td> Oceania </td> <td align="right"> 2691.00 </td> </tr>
  <tr> <td align="right"> 33 </td> <td align="right"> 1959.00 </td> <td> Oceania </td> <td align="right"> 2868.00 </td> </tr>
  <tr> <td align="right"> 34 </td> <td align="right"> 1960.00 </td> <td> Oceania </td> <td align="right"> 3054.00 </td> </tr>
  <tr> <td align="right"> 35 </td> <td align="right"> 1961.00 </td> <td> Oceania </td> <td align="right"> 3224.00 </td> </tr>
  <tr> <td align="right"> 36 </td> <td align="right"> 1951.00 </td> <td> Africa </td> <td align="right"> 89.00 </td> </tr>
  <tr> <td align="right"> 37 </td> <td align="right"> 1956.00 </td> <td> Africa </td> <td align="right"> 1411.00 </td> </tr>
  <tr> <td align="right"> 38 </td> <td align="right"> 1957.00 </td> <td> Africa </td> <td align="right"> 1546.00 </td> </tr>
  <tr> <td align="right"> 39 </td> <td align="right"> 1958.00 </td> <td> Africa </td> <td align="right"> 1663.00 </td> </tr>
  <tr> <td align="right"> 40 </td> <td align="right"> 1959.00 </td> <td> Africa </td> <td align="right"> 1769.00 </td> </tr>
  <tr> <td align="right"> 41 </td> <td align="right"> 1960.00 </td> <td> Africa </td> <td align="right"> 1905.00 </td> </tr>
  <tr> <td align="right"> 42 </td> <td align="right"> 1961.00 </td> <td> Africa </td> <td align="right"> 2005.00 </td> </tr>
  <tr> <td align="right"> 43 </td> <td align="right"> 1951.00 </td> <td> Mid.Amer </td> <td align="right"> 555.00 </td> </tr>
  <tr> <td align="right"> 44 </td> <td align="right"> 1956.00 </td> <td> Mid.Amer </td> <td align="right"> 733.00 </td> </tr>
  <tr> <td align="right"> 45 </td> <td align="right"> 1957.00 </td> <td> Mid.Amer </td> <td align="right"> 773.00 </td> </tr>
  <tr> <td align="right"> 46 </td> <td align="right"> 1958.00 </td> <td> Mid.Amer </td> <td align="right"> 836.00 </td> </tr>
  <tr> <td align="right"> 47 </td> <td align="right"> 1959.00 </td> <td> Mid.Amer </td> <td align="right"> 911.00 </td> </tr>
  <tr> <td align="right"> 48 </td> <td align="right"> 1960.00 </td> <td> Mid.Amer </td> <td align="right"> 1008.00 </td> </tr>
  <tr> <td align="right"> 49 </td> <td align="right"> 1961.00 </td> <td> Mid.Amer </td> <td align="right"> 1076.00 </td> </tr>
   </table>

--- .largecontent
## Multiple Line

```r
ggplot(WP.long,aes(x=year,y=number,group=area,color=area))+ # gruop按照不同區域劃線
  geom_line(size=1.5)+
  geom_point(size=5)+thm
```

<img src="assets/fig/wp11-1.png" title="plot of chunk wp11" alt="plot of chunk wp11" style="display: block; margin: auto;" />



--- .dark .segue

## 質化 v.s. 量化：Bar Chart

--- &vcenter .largecontent
## 讀取檔案



```r
pixnet=read.csv('train.csv',stringsAsFactors = FALSE)
```

- 2014-11-01 至 2014-11-30 期間，10000 筆隨機取樣的台灣地區網站訪客的瀏覽紀錄

--- &vcenter 
## 欄位說明
- url_hash - 去識別後的部落格文章 url
- resolution - 瀏覽裝置的螢幕解析度
- browser - 瀏覽裝置的瀏覽器
- os - 瀏覽裝置的作業系統
- device_marketing - 瀏覽裝置的產品型號
- device_brand - 瀏覽裝置的品牌名稱
- cookie_pta - 去識別化的瀏覽者代碼
- date - 瀏覽日期
- author_id - 文章作者 ID 去識別碼
- category_id - 文章分類
- referrer_venue - 訪客來源（網域）

--- 
## Bar Chart


```r
ggplot(pixnet,aes(x=referrer_venue))+
  geom_bar(stat='bin')+thm # stat='bin'算個數
```

<img src="assets/fig/pix1-1.png" title="plot of chunk pix1" alt="plot of chunk pix1" style="display: block; margin: auto;" />

---
## 兩種類別


```r
ub2=filter(ubike, 場站區域=='中和區',時間==8) %>% 
  mutate(is.rain=降雨量>1) %>%
  mutate(is.rain=factor(is.rain, levels=c(FALSE, TRUE), 
                        labels = c("晴天","雨天"))) %>%
  select(日期,  平均空位數, 場站名稱, is.rain,總停車格) %>%
  group_by(場站名稱,  is.rain) %>%
  summarise(use_rate=mean(平均空位數/總停車格)) 
head(ub2)
```

```
## Source: local data frame [6 x 3]
## Groups: 場站名稱 [3]
## 
##         場站名稱 is.rain  use_rate
##           (fctr)  (fctr)     (dbl)
## 1 捷運永安市場站    晴天 0.6671052
## 2 捷運永安市場站    雨天 0.6483044
## 3       秀山國小    晴天 0.4966519
## 4       秀山國小    雨天 0.4436588
## 5       中和公園    晴天 0.6363115
## 6       中和公園    雨天 0.5917228
```

--- &vcenter .largecontent
## 兩種類別


```r
las2 <- theme(axis.text.x = element_text(angle = 90, hjust = 1),
              text=element_text(size=20,family="STHeiti")) #控制字的方向
ggplot(ub2,aes(x=場站名稱,y=use_rate,fill=is.rain))+
  geom_bar(stat='identity')+
  las2 # stat='identity'以表格的值做為bar的高度
```

--- &vcenter .largecontent
## 兩種類別: stack

<img src="assets/fig/ubar1-1.png" title="plot of chunk ubar1" alt="plot of chunk ubar1" style="display: block; margin: auto;" />

--- &vcenter .largecontent
## 兩種類別: dodge


```r
ggplot(ub2,aes(x=場站名稱,y=use_rate,fill=is.rain))+
  geom_bar(stat='identity',position = 'dodge')+las2 #dodge類別並排
```

<img src="assets/fig/ubar2-1.png" title="plot of chunk ubar2" alt="plot of chunk ubar2" style="display: block; margin: auto;" />

--- &vcenter .largecontent
## Pie Chart: Bar Chart變形
### 整理資料

```r
pix=data.frame(table(pixnet$referrer_venue)) #table可以算個類別個數
colnames(pix)=c('入口網站','數量')
pix[5,2]=pix[5,2]+pix[1,2]
pix=pix[-1,]
```

--- &vcenter .largecontent
## Pie Chart: Bar Chart變形
![plot of chunk pix3](assets/fig/pix3-1.png) 

--- &vcenter .largecontent
## Pie Chart: Bar Chart變形

```r
ggplot(pix,aes(x="",y=數量,fill=入口網站))+
  geom_bar(stat='identity',width=1)+
  coord_polar('y')+
  geom_text(aes(y = 數量*0.5+ c(0, cumsum(數量)[-length(數量)]), 
                label = paste(round(數量/sum(數量),3)*100,'%',sep="")),
            size=7)+
  theme(axis.title.y = element_blank(),
        axis.text.x=element_blank(),
        panel.grid=element_blank(),
        text=element_text(size=20,family="STHeiti"))
```


--- .dark .segue

## The Grammer of Graphics

--- &vcenter .largecontent

## ggplot2基本架構

- 資料 (data) 和映射 (mapping)
- 幾何對象 (<font color='red'>geom</font>etric)
- 座標尺度 (<font color='red'>scale</font>)
- 統計轉換 (<font color='red'>stat</font>istics)
- 座標系統 (<font color='red'>coord</font>inante)
- 圖層 (layer)
- 刻面 (<font color='red'>facet</font>)
- 主題 (<font color='red'>theme</font>)

---
## Data and Mapping


```r
ggplot(data=WP.df)+geom_line(aes(x=year,y=Asia))
```

### Data is Data
### mapping: aes(x=...,y=...)

---
## <font color='red'>geom</font>etric

### geom_line and geom_point

```r
ggplot(WP.df,aes(x=year,y=Asia))+
  geom_line(size=2)+geom_point(size=5)
```

<img src="assets/fig/unnamed-chunk-5-1.png" title="plot of chunk unnamed-chunk-5" alt="plot of chunk unnamed-chunk-5" style="display: block; margin: auto;" />

--- 
## <font color='red'>scale</font>


```r
ggplot(x3) +
  geom_point(aes(x =平均溼度, y=平均降雨量,colour=場站名稱,size=平均降雨量))+
  scale_size(range=c(5,10)) +thm
```

<img src="assets/fig/scale1-1.png" title="plot of chunk scale1" alt="plot of chunk scale1" style="display: block; margin: auto;" />

---
## <font color='red'>stat</font>istics


```r
 ggplot(pressure,aes(x=temperature,y=pressure))+
  geom_point()+
  stat_smooth()
```

<img src="assets/fig/unnamed-chunk-6-1.png" title="plot of chunk unnamed-chunk-6" alt="plot of chunk unnamed-chunk-6" style="display: block; margin: auto;" />

--- &twocol .largecontent

## <font color='red'>coord</font>inante 

*** =left


```r
ggplot(pix,aes(x="",y=數量,fill=入口網站))+
  geom_bar(stat='identity')+thm
```

![plot of chunk pix5](assets/fig/pix5-1.png) 

*** =right


```r
ggplot(pix,aes(x="",y=數量,fill=入口網站))+
  geom_bar(stat='identity',width=1)+
  coord_polar('y')+thm
```

![plot of chunk pix6](assets/fig/pix6-1.png) 


--- 
## <font color='red'>facet</font>

```r
rain <- filter(ubike, grepl("2015-02", 日期, fixed = TRUE), 場站區域 == "中和區") %>%
  group_by(日期,場站名稱) %>% 
  summarise(每日平均降雨量 = mean(降雨量))
```

--- .largecontent

## <font color='red'>facet</font>
### Line Chart

```r
ggplot(rain) + thm+las2+
  geom_line(aes(x = 日期, y = 每日平均降雨量,group=場站名稱,colour=場站名稱),size=2)
```

<img src="assets/fig/ubike.site.wet.rainfall13-1.png" title="plot of chunk ubike.site.wet.rainfall13" alt="plot of chunk ubike.site.wet.rainfall13" style="display: block; margin: auto;" />

--- .largecontent

## Line Chart in Facets


```r
ggplot(rain) +thm+las2+facet_wrap(~場站名稱,nrow=2)+ # facet_wrap將各站的情況分開畫
  geom_line(aes(x = 日期, y = 每日平均降雨量,group=場站名稱,colour=場站名稱),size=2)
```

<img src="assets/fig/ubike.site.wet.rainfall14-1.png" title="plot of chunk ubike.site.wet.rainfall14" alt="plot of chunk ubike.site.wet.rainfall14" style="display: block; margin: auto;" />

--- .dark .segue
## 可以存檔嗎？

--- &vcenter .largecontent
## 存檔
    # 畫完圖之後，再存檔~~
    ggsave('檔案名稱')

--- .dark .segue
## 學習資源

--- &vcenter .largecontent

- [ggplot2 cheat sheet from RStudio Inc.](http://www.rstudio.com/wp-content/uploads/2015/03/ggplot2-cheatsheet.pdf)
- [ggplot2 官方文件](http://docs.ggplot2.org/current/index.html)

--- &vcenter .largecontent

## 本週目標

### 環境設定

- 建立可以使用R 的環境
- 了解R 的使用界面

### 學習R 語言

- 透過實際的範例學習R 語言
    - 讀取資料
    - 選取資料
    - 敘述統計量與視覺化

--- &vcenter .largecontent

## 掌握心法後，如何自行利用R 解決問題

- 了解自己的需求
- 詢問關鍵字與函數
    - 歡迎來信 <benjamin0901@gmail.com> 或其他教師
    - 多多交流
        - [Taiwan R User Group](http://www.meetup.com/Taiwan-R)，mailing list: <Taiwan-useR-Group-list@meetup.com>
        - ptt R_Language版
        - [R軟體使用者論壇](https://groups.google.com/forum/#!forum/taiwanruser)
    - `sos`套件，請見Demo


--- .dark .segue

## Team Project
