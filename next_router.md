---
title: Use router in Next.js
tags: next.js
description: View the slide with "Slide Mode".
---

# 如何使用 router in Next.js

---

已知有三種方法可以實現導向到其他頁面

---

```javascript=
 1. <Link href="/about"><a>About me</a></Link>
 2. <a href="/about">About me</a>
 3. <button onClick={() => router.push("/about")}>About me</button>
```

---

分開介紹

---

> <Link/>

- <Link/>將創建一個<a/> tag ，這代表爬蟲抓取您的網站時將檢測到您的link。
- 最終user仍可以繼續瀏覽而無需重新加載頁面，從而創建了(單頁應用程序(SPA)的行為。

:::success
SPA 是能讓用戶在使用網站時，不用從伺服器載入整個頁面，就能快速的切換頁面，所以會提升性能與動態體驗。相對地，在 SEO、狀態與路由管理、還有性能監控方面，都會是需要考慮的劣勢。
:::

---

> <a/>

- <a/> tag  不使用next / link的<Link/>，反而會創建一個超連結，該超連結 將user 作為新頁面定向到url。

:::success
通過使用<a/>標記，我們將失去Next.JS的SPA功能。
<a/>標記只是純HTML元素，具有所有默認行為。使用它時，會發生完全重新加載。如果您正在使用它，請嘗試切換到<Link/>。
類似於<Link/>對SEO有利
::::

---
> router.push

- 此行為像是 window.location，但是這沒有創建<a/> tag，這意味著-如果您很關注SEO，則爬蟲將不會檢測到此link。
- 這主要用於事件處理程序（onClick此處）。像是單擊按鈕，然後執行一些任務，然後根據結果將用戶帶到另一個頁面。
- 若您想使用router.push()。不要僅使用它來轉到另一個頁面。請注意，如果您希望對SEO進行爬網，那麼這是不利的。

---

### 聯絡方式 :sheep: 

You can find me on

- GitHub : 
  https://github.com/kai4idps
- Linkedin me : 
  https://www.linkedin.com/in/kai-cc-a445221b5/
- Blog: 
  https://chekai7.github.io/c.c.k
