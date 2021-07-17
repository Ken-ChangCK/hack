---
title: Docker
tags: devops
description: View the slide with "Slide Mode".
---

# Docker!

---

Docker 的目標是實作輕量級的**作業系統虛擬化**解決方案

Docker 讓使用者不需要去關心容器的管理，使得操作更為簡便。:heart:
使用者操作 Docker 的容器就像操作一個快速輕量級的虛擬機一樣簡單

---

## 優點

- 更簡單的管理     
- 更快速的交付和部署 :+1:
- 更輕鬆的遷移和擴展 :cat: 

---

#### 更簡單的管理

使用 Docker，只需要小小的修改，就可以替代以往大量的更新工作。所有的修改都以增量的方式被分發和更新，從而實作自動化並且有效率的管理。


---

#### 更快速的交付和部署

對開發和維運（DevOps）人員來說，
最希望的就是**一次建立或設定，可以在任意地方正常執行。**

開發者可以使用一個標準的映像檔來建立一套開發容器，
開發完成之後，維運人員可以直接使用這個容器來部署程式碼。

Docker 可以快速建立容器，快速迭代應用程式，並讓整個過程全程可見，
使團隊中的其他成員更容易理解應用程式是如何建立和工作的。

Docker 容器很輕很快！容器的啟動時間是秒級的，大量地節約開發、測試、部署的時間。



---

#### 更輕鬆的遷移和擴展

Docker 容器幾乎可以在任意的平台上執行，包括實體機器、虛擬機、公有雲、私有雲、個人電腦、伺服器等。 這種兼容性可以讓使用者把一個應用程式從一個平台直接遷移到另外一個。

---


## 基本介紹

映像檔(Image) vs 容器 (Container) vs 倉庫 (Repository)

---

#### 映像檔(Image) 

==容器要打包的程式==

Docker 映像檔就是一個唯讀的模板。
**映像檔可以用來建立 Docker 容器**

---


#### 容器 (Container)

==隔離好的環境==

容器是從映像檔建立的執行實例。
Docker 利用容器來執行應用。
可以被啟動、開始、停止、刪除。
每個容器都是相互隔離的、保證安全的平台。

---

#### 倉庫 (Repository)

倉庫是集中存放映像檔檔案的場所。
每個倉庫中又包含了多個映像檔。
每個映像檔有不同的標籤（tag）。
倉庫分為公開倉庫（Public）和私有倉庫（Private）兩種形式。



---

![image alt](https://blog.scottchayaa.com/post/2018/11/04/1.png)

---

### dockerfile

> Dockerfile 是一個設定檔，專門管理單一 Container，讓我們可以透過指令的設定，快速地更新 Container 的環境。
簡單的說，dockerfile 就是描述如何透過文字檔產生客製化的 Docker image


透過 dockerfile 之後，整個新的 Linux 都是以 code 形式描述，
你只要將 dockerfile 給其他人，
對方只要使用 docker build 就能自行根據 dockerfile 建立 Docker image，
最後再以 docker run 執行客製化過的 docker container


---



#### Dockerfile範例

```javascript=
// Dockerfile
FROM node:14.17.1-alpine3.12

USER root
WORKDIR /home/docker-test

ENV PORT=7779

EXPOSE ${PORT}

COPY . /home/payment-service

RUN npm ci
RUN npm run build

CMD ["npm", "run", "start"]
```

---

### Docker Compose

如一個典型的 Web 服務，以 .NET Core 為例，最少就必須有

.NET Core Runtime + Nginx + Redis + PosgreSQL

4 個 service 一起運行，若只使用 docker run，則勢必寫 Bash 來管理 4 個 service，還必須考慮：

4 個 service 必須在同一個虛擬 network 下
4 個 service 哪些檔案需與 host 共用
4 個 service 的啟動順序

Docker 為此提出 Docker Compose 概念，在 docker-compose.yml 檔描述各 service 間的參數與關係：


---

```javascript=
version: "3"

services:
  netcore:
    image: microsoft/dotnet
    container_name: MyNETCore
    volumes:
      - ${NETCORE_HOST_DIR}:/code/
    tty: true
    networks: 
      - netcore-dev
    depends_on:
      - postgres

  postgres:
    image: postgres
    container_name: MyPostgres
    volumes:
      - ${POSTGRES_HOST_DIR}/data:/var/lib/postgresql/data
    expose:
      - "5432"
    ports:
      - "${POSTGRES_PORT}:5432"
    environment:
      - POSTGRES_DB=${POSTGRES_DB}
      - POSTGRES_USER=${POSTGRES_USER}
      - POSTGRES_PASSWORD=${POSTGRES_PASSWORD}
    networks:
      - netcore-dev

networks:
  netcore-dev:
```


- docker-compose.yml 檔案很小，只是文字檔而已
- 由於 docker-compose.yml 是文字檔，可以 git 版控

---




## 基本指令

---

#### build docker image 

```javascript=
docker build . -t kk-bv-web-image
// . -> 目前檔案位置
// -t -> docker 互動模式
```

---



#### Run 起一個 Container

```javascript=
docker run -d --name kk-bv-web-container -p 5020:3000 kk-bv-web-image
```
用 docker-images 這個 image 背景執行一個容器環境，取名叫做 docker-container。

-d 代表在 Detached（ 背景 ）執行，如不加 -d，預設會 foreground ( 前景 ) 執行

-p 代表將本機的 9090 port 的所有流量轉發到 container 中的 9090 port

--name 設定 container 的名稱

---

#### 進入一個container

```javascript=
docker ps
docker exec -it xxxx /bin/sh
```
進入執行環境中的 /bin/sh 資料夾下，類似於 SSH 進入機器的概念


---

### 刪除container 或 image

```javascript=
docker ps // contaner
docker rm -f xxx

docker image list //image
docker rmi xxxxx

```

---

### Dockerfile 搭配 entrypoint.sh檔

#### 記得要先 chmod -x ./entrypoint.sh ，讓linux知道此檔案是可以被執行的

```javascript=
// dockerfile 
FROM node:14.16.1-alpine3.12

USER root
WORKDIR /home/KK-BV/Web

COPY . ${WORKDIR}

RUN apk update && \
     apk add git
RUN yarn install --frozen-lockfile
RUN yarn run build

EXPOSE 3000

RUN ls -l
RUN echo "PWD is: $PWD"
RUN pwd

RUN chmod +x /home/KK-BV/Web/entrypoint.sh
# RUN cat /home/KK-BV/Web/entrypoint.sh

ENTRYPOINT ["/home/KK-BV/Web/entrypoint.sh"]

# CMD ["yarn", "run", "start"]

```
```javascript=
//entrypoint.sh
echo hello world
cp ./.env.kk ./.env.production
echo copy done

echo start cat
cat ./.env.production
echo end cat

echo start
yarn run start

// dockerfile
RUN chmod +x /home/KK-BV/Web/entrypoint.sh
# RUN cat /home/KK-BV/Web/entrypoint.sh

ENTRYPOINT ["/home/KK-BV/Web/entrypoint.sh"]
```


---


### Docker Set environment variables 搭配 Next.js runtime environment

```javascript=
docker build . -t "docker-image"

docker run -d  -e NEXT_PUBLIC_MEMBER_API_ENDPOINT='https://test.tw:5001' -e NEXT_PUBLIC_ENABLED_EMAIL_SIGNIN='false'  --name docker-container -p 5020:3000 docker-image
```

---


### docker-compose

啟動 docker container
前提是要有 docker-compose.yml 

```javascript=
method 1:
docker-compose up -d  // Start Docker instance
docker-compose down   // Stop Docker instance

method 2:
docker stack ls //Check if you need to delete Docker instance
docker stack rm xxxx //Delete  Docker instance if needed

docker stack deploy --compose-file docker-compose.yml xxx //Deploy Docker instance

---
// build with no cache
docker-compose build --no-cache
// start the services
docker-compose up
// list the services
docker-compose ps
// list the containers
docker ps
// stop services
docker-compose stop
```

---

### dockerfile v.s. docker-compose.yml :+1: 

<style>
code.blue {
  color: #337AB7 !important;
}
code.orange {
  color: #F7A004 !important;
}
</style>

<code class="orange">dockerfile</code> 是用來描述 image，也就是如何產生客製化的 image，通常用來安裝 package / dependency，將檔案複製進 image 用

<code class="blue">docker-compose.yml</code> 是用來描述 container，也就是管理一個以上的 container，彼此串連，把同一組架構寫在一起，通常用來設定 container 參數，設定 container 的網路，設定 container 啟動順序或 service 環境變數 … 等


---

# :fork_and_knife: 
假如今天你修改了 `docker-compose.yml` 又或是更新了 image,

當你要重建 docker , 有幾種方法

- 方法一.

先停止 container, 執行 `docker-compose down` 再執行 `docker-compose up`.

- 方法二.

不需要停止 container, 直接執行 `docker-compose up -d`.

(他會自動幫你重建, 很方便, 不需要多一步先關閉 container )

結論, 只要你的 docker-compose.yml 有任何變動, 一定要執行 docker-compose up 才會生效.

---

#### Dockerfile ARG v.s ENV

<style>
code.blue {
  color: #337AB7 !important;
}
code.orange {
  color: #F7A004 !important;
}
</style>

- We use `ARG` in building images stage
- We use `ENV` in runngin containers stage

[參考連結](https://blog.scottchayaa.com/post/2018/11/04/docker-arg-env-variable/)


--- 


# :100: :muscle: :tada:

---

### 參考網址

- https://philipzheng.gitbook.io/docker_practice/basic_concept/container
- [A small library to solve messaging pain](https://ithelp.ithome.com.tw/articles/10190728)
- [TypeScript Rocks :tada: ](https://github.com/twtrubiks/docker-tutorial)
- https://fpjs.fun/docker/docker-compose/
- https://askie.today/docker-dockerfile-dockercompose-intro/
- https://docs.docker.com/compose/environment-variables/
- https://www.saltycrane.com/blog/2021/04/buildtime-vs-runtime-environment-variables-nextjs-docker/

---

### Thank you! :sheep: 

You can find me on

- GitHub
- Twitter
- or email me
