---
title: Next.js
tags: next.js
description: View the slide with "Slide Mode".
---

# Next.js
Next 是一個基於 React 的 SSR 解決方案


---

## Next.js 有兩種 pre-rendering 方式

- Server-side Rendering
- Static Generation (Recommended)


---


## Server-side Rendering （服務器端渲染）

針對<font color="#f00">每個請求</font>動態產生HTML頁面， 因為服務器端渲染導致性能比靜態生成慢，所以只有在絕對必要時才使用它。



---

![附圖參考](https://nextjs.org/static/images/learn/dynamic-routes/page-path-external-data.png)

:::info
每次客戶端請求頁面時，服務器將獲取該頁面的數據(call api)，
並使用該數據生成頁面的HTML(render 畫面)。
只有完成所有這些操作後，HTML才會發送回客戶端。
:::



---

==優點==

1. 每當有 client 發起對某個頁面的 request 時，server 會抓取對應的資料，建立完整的 HTML，最後再回傳給 client，不需要先下載一堆 JS 和 CSS 後才能看到頁面（FCP加載速度快)，即使因為 JS 還沒被執行，所以畫面還不具備互動性，但讓使用者先看到畫面，再利用人的感知延遲時間去執行 JS code，可以大大減少使用者的跳出率
2. 伺服器端渲染不需關心瀏覽器兼容的問題
3. 可解決SEO問題

==缺點==
1. 每次的重新 Render 會造成後端的負擔提升也讓使用者的體驗變差
2. Server 端渲染的頁面看上去似乎可以交互，但實際上還是要等待js執行完成才能交互。簡單地說，會增加TTI(Time to Interactive)



---

## Static Generation (Recommended)

在<font color="#f00">編譯</font>的時候，並將在每個請求上重用HTML。

---

:::info
將生成HTML頁面的工作放到編譯時，而不必在請求帶來時動態完成。
為每個URL預先單獨生成HTML文件，達到極佳的FCP效能。
:::

![附圖參考](https://nextjs.org/static/images/learn/data-fetching/static-generation.png)

---

==實際操作==

1. 使用 next export： 將所有頁面導出到可以與任何主機一起使用的靜態HTML文件
2. 用nginx做反向代理: 針對out folder 啟動next server

---



### 要怎麼選擇使用 Server-side Rendering 或是 Static Generation


---

部落格,官網,電子商務產品列表...等靜態頁面 => 使用Static Generation 

兩者都能解決SEO問題



![附圖參考](https://pic4.zhimg.com/v2-189aa202a35878211c3bc392d14d87af_r.jpg)

此圖參考網址：https://zhuanlan.zhihu.com/p/113853235

---

##  bonus : csr vs. ssr vs. Static Rendering :smile: 

---

## csr (Client Side Render)

在前端進行 Render，後端作為資料供應的 API 層。
當需要新的資料時，前端發送 Request 後只需要部份更新畫面就好。

---

渲染流程：
1. 客戶端發起頁面請求，服務端把頁面（響應的字符串）發送給Client端
2. Client端從上到下依次解析，解析過程中，發現網絡請求，再次向服務器發送網絡請求，Client端拿到響應的結果，render出 HTML頁面。

![附圖參考](https://developers.google.com/web/updates/images/2019/02/rendering-on-the-web/client-rendering-tti.png)

優點：
靈活，真正的前後端分離，方便於前後台各自更新維護。

缺點: 
有些 CSR 的網站會造成爬蟲取得的頁面不是那麼完整，造成 SEO 的問題。
第一次載入畫面是等到前端載入後，會有一段時間的空白。


---

##  ssr (Server Side Render)

Server 端完全可以在獲取到HTML文件的內容之後經過一些處理再返回給Client端，也就說，Server端可以將數據插入到HTML字符串中之後再返回給Client端

---

渲染流程：
在後端接收到 Request 即產生 HTＭＬ畫面給前端

![附圖參考](https://developers.google.com/web/updates/images/2019/02/rendering-on-the-web/server-rendering-tti.png)

優點：
SEO。
不需要先下載一堆 JS 和 CSS 後才能看到頁面（FCP加載速度快）。

缺點：
因渲染在Server端，這樣的作法不論點擊什麼網頁上什麼功能，每一次都是將整個畫面重新繪製，如果在頻寬網路較差的情況下，會是一個較不好的體驗，因為要一直重新 loading 整個頁面

---

## Static Rendering

需要為每個URL 單獨生成一份HTML 文件

---

渲染流程：
在後端接收到 Request 即產生 HTＭＬ畫面給前端，但HTML會被重用於每個客戶端請求

![附圖參考](https://developers.google.com/web/updates/images/2019/02/rendering-on-the-web/static-rendering-tti.png)



---


# :100: :muscle: :tada:

---

### 參考文件

- https://nextjs.org/docs/basic-features/pages#static-generation-recommended
- http://www.ayqy.net/blog/csr-vs-ssr-vs-prerendering-vs-hydration/
- https://yxyuxuan.github.io/2019/09/18/SSR%E5%92%8CCSR/
- https://pjchender.dev/react/nextjs-getting-started/

---

### Thank you! :sheep: 

You can find me on

- GitHub : 
  https://github.com/kai4idps
- Linkedin me : 
  https://www.linkedin.com/in/kai-cc-a445221b5/
- Blog: 
  https://chekai7.github.io/c.c.k