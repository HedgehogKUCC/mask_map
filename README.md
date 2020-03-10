# 口罩地圖

## 目錄

  - [作品展示](#display)
  - [Leaflet](#leaflet)
  - [OpenStreetMap](#open-street-map)
  - [起手式](#get-started)
  - [插入 Icon](#insert-icon)
  - [Icon 顏色](#icon-color)
  - [效能處理](#handle-efficient)
  - [接上真正資料](#ajax-data)
  - [判斷加入哪種顏色 Icon](#which-icon-color)
  - [定位 API](#geolocation)
  - [共筆文件](#document)
  - [口罩剩餘數量 JSON API](#mask-api)
  - [台灣 縣市，鄉鎮，地址 中英文 JSON](#taiwan)
  - [Project setup](#setup)

<br>

<h2 id="display">作品展示</h2>

[https://hedgehogkucc.github.io/mask_map/#/](https://hedgehogkucc.github.io/mask_map/#/)

<br>

<h2 id="leaflet">Leaflet</h2>

  - JS 框架
  - 載入圖資
  - 標示點 ( Marker )

[官網](https://leafletjs.com/reference-1.6.0.html#map-example)

<br>

<h2 id="open-street-map">OpenStreetMap</h2>

  - 提供圖資 ( 地圖資料 )
  - EX： `Google Map` `Safari Map` `OpenStreetMap`

地圖都是由一張張圖片組合起來簡稱 **圖磚**

<br>

<h2 id="get-started">起手式</h2>

```html
<link rel="stylesheet" href="https://unpkg.com/leaflet@1.6.0/dist/leaflet.css"
   integrity="sha512-xwE/Az9zrjBIphAcBb3F6JVqxf46+CDLwfLMHloNu6KEQCAWi6HcDUbeOfBIptF7tcCzusKFjFw2yuvEpDL9wQ=="
   crossorigin=""/>

<div id="mapid"></div>

 <!-- Make sure you put this AFTER Leaflet's CSS -->
 <script src="https://unpkg.com/leaflet@1.6.0/dist/leaflet.js"
   integrity="sha512-gZwIG9x3wUXg2hdXF6+rVkLF/0Vi9U8D2Ntg4Ga5I5BZpVkVxlJWbSQtXPSiUTtC0TjtGOmxa1AJPuV0CPthew=="
   crossorigin=""></script>
```

```css
body, html {
  width: 100%;
  height: 100%;
}

#mapid {
  width: 100%;
  height: 100%;
}
```

```js
// 設定一個地圖，把這地圖定位在 #mapid，先定位 center 座標，zoom 為 16。
// zoom 數字愈小愈遠，數字愈大愈近。
var mymap = L.map('mapid', {
  center: [25.0722973,121.4316257],
  zoom: 16
})

// 要用誰的圖資
L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
    attribution: '&copy; <a href="https://www.openstreetmap.org/copyright">OpenStreetMap</a> contributors'
}).addTo(mymap)
```

<br>

<h2 id="insert-icon">插入 Icon</h2>

```js
// 加上一個 marker，並設定它的座標，同時將這座標放入對應的地圖裡。
L.marker([25.0730568,121.4314497]).addTo(mymap)

// 針對這個 marker，加上 HTML 內容進去。
  .bindPopup('<h1>測試藥局-1</h1><p>成人口罩： 100</p><p>兒童口罩： 100</p>')

// 預設開啟
  // .openPopup()
```

<br>

<h2 id="icon-color">Icon 顏色</h2>

[https://github.com/pointhi/leaflet-color-markers](https://github.com/pointhi/leaflet-color-markers)

```js
var greenIcon = new L.Icon({
  iconUrl: 'https://cdn.rawgit.com/pointhi/leaflet-color-markers/master/img/marker-icon-2x-green.png',
  shadowUrl: 'https://cdnjs.cloudflare.com/ajax/libs/leaflet/0.7.7/images/marker-shadow.png',
  iconSize: [25, 41],
  iconAnchor: [12, 41],
  popupAnchor: [1, -34],
  shadowSize: [41, 41]
});

L.marker([25.0722973,121.4316257], {icon: greenIcon}).addTo(mymap)
  .bindPopup('<h1>測試藥局-2</h1><p>成人口罩： 50</p><p>兒童口罩： 50</p>')
```

<br>

<h2 id="handle-efficient">效能處理</h2>

[https://github.com/Leaflet/Leaflet.markercluster#examples](https://github.com/Leaflet/Leaflet.markercluster#examples)

```html
<link href="https://cdnjs.cloudflare.com/ajax/libs/leaflet.markercluster/1.4.1/MarkerCluster.css"></link> 

<link href="https://cdnjs.cloudflare.com/ajax/libs/leaflet.markercluster/1.4.1/MarkerCluster.Default.css"></link> 

<script src="https://cdnjs.cloudflare.com/ajax/libs/leaflet.markercluster/1.4.1/leaflet.markercluster.js"></script>
```

```css
.marker-cluster-small {
  background-color: rgba(181, 226, 140, 0.6);
}
.marker-cluster-small div {
  background-color: rgba(110, 204, 57, 0.6);
}

.marker-cluster-medium {
  background-color: rgba(241, 211, 87, 0.6);
}
.marker-cluster-medium div {
  background-color: rgba(240, 194, 12, 0.6);
}

.marker-cluster-large {
  background-color: rgba(253, 156, 115, 0.6);
}
.marker-cluster-large div {
  background-color: rgba(241, 128, 23, 0.6);
}

.marker-cluster {
  background-clip: padding-box;
  border-radius: 20px;
}
.marker-cluster div {
  width: 30px;
  height: 30px;
  margin-left: 5px;
  margin-top: 5px;

  text-align: center;
  border-radius: 15px;
  font: 12px "Helvetica Neue", Arial, Helvetica, sans-serif;
}
.marker-cluster span {
  line-height: 30px;
}
```

```js
var data = [
  { name: '測試藥局-1', lat: 25.0730568, lng: 121.4314497, mask_adult: 100, mask_child: 100 },
  { name: '測試藥局-2', lat: 25.0722973, lng: 121.4316257, mask_adult: 50, mask_child: 50 }
]

// 新增一個圖層，這圖層專門放 Icon 群組。
var markers = new L.MarkerClusterGroup().addTo(mymap)

for( let i = 0; i < data.length; i++) {
  // 在該圖層上加入各個 marker
  markers.addLayer(L.marker([data[i].lat, data[i].lng], { icon: greenIcon })
                   .bindPopup(`<h1>${data[i].name}</h1><p>成人口罩： ${data[i].mask_adult}</p><p>兒童口罩： ${data[i].mask_child}</p>`))
}

mymap.addLayer(markers)
```

<br>

<h2 id="ajax-data">接上真正資料</h2>

```js
// 新增一個圖層，這圖層專門放 Icon 群組。
var markers = new L.MarkerClusterGroup().addTo(mymap)

// 開啟一個網路請求
var xhr = new XMLHttpRequest()

// 準備要跟某某伺服器，拿藥局剩餘口罩資料。
xhr.open('get', 'https://raw.githubusercontent.com/kiang/pharmacies/master/json/points.json')

// 執行要資料的動作
xhr.send()

// 當資料回傳時，下面語法就會自動觸發。
xhr.onload = function() {
  // 我要把它轉成物件陣列的 JSON 格式
  var data = JSON.parse(xhr.responseText).features
  
  for( let i = 0; i < data.length; i++) {
    markers.addLayer(L.marker([data[i].geometry.coordinates[1], data[i].geometry.coordinates[0]], { icon: greenIcon })              
                     .bindPopup(`<h1>${data[i].properties.name}</h1><p>成人口罩： ${data[i].properties.mask_adult}</p><p>兒童口罩： ${data[i].properties.mask_child}</p>`))
  }
  
  mymap.addLayer(markers)
}
```

<br>

<h2 id="which-icon-color">判斷加入哪種顏色 Icon</h2>

```js
var greyIcon = new L.Icon({
  iconUrl: 'https://cdn.rawgit.com/pointhi/leaflet-color-markers/master/img/marker-icon-2x-grey.png',
  shadowUrl: 'https://cdnjs.cloudflare.com/ajax/libs/leaflet/0.7.7/images/marker-shadow.png',
  iconSize: [25, 41],
  iconAnchor: [12, 41],
  popupAnchor: [1, -34],
  shadowSize: [41, 41]
})

for( let i = 0; i < data.length; i++) {
    
    let isMask_adult;
    
    // 如果成人口罩為 0，就顯示灰色的 Icon。
    if(data[i].properties.mask_adult === 0) {
      isMask_adult = greyIcon
    } else {
      isMask_adult = greenIcon
    }
    
    markers.addLayer(L.marker([data[i].geometry.coordinates[1], data[i].geometry.coordinates[0]], { icon: isMask_adult })              
                     .bindPopup(`
                                  <h1>${data[i].properties.name}</h1>
                                  <p>${data[i].properties.phone}</p>
                                  <p>${data[i].properties.address}</p>
                                  <hr>
                                  <p>成人口罩： ${data[i].properties.mask_adult}</p>
                                  <p>兒童口罩： ${data[i].properties.mask_child}</p>
                                  <p>更新時間： ${data[i].properties.updated}</p>
                                `
                               ))
  }
```

<br>

<h2 id="geolocation">定位 API</h2>

[Google Map Geolocation API](https://ithelp.ithome.com.tw/articles/10192680)

<br>

<hr>

<br>

<span id="document">[共筆文件](https://quip.com/vdqYAiFHHkaV)</span>

<span id="mask-api">[口罩剩餘數量 JSON API](https://raw.githubusercontent.com/kiang/pharmacies/master/json/points.json)</span>

<span id="taiwan">[台灣 縣市，鄉鎮，地址 中英文 JSON](https://github.com/donma/TaiwanAddressCityAreaRoadChineseEnglishJSON)</span>

<br>

<h2 id="setup">Project setup</h2>

```
npm install
```

### Compiles and hot-reloads for development
```
npm run serve
```

### Compiles and minifies for production
```
npm run build
```

### Lints and fixes files
```
npm run lint
```
