---
title: Nginx
tags: devops
description: View the slide with "Slide Mode".
---

# Nginx

---

**Nginx** 是一款由俄羅斯程式設計師Igor Sysoev所開發
輕量級的網頁伺服器、反向代理伺服器

---

## 什麼是反向代理伺服器?

---

反向代理（Reverse Proxy）方式是指
以代理服務器來接受internet 上的連接請求，
然後將請求轉發給內部網絡上的服務器，
並將從服務器上得到的結果返回給internet 上請求連接的客戶端，
此時代理服務器對外就表現為一個反向代理服務器。

---


**生活舉例**：Dcard 的網站或 App 每天都會有使用者回報服務的異常與問題，而使用者只知道他跟 Dcard 內部反映問題了，卻不知道是 Dcard 的哪位員工看到了他的回應並去做處理，而使用者實際上也不在乎是誰看到他的訊息的，只要問題有順利解決就好。

**技術術語**：Client 端發送一個 request 到我想要連的網站，這個網站背後可能就會有好幾台的 Server 來為我們服務（ex: 負載平衡），為了應付高流量的狀況，會將請求分配到不同的 Server，而 Client 根本不會知道具體到底是哪一台 Server，**而 Client 也不需要知道**，Client 只需要知道反向代理 Server是誰就好。

---


![Reverse Proxy](https://raw.githubusercontent.com/dunwu/images/dev/cs/web/nginx/reverse-proxy.png)

---

### 如何安裝nginx

```javascript=
2選1

(1)
sudo apt-get update
apt-get install nginx

(2)
docker run -d -p 7777:80 --name nginx-web-server nginx
```

---


### 常用指令

---

```javascript=
nginx -t 不運行，自己測試配置文件。nginx 將檢查配置文件的語法的正確性，並嘗試打開配置文件中所引用到的文件。
nginx -s reload 因改變了Nginx 相關配置，需要重新加載配置而重載。
nginx -v 顯示nginx的版本。
nginx -V 顯示 nginx 的版本、編譯器版本和配置參數。
```

---

### Nginx 的 Config 檔

---

Nginx 的主要設定檔通常會放置在 /etc/nginx/nginx.conf
另外在 /etc/nginx/conf.d/*.conf 則會放置不同域名的 config file
例如： /etc/nginx/conf.d/kylemocode.com.conf
然後在主設定檔中的 <span style="color:blue">http context text</span> 加入一行
```javascript=
include /etc/nginx/conf.d/*.conf;
```
即可將不同域名的設定引入，達成方便管理與修改不同域名設定的特性。



---

範例：


![](https://i.imgur.com/8Mgo394.png)

---


### nginx 基本設置

在 /etc/nginx/conf.d/ 建立 default.conf 的設定檔，來為 Nginx Server 建立基礎的配置

---

```javascript=
// default.conf

upstream api {
    server localhost:5000;
    server localhost:5001;
}

server {
    listen 80;
    listen [::]:80;
    server_name SERVER_IP;
    root /home/ryan;

    proxy_set_header Host $http_host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;

    location / {
            proxy_pass http://api/;
    }
}
```


---

#### 講解 default.conf

---


### upstream

<span style="color: brown">Server對內的port</span>.

upstream block 定義了我們想要將 request proxy 過去的應用，上圖中的例子代表我們可以將請求 proxy 到分別監聽 5000 與 5001 port 的兩個應用，聰明的讀者應該猜到了，這個 block 可以讓我們達到 load balancer 負載平衡的功能。




---

### server

<span style="color: brown">Server對外的port</span>.


這個 block 則是定義了 proxy server 的相關設定，包括要監聽的 port (http 為 80 ，https 為 443)、規定哪些 domain 或 ip 的 request 會被 nginx server 處理（server_name）。

---

### location

這個 block 非常重要，不過幸好概念還蠻易懂的，它其實就像是 routing 的概念，
設定不同的 path 要對應到怎麼樣的設定。上圖的範例 location 後面接的是 /，
代表任何路徑都會被這個 block 給接收處理。

---

### https

要開啟 https ，是必須取得 SSL certificate 的，這個範例中我選擇使用 certbot 這個 package 取得來自 **Let’s Encrypt** 的免費憑證（有效期限 90 天）


```javascript=
sudo yum install certbot
certbot certonly --standalone -d DOMAIN_NAME
```

完成指令後 certificate 與 private key 會被建立在 
<span style="color:brown">./etc/letsencrypt/live/DOMAIN_NAME/</span> 路徑中，
在接下來的 Nginx Conf 中會引入它們。

---

#### 簡單範例：

值得注意的是我們將來自 80 port (http) 的 request 重新導回 https 的 block，這也就是為什麼某些網站你輸入的是 http，它還是會自動幫你導回 https 的原因


```javascript=
server {
    listen 80;
    listen [::]:80;
    server_name DOMAIN_NAME;
    return 301 https://$host$request_uri;
}

  server {
    listen 443 ssl http2;
    listen [::]:443 ssl http2;
    server_name DOMAIN_NAME;
    root /home/ryan;

    ...
    ...

    // 這邊是重點
    ssl_certificate /etc/letsencrypt/live/DOMAIN_NAME/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/DOMAIN_NAME/privkey.pem;
    //
    
    ssl_session_timeout 1d;
    ssl_session_cache shared:SSL:50m;
    ssl_session_tickets off;

    ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
    ssl_prefer_server_ciphers on;

    add_header Strict-Transport-Security max-age=15768000;

    resolver 8.8.8.8;
    
    ...
    ...
}
```




---

### load balancer

這個範例就單純多了，要達到 load balancer 透過一開始介紹的 upstream block 就可以達成，在上面的例子中，
來自某個 domain 80 port 的任何 path 都會被分配到 port 5000 或 port 5001 兩個應用中，達成用兩個應用去分擔 request 的負載平衡器。


```javascript=
upstream api {
        ip_hash;
        // server 對內的port
        server localhost:5000;
        server localhost:5001;
}

server {
    //server對外的port
    listen 80;
    listen [::]:80;
    server_name SERVER_IP;
    root /home/ryan;

    proxy_set_header Host $http_host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;


    location / {
            proxy_pass http://api/;
    }
    
    location /socket.io/ {
            proxy_http_version 1.1;
            proxy_set_header Upgrade $http_upgrade;
            proxy_set_header Connection "upgrade";
            proxy_pass http://nodejs/socket.io/;
    }

  
}

```

詳解
```javascript=
proxy_pass = 導流到根目錄
# 指到設定的 upstream 及 protocol

例如：
location /app/ {
    proxy_pass      http://192.168.1.100/;
}

test.com/app/xxxxx =>  http://192.168.1.100/xxxxx

```

---




### 實際範例 default.conf ＆＆ load balancer

![](https://i.imgur.com/rqITdD3.png)




---

### 看logs

access log 位置會在 <span style="color:brown">/var/log/nginx/access.log</span>，
這邊推薦可以使用 GoAccess 將資訊圖像化，如果不想使用手工，可以搭配 Linux 的 crontab 排程進行自動更新。
error log 位置會在 <span style="color:brown">/var/log/nginx/error.log</span>

---



### 參考資料

- https://medium.com/starbugs/web-server-nginx-2-bc41c6268646
- https://linyencheng.github.io/2019/07/13/tool-nginx/
- https://github.com/dunwu/nginx-tutorial :tada: 

---

### Thank you! :sheep: 

You can find me on

- GitHub : 
  https://github.com/kai4idps
- Linkedin me : 
  https://www.linkedin.com/in/kai-cc-a445221b5/
- Blog: 
  https://chekai7.github.io/c.c.k
