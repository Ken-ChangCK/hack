---
title: webpack
tags: devops
description: View the slide with "Slide Mode".
---

# Webpack !

<!-- Put the link to this slide here so people can follow -->
slide: https://hackmd.io/p/template-Talk-slide

---

**Webpack** 是一個開源的前端打包工具。 

**Webpack**  提供了前端開發缺乏的模組化開發方式，將各種靜態資源視為模組，
並從它生成優化過的程式碼，通常都會寫一個 **webpack.config.js** 來管理各個檔案。

---

### 優勢

- 將你的 js 檔案 Bundle 變成單一的檔案:fish: 
- 壓縮 javaScript、css、html 代碼，壓縮合並圖片 :heart: 
- Minify 或優化程式碼 

---

### 實際效果

> 將你的 js 檔案 Bundle 變成單一的檔案

讓你可以撰寫模組化的 JavaScript，但是你不需要 include 每個 JavaScript <script/> 的檔案（如果你需要多個 JavaScript 檔案可以透過設定來完成）。

---

> 壓縮 javaScript、css、html 代碼，壓縮合並圖片
> Minify 或優化程式碼

減少bundle的檔案大小，能更快的將頁面載入，並且將不需要執行部分的代碼讓其異步加載 


---

### 基本觀念

Entry： Webpack的預設入口點
Output：Webpack的預設輸出資料夾
Loader：進行文件處理
Plugins：插件，比 loader 更強大，能使用更多 webpack 的 api

```javascript=
const path = require('path')    // Node.js 提供快速獲得當前專案路徑的函式庫

module.exports = {
  entry: './src/index.js',
  output: {
    path: path.resolve(__dirname, 'dist'),,//打包後的文件存放的地方
    filename: 'bundle.js',  //打包後输出文件的文件名
  },
  module: {
    rules: [
      // loaders
    ],
  },
  plugins: [
    // plugins
  ],
}

```


---

## Entry

Entry是收集前端專案的所有依賴項的起點

```javascript=
entry: path.resolve(__dirname, '../src/app.js'),
```

![](https://i.imgur.com/V7xrd3f.png)


---

## Output

Output是在bundle過程中收集生成的JavaScript和靜態檔案的位置。
Webpack的預設輸出資料夾（從版本4開始）是 `dist/` ，也可以額外配置。
生成的JavaScript檔案是所謂的 bundle 的一部分。
```javascript=
output: {
    path: path.resolve(__dirname, "dist"),
    filename: "main.[hash].bundle.js"
  }
```
![](https://i.imgur.com/O26jUWO.png)

---

## Loader

module 就是放loader 的地方，module下有一個rules字段，rules下有就是處理模塊的規則，配置哪類的模塊，交由哪類loader來處理。

跟讀檔案有關係都會用到loader

前面提到 Webpack 可以打包的東西不只是 JS，也可以打包其他諸如 CSS、圖片、文件等資源，而這些就需要藉由 Loader 來處理，只要將這些種類的檔案透過 loader 轉為 module，就能讓專案方便的直接引入。





![](https://i.imgur.com/3tOjo53.png)

---

#### 常用的Loader

1. [css-loader](https://webpack.js.org/loaders/css-loader/#root): 加載.css 文件，
2. style-loader:使用 style 標籤將 css-loader 內部樣式注入到我們的 html 頁面
3. less-loader, sass-loader: 解析css預處理器
4. file-loader 或 url-loader 或 [Resource assets](https://webpack.js.org/guides/asset-modules/#resource-assets): 處理圖片資源

```javascript=
 module: {
    rules: [
      {
        test: /\.css$/i,
        use: [MiniCssExtractPlugin.loader, "css-loader"]
      },
      {
        test: /\.m?js$/,
        exclude: /node_modules/,
        use: {
          loader: "babel-loader"
        }
      },
      {
        test: /\.jpeg/,
        type: "asset/resource"
      }
    ]
  }
```


---

## Plugins
是第三方擴充套件，可以更改webpack的工作方式。例如，有一些用於提取HTML，CSS或設定環境變數的外掛。

[html webpack plugn](https://webpack.js.org/plugins/html-webpack-plugin/):
在webpack新增 template
為了要以某個寫好的index.html當模板去在dist產生html檔

 [css extract webpack](https://webpack.js.org/plugins/mini-css-extract-plugin/) :
獨立的.css檔放在dist資料夾內，而不是變成 <style / >引入到header裡面


---

```javascript=
 plugins: [
    new HtmlWebpackPlugin({
      template: "./index.html"
    }),
    new MiniCssExtractPlugin({
      filename: "main.[hash].css"
    })
  ],
```

---

## Mode
webpack有兩種操作模式：development開發和production生產。
它們之間的主要區別是生產模式會自動將最小化
和其他優化應用於JavaScript程式碼。

mode
= develpoment 以開發者的角度去bundle
= production 會壓縮檔案，把空格壓縮

---

```javascript=
devtool: "source-map",
```
點擊紅色框框後
![](https://i.imgur.com/XLRxgAQ.png)

可跑出看得懂的code，比較容易debug
![](https://i.imgur.com/i260lHG.png)

---

[完整範例](https://github.com/chekai7/webpack-tutorial)：
可以clone下來玩玩 :+1: 
```
|- dist
|- src
   |- index.js
   |- index.css
|- index.html
|- package.json
|- webpack.config.js

clone 下來之後
先 npm install 
-> 打包檔案 npm run build

```

---

### 進階應用

chunk

- 一堆module的集合
- chunk 是指可以將共同用到的套件拉出來獨立成打包成一個 JS 檔，通常都會命名成 vendor.js 
- 最大的優勢就是在重新打包檔案的過程中不會重複在打包一樣的東西而造成打包時間過長，而且最重要的是可以讓 bundle.js 整體進行一個大瘦身

---

把整個應用所有來自node_modules的代碼。抽取為vendors代碼塊。

```javascript=
splitChunks: {
    cacheGroups: {
        commons: {
            test: /[\/]node_modules[\/]/,
            name: "vendors",
            chunks: "all"
        }
    }
}
```

關於chunk的know how :heart_decoration: 
1. https://ithelp.ithome.com.tw/articles/10209683
2. https://juejin.cn/post/6844903889393680392

---

## webpack-dev-server

dev server 可以在瀏覽器看到你的網站外觀也可以更快速的開發，預設的port是 http://localhost:8000

contentBase: 服務的檔案來自哪裡。
hot: 啟用 熱更新(Hor Reload)。

```javascript=
  devServer: {
    contentBase: './dist',
    hot: true
  }
```

---

執行dev server

可以透過 npm run dev，並導到 http://localhost:8080 看到自己的網站

```javascript=
// package.json
{
  //...
  "scripts": {
    "build": "webpack --config webpack.config.prod.js",
    "dev"  : "webpack-dev-server --config webpack.config.dev.js"
  }
  //...
}
```

---

## Webpack-dev-server的proxy用法
(解決開發環境的跨域問題)

- 多個路徑代理到同一個target下, 
你可以使用由一個或多個「具有context 屬性的對象」構成的array

```javascript=
module.exports = {
    //...
    devServer: {
        proxy: [{
            context: ['/auth', '/api'],
            target: 'http://localhost:3000',
            secure: true, // 接受運行在HTTPS上
             changeOrigin: true, // 虛擬一個服務器接收你的請求並代你發送該請求
        }]
    }
};
```

[參考網址](https://segmentfault.com/a/1190000016314976)



---




# :100: :muscle: :tada:

---

### 參考網址

- https://webpack.docschina.org/concepts/ :tada: 官方文檔
- https://ithelp.ithome.com.tw/articles/10209683
- https://neighborhood999.github.io/webpack-tutorial-gitbook/Part1/
- https://zhuanlan.zhihu.com/p/29161762
- https://juejin.cn/post/6844903889393680392
- https://webpack.js.org/

---

### Thank you! :sheep: 

You can find me on

- GitHub : 
  https://github.com/kai4idps
- Linkedin me : 
  https://www.linkedin.com/in/kai-cc-a445221b5/
- Blog: 
  https://chekai7.github.io/c.c.k