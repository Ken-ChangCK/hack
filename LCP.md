---
title: 如何優化LCP
tags: google speed
description: View the slide with "Slide Mode".
---

# 如何優化LCP!

---

## 網站核心指標三劍客
> Google 定義出衡量網站使用者體驗最重要的三個指標 – <font color="#f00">LCP, FID, CLS</font>

---

![網站使用體驗三大核心指標](https://www.darrenhuang.com/wp-content/uploads/2020/06/%E7%B6%B2%E7%AB%99%E4%BD%BF%E7%94%A8%E9%AB%94%E9%A9%97%E6%A0%B8%E5%BF%83%E6%8C%87%E6%A8%99.png)
[圖片參考網址](https://www.darrenhuang.com/core-web-vitals-lcp-fid-cls.html)

---



## 什麼是ＬＣＰ

- Largest Contentful Paint
- 最大內容繪製 
- 網頁中最大元素的載入速度 :heart: 

---

![LCP範例](https://web-dev.imgix.net/image/admin/e0O2woQjZJ92aYlPOJzT.png?auto=format&w=1600)
[圖片參考網址](https://web.dev/lcp/)

---

### LCP 會偵測那些項目？

- 圖片
- svg 向量圖片
- 影片 (預覽大圖)
- 透過 url() 的 CSS 功能載入背景圖片的元素 
  包含文字的區塊級元素 (block-level elements) 或行內元素 (inline elements)

---



### 如何改進 LCP

---

##### 減少伺服器回應時間 

:::info
針對主機進行優化 (效能太差或是頻寬不足)
讓第三方的資源提早載入
:::

---

**讓第三方的資源提早載入**
- 預先載入重要資源

有時候，在css或者js中聲明或者使用的一個重要資源
可能比你預期的要晚獲取，比如字體文件。
如果你明確的知道某個資源需要提高優先級，可以使用<link rel="preload">。

```javascript=
<link rel="preload" as="script" href="script.js">
<link rel="preload" as="style" href="style.css">
<link rel="preload" as="image" href="img.png">
<link rel="preload" as="video" href="vid.webm" type="video/webm">
<link rel="preload" href="font.woff2" as="font" type="font/woff2" crossorigin>
```



---

#### 加快資源載入的時間

> 大部分網站的最大元素都是圖片，如果能加速這些圖片的加載，能有效改善LCP

:::info
最小化css
使用響應式圖片
將文字檔案進行壓縮
:::

---

**最小化css**
使用開發者工具中的Coverage能夠找到頁面未使用的css。

步驟：
chrome開啟開發者模式
=> 找到 Command menu https://developer.chrome.com/docs/devtools/command-menu/ 
=> 會在下方看到coverage
=> 在source裡面選取某個檔案，點擊下方Coverage的重新加載頁面的按鈕
=> 就可以發現code coverage
[步驟參考網址](https://developer.chrome.com/docs/devtools/coverage)


![image alt](https://reactgo.com/static/1cc096baa9609e8ce81a189424b7b245/8bd7c/css-jss-code-coverage.png)

上圖中紅色表示未使用的代碼，綠色表示使用過的代碼。

這樣我們就可以在將文件傳送到用戶瀏覽器之前從文件中刪除未使用的代碼。這有助於提高 Web 性能。

---

**使用響應式圖片**

- 用 picture 的scrset取代RWD不同大小螢幕顯示的圖片
- 使用 lazy load 延遲加載，藉由使用者剛好滑到圖片位置時，才顯示圖片的方式，達到節省資源、加速網頁速度的效果。
(推薦套件:[react-lazy-load-image-component](https://www.npmjs.com/package/react-lazy-load-image-component))

---

**將文字檔案進行壓縮**

可以用gzip 壓縮檔案

詳細做法： 
https://stackoverflow.com/questions/56186336/how-to-compress-the-files-using-gzip-in-react-app

---



#### 避免使用客戶端渲染(CSR)

:::info
若必須使用 CSR ，建議優化 JavaScript ，避免渲染時使用太多資源
盡量在伺服器端完成頁面渲染，讓用戶端取得已渲染好的內容
:::

---

如果你的bundle的js文件過大，會影響LCP。如果不做優化，用戶可能長時間無法看到頁面，也無法交互，直到js全部下載執行完

1. 使用SSr
2. 透過Webpack打包重複在各個componets的modules
3. Code-Splitting


---

**Webpack打包重複的modules**


在首次進入網頁的時候一樣會下載重複的modules，
但是在前往其他頁面的時候，就不會重複的去載相同的modules.

舉例來說 react 和 react-dom 套件合計約為 130 KB，如果把它們獨立打包在每次 app 更新時就可以省下 130 KB 的傳輸。對於不常更新的第三方套件來說，這種處理方式實作上簡單也能達到不錯的效果。

```javascript=
// .webpack.config
optimization: {
  splitChunks: {
    cacheGroups: {
      // Split vendor code to its own chunk(s)
      vendors: {
        name: 'vendor',
        test: /[\\/]node_modules[\\/](react|react-dom)[\\/]/,
        chunks: 'all',
      },
    },
  },
},
...
```
[參考資料](https://papan01.com/archives/2019-12-20-code-splitting)

基礎webpack設定：https://gist.github.com/gaearon/ca6e803f5c604d37468b0091d9959269

P.S. Next.js 把基本會用倒的 Webpack 都包好了，如果要增加功能的話可以到 next.config.js 設定


---


**Code-Splitting**

透過Dynamic Imports來延遲下載，可以先顯示某些頁面，然後元件可以再慢慢延遲加載

```javascript=
import dynamic from 'next/dynamic'
const DynamicComponent = dynamic(import('../components/hello'))

export default const Home = () => 
return (
    <div>
    <Header />
    <DynamicComponent />  // 延遲下載
    <p>HOME PAGE is here!</p>
    </div>
)
```

---

### 參考網址

- https://web.dev/lcp/
- https://awoo.ai/zh-hant/blog/core-web-vitals-guide/
- https://segmentfault.com/a/1190000023558047
- https://itnext.io/hunting-for-unused-css-and-javascript-57e8d9bc2a67

---

### Thank you! :sheep: 

You can find me on

- GitHub : 
  https://github.com/kai4idps
- Linkedin me : 
  https://www.linkedin.com/in/kai-cc-a445221b5/
- Blog: 
  https://chekai7.github.io/c.c.k
