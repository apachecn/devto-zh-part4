# ð³å¨ Docker ä¸ç»å½ MySQL å¹¶è¿è¡ SQL æä»¶

> åæï¼<https://dev.to/n350071/login-to-mysql-on-docker-and-run-a-sql-file-2bk7>

## ð¤æåµ

ä½ ç¨ MySQL ç»´æ¤ä¸ä¸ª appï¼å¨ Docker ä¸å¼åãæ¨æä¸ä¸ª. sql æ ¼å¼çè½¬å¨æä»¶ï¼ç¶åæ¨æ³è¿è¡å®ã

## ðç¨åº

### 1ãå° SQL æä»¶å¤å¶å° Docker å®¹å¨ä¸­

```
$ docker cp hoge.sql [cotaier-id]:/hoge.sql 
```

### 2ãç»å½å°åç«

```
$ docker ps
$ docker exec -it [container-id] /bin/bash 
```

### 3ãç»å½ MySQL

```
$ mysql -u root -p
$ show databases;
$ use [your_database_name];
$ show tables; 
```

ð¡æç¤º:å¦æä½ ä½¿ç¨ rails åºç¨ï¼å¯ç åè¿æ¥ä¿¡æ¯åºè¯¥å¨`config/database.yml`å®ä¹ãæè®¸ï¼å¶å®æ¯åå¨`.env`æä»¶éçã

### 4ãè¿è¡ SQL æä»¶

```
$ source hoge.sql 
```

* * *

## æ¯æ³¨

[![n350071 image](img/c9dd0fa7ece3b4a6c3e55d373898cd1e.png)](/n350071) [## æçç å¤´ç¬è®°

### n350071ð¯ðµ10 æ 29 æ¥ 191 åééè¯»

#docker](/n350071/my-docker-note-3d0m)