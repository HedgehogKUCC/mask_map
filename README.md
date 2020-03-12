# 口罩地圖

    因為最近疫情關係大家對於藥局還有沒有口罩有這個需求出現，所以為了能方便社會大眾和自己也想研究圖資的技術才有了這個作品。

<hr>

<br>

## 目錄

  - [作品介紹](#introduce)
  - [展示作品](#display)
  - [專案設定](#setup)
  - [簡介](#summary)
  - [使用技術](#skills)
  - [資料來源](#source)
  - [聲明](#declaration)

<br>

<hr>

<br>

<h2 id="introduce">作品介紹</h2>

使用 Vue CLI 4 + Leaflet + OpenStreetMap 所製作。

串接健保署所提供 [口罩數量資料](https://raw.githubusercontent.com/kiang/pharmacies/master/json/points.json)

<br>

<h2 id="display">展示作品</h2>

[https://hedgehogkucc.github.io/mask_map/#/](https://hedgehogkucc.github.io/mask_map/#/)

<br>

<h2 id="setup">專案設定</h2>

```
npm install
```

<br>

### 開發時編譯和重載頁面
```
npm run serve
```

<br>

<h2 id="summary">簡介</h2>

此作品主要功能有以下：

  - 側邊欄收縮
  - 依據縣市、地區顯示藥局位置圖標
  - 依據第一筆經緯度跳置該藥局並打開圖標資訊
  - 依據藥局有無成人口罩顯示不同顏色
  - 收藏該縣市、地區的藥局 ( 儲存至 localStorage )
  - 右下角有如何買口罩相關資訊

<br>

<h2 id="skills">使用技術</h2>

  - Vue CLI 4
  - ESLint ( Airbnb )
  - JavaScript ( ES6 ...等 )
  - Axios
  - Bootstrap 4
  - Leaflet
  - OpenStreetMap
  - Moment

<br>

<h2 id="source">資料來源</h2>

  - [台灣 縣市，鄉鎮，地址 中英文 JSON](https://github.com/donma/TaiwanAddressCityAreaRoadChineseEnglishJSON)
  - 設計稿來自 [F2E 投稿設計師 - Wendy](https://challenge.thef2e.com/user/2259)
  - [口罩實名制 3/5 圖片](https://www.google.com/imgres?imgurl=https%3A%2F%2Fimgs.gvm.com.tw%2Fupload%2Fgallery%2F20200302%2F71389_02.jpg&imgrefurl=https%3A%2F%2Fhealth.gvm.com.tw%2Farticle.html%3Fid%3D71389&tbnid=Ka8PJXwPxOqfbM&vet=12ahUKEwiEs7eyspHoAhWVAKYKHSKYBGsQMygMegUIARCoAg..i&docid=q0GEZyjhCJAOVM&w=792&h=792&q=%E5%8F%A3%E7%BD%A9%E5%AF%A6%E5%90%8D%E5%88%B6&ved=2ahUKEwiEs7eyspHoAhWVAKYKHSKYBGsQMygMegUIARCoAg)
  - [口罩實名制 3/10 圖片](https://www.google.com/imgres?imgurl=https%3A%2F%2Fimg.ltn.com.tw%2FUpload%2Fnews%2F600%2F2020%2F03%2F10%2FphpaJUPwL.png&imgrefurl=https%3A%2F%2Fnews.ltn.com.tw%2Fnews%2Flife%2Fbreakingnews%2F3094800&tbnid=ryQAcZYJ-ybDeM&vet=12ahUKEwiEs7eyspHoAhWVAKYKHSKYBGsQMygAegUIARCEAg..i&docid=J7WTD_dlfiQ8-M&w=800&h=1082&q=%E5%8F%A3%E7%BD%A9%E5%AF%A6%E5%90%8D%E5%88%B6&ved=2ahUKEwiEs7eyspHoAhWVAKYKHSKYBGsQMygAegUIARCEAg) - 網路購買試營運

<br>

<h2 id="declaration">聲明</h2>

[![GitHub license](https://img.shields.io/github/license/Naereen/StrapDown.js.svg)](https://github.com/HedgehogKUCC/mask_map/blob/master/LICENSE)&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;本作品內圖片、內容等，純粹為個人練習前端使用，不做任何商業用途。



