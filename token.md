---
title: 'Access token v.s. Refresh Token'
tags: 'Knowledge'
disqus: hackmd
---

Access token v.s. Refresh Token
===


[TOC]

## JWT簡短回顧


為了進行身分驗證，Auth Server 頒發 Token 給Client端。Toekn的數據格式是JSON，其中包含特定於用戶的信息。

Client端在與API交談時可以使用此token （通過將其作為HTTP Header傳送），以便API可以識別Token 代表的用戶並採取特定於用戶的操作


JWT 的組成
---

分成 header、payload、signature/encryption data

1. header
```javascript=
{
  'typ': 'JWT', # 聲明類型
  'alg': 'HS256' 
  # 加密的方法: HMAC、SHA256、RSA 進行 Base64 編碼
}
```
2. payload : 帶有想存放的資訊（例如用戶資訊）
通常會使用 exp 設定 Token 到期的時間、iat 設定 Token 簽發時間
```javascript=
{
  "_id": "<user_id>", 
  "name": "Mike",
  "exp": 1300819380,
  "iat": 1525814520
}
```
3. signature/encryption data
由三個部分組成
(1)header (base64後的)
(2)payload (base64後的)
(3)secret


```javascript=
HMACSHA256(base64UrlEncode(header) + "." + base64UrlEncode(payload), 'secret')
```

:::info
❗️使用 JWT 的重點不在於把資料隱藏起來，畢竟 Payload 資料是可以被轉換回來的，但由於Server端才擁有密鑰，因此即使 payload 被修改，轉換成 Base64 重新置入 Token 中，透過與 Signature 比對之下，就能發現資料的不一致，產生驗證錯誤
:::

> secret 要保存在 server 端，jwt 的 簽發驗證都必須使用這個 secret，當其他人得知這個 secret，那就意味著Client端是可以自我簽發 jwt ，因此在任何場景都不應該外流。
> 像是自己把鑰匙給小偷，那這樣鎖匠在怎麼厲害也很難防盜。
>  [name=Kai]



使用方法(傳統)
---

```sequence
Client->Server: 1.使用帳密登入
Note right of Server: 2.產生僅能在Server端被驗證的 Token(JWT)
Server-->Client: 3.Token 被回傳後，存取在「Client端」
Client->Server: 發送請求時，會附帶此 Token 讓Server端驗證
Note right of Server:Server端在請求中沒有找到 Token，回傳錯誤；若有找到 Token 則驗證
Server-->Client: 回傳respose給Clinet端
```


重頭戲  Access token v.s. Refresh Token(進階)
---

>當使用者第一次向Auth Server要求登入時，Auth Server通常會同時回傳 Access token 和 Refresh token。

Access token: 

當Client端將Access Token 傳給 Resource Server 時，該 Server 可以使用token中包含的信息來決定是否授權給Client端。通常有一個過期時間，而且通常時間非常短暫。


Refresh token :

當Client端需要使用Access token 來訪問特定資源或是 
原本的Access Token expired time快過期的時候，
Client端可以使用Refresh token 來向Auth Server 請求下發新的Access token 和 Refresh Token。

:::info
Auth Server在每次換發Access Token時，會自動的將之前的Refresh Token變成無效。故當駭客拿舊的Refresh Token 來向Auth Server換發新token，就會馬上被揭穿。
:::

![image alt](https://user-gold-cdn.xitu.io/2018/1/14/160f2c332aa4c34a?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

通常情況下，當舊的Access token 失效之後，才需要獲得新的訪問令牌，或者是在第一次訪問資源的時候。Refresh Token也有過期時間但時間相對較長。
Refresh Token對儲存的要求通常會非常嚴格，以確保它不會被洩漏。
它們也可以被授權服務器列入黑名單。


![image alt](https://user-gold-cdn.xitu.io/2018/1/14/160f2bcf5950e9cd?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

結論: 

>Client端會存 Access Token和Refresh Token；Server端只存 Refresh Token 。分成兩個server的目的是可以讓分工變得更明確，減少重複的Access Token 訪問Auth Server的機會。

## 如果token被盜怎麼辦?
1.(被動) 縮短過期時間。JWT可以配置在設定的時間（一分鐘，一小時，一天，任何時間）之後自動過期，因此攻擊者只能使用您的JWT來訪問服務，直到它過期為止。

2.(主動)攻擊者首先能夠竊取你的token，那麼一旦你獲得了新token，他們很可能也可以竊取你的新token，這意味著切勿將Token存儲在Local Storage中，而應將Token存儲在JavaScript不可訪問的httpOnly cookie中。


## 如果JWT被盜怎麼辦?

(臨時解決方案)
1.立即吊銷受被盜的Token。如果您使用服務器上的DB來使Token無效，則吊銷Token可以立即將攻擊者引導出系統，直到他們掌握了新Token為止。
2.強制您的Client端立即更改其密碼

(長期)
3.檢查自己的服務器端環境

:::info
**有什麼觀念還不清楚嗎?** 下方留言告訴我!
:::

### 參考資料 
https://juejin.cn/post/6844903549395009544
https://www.rdegges.com/2018/please-stop-using-local-storage/
https://medium.com/@ryanchenkie_40935/react-authentication-how-to-store-jwt-in-a-cookie-346519310e81


### 聯絡方式 :sheep: 

You can find me on

- GitHub : 
  https://github.com/kai4idps
- Linkedin me : 
  https://www.linkedin.com/in/kai-cc-a445221b5/
- Blog: 
  https://chekai7.github.io/c.c.k
