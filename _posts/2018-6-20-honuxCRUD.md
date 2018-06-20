---
layout: post
title: 2018-6-20 DB개론 CRUD
---

MySQL CRUD 1
----

- use 데이터베이스이름;
- show tables;
- select * from tableName;
- select columnName[, ...] from tableName;


컬럼(열), 필드
\--------------------
컬럼1|컬럼2|컬럼3 <--- 스키마
정호영|100원|가난 <--- 데이터, 레코드, 로우(행)

#### 유저추가
- create user 'id'@'localhost' identified by 'password';
- localhost : local에서만 사용하고 싶다.
```
create user 'hoyoung'@'localhost' identified by 'db1004';
create user 'hoyoung'@'%' identified by 'db1004';
create user 'hoyoung'@'192.168.0.1' identified by 'db1004';
```
- %를 붙이면 어디서나 접속 가능한 데이터베이스를 만드는것이다.
- 192.168.0.1에서만 접속이 가능한 db를 만든다.

```
mysql> select User from user;
+---------------+
| User          |
+---------------+
| jimmy         |
| jpa_user      |
| mysql.session |
| mysql.sys     |
| root          |
+---------------+
5 rows in set (0.00 sec)
```

1. 유저가 사용할 데이터베이스를 만들기 : create database dbName;
2. 권한 설정 : grant all on dbName.* to 'user'@'%' with grant option; : 모든권한을 준다.

```
mysql> create user 'jimmy'@'%' identified by '12345';
Query OK, 0 rows affected (0.01 sec)

mysql> grant all on jimmydb.* to 'jimmy'@'%' with grant option;
Query OK, 0 rows affected (0.05 sec)
```
- 유저 jimmy에게 데이터베이스 jimmydb에서 사용할 수 있는 모든 권한을 양도?부여한다.

```
mysql> flush privileges;
Query OK, 0 rows affected (0.01 sec)
```
- flush privileges를 해야 권한을 서버에 업데이트를 한다.

![](/Users/jaeyeonkim/Desktop/mysql-1.png)

#### 생성한 user로 접속하기

```
 jaeyeonkim@jaeyeonui-iMac  ~  mysql -u jimmy -p
Enter password:
Welcome to the MySQL monitor.  Commands end with ; or \g.

mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| jimmydb            |
+--------------------+
2 rows in set (0.00 sec)

```
- 생성한 jimmy유저로 접속을 해서 show databases를 하면 내가 접속할 수 있는 데이터베이스의 목록이 나온다.


1. 테이블(표) 생성 : create tablse (columnName type, columnName type...); ```create table score (name char(32), korean tinyint, today date);  ```
2. 레코드 삽입 ```insert into score values('jimmy', 60, '2018-6-3');```
3. 검색 ```select * from score; ```, 특정 조건만 검색하기 ```select * from score where name = 'jimmy';```

```
drop table if exists score;
create table score (name char(32), korean tinyint, today date);
```
- tinyint는 매우 작은 integer를 나타낸다.

4. 삭제 : delete from tableName where 조건 ```delete from score name='jimmy'```
- 삭제시 주의사항은 먼저 삭제하려는 데이터가 맞는지 확인을 해야한다. ```select * from score where name='jimmy'```를 먼저 해서 해당 조건이 맞는지를 확인하고, 그런다음에 ```delete from score where name='jimmy'```를 해야한다. 이 습관은 매우 중요

5. 업데이트 : update tableName set 변경값 where 조건; ```update score set korean=100 where name='jimmy';```
