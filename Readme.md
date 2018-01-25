將 mongodb container 的檔案寫入到 /var/lib/mongo/data/db
事前準備:
1. 先使用 commnad line 去取得 mongodb 的 image
> \# docker pull mongo

2. 先將 /var/lib/mongo/data/db 這些目錄透過 mkdir 建立出來
> \# cd /var/lib
> \# mkdir mongo
> \# mkdir data
> \# mkdir db

3. 在背景 run 一個 container, 命名為 mongodb
> docker run -v /var/lib/mongo/data/db:/data/db -p 27017:27017 --name mongodb -d mongo

4. 透過 docker exec 進入在背景執行的 container, 先做 auth user 帳號的設定, 並記住 container id
> docker exec -ti mongodb /bin/bash
> \# mongo
> \# use admin
> \> db.createUser({ user: '管理所有資料庫的帳號', pwd: '密碼', roles: [ { role: "userAdminAnyDatabase", db: "admin" } ] })
> \> db.createUser({ user: '讀寫專用帳號', pwd: '密碼', roles: [ { role: "readWrite", db: "toutiao" } ] })
> \> exit
> \# exit

5. 將變更過的 container 執行提交, 並且加上 tag: v1.0.0
> \# docker commit -m "Add init admin and user" 容器ID mongo:v1.0.0

6. 先將原本起的 container 刪除
> \# docker stop mongodb
> \# docker rm mongodb

7. 使用 docker-compose.yml 來跑 mongodb 的 container (記得先切到 docker-compose.yml 的當前目錄)
> \# docker-compose up -d

8. 查看 log
> \# docker-compose logs -f