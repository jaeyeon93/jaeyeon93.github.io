---
layout: post
title: 2018-8-26 복합인덱스와 클러스터드 인덱스
---

복합인덱스
--

#### Index는 검색성능을 굉장히 향상 시킨다.

- create index indexName on table(column);을 통해 인덱스를 생성한다.

```
create index index_ex1 on user(money);
```
#### 테이블에는 기본적으로 PrimaryKey에 PrimaryIndex, ForeignKey에 SecondaryIndex가 존재를 한다.

#### 인덱스삭제하기 :  drop index indexName on tableName;


### Bulk insert를 할때

1. 인덱스제거 - 데이터삽입 - 인덱스재생성
2. 인덱스일시중지 - 데이터삽입 - 인덱스활성화
#### BulkInsert를 할때는 1,2번경우를 사용할 수 있는데, 1번이 더 빠르다.

### 인덱스 일시중지

```
alter table user disable keys;

// index restart
alter table user enable keys;
```

### 복합인덱스만들기
- 여러컬럼을 묶어서 인덱스를 만든다.

```
// 기존의 인덱스를 삭제한다.
drop index idx_user1

// 새로운 인덱스를 만든다.
create index idx_user2 on user(last_visit, money);
create index idx_user2 on user(money,last_visit);
```
- 위의 인덱스 생성은 서로 완전히 다르다.!!!!!

### 복합인덱스 성능시험

```
// last와 money를 함께 찾을때
mysql> select count(*) from user where last_visit > '2018-6-1' and money = 1000;
+----------+
| count(*) |
+----------+
|      161 |
+----------+
1 row in set (0.39 sec)

// 머니만 찾을때
mysql> select count(*) from user where money = 1000;
+----------+
| count(*) |
+----------+
|      194 |
+----------+
1 row in set (0.34 sec)

//last_visit만 찾을때
mysql> select count(*) from user where last_visit > '2018-6-1';
+----------+
| count(*) |
+----------+
|  1697674 |
+----------+
1 row in set (0.31 sec)
```
- 위에서 복합인덱스를(last_visit, money)순으로했기때문에 둘다 검색을 하거나 last_visity만을 하면 성능이 빠르지만, money만 검색을 하면 성능이 저하가 된다. 저하가 되어야되는데 위의 그림에서는 아니다...
- 복합인덱스를 생성할때 순서가 (last_visit, money)순으로 생성을 했다. 검색을 하게될때 last_visit, money가 둘다 있으면 빠르다. last_visit만 있어도 빠르다. money만 있으면 느리다. 왜 그런지 이유를 알려면 B-Tree구조에 대해서 알아야한다.

## 인덱스의 종류

### 물리적 저장구조에 따라 Clustered Index와 Secondary Index(Non-clustered)

### 논리적 저장구조에 따라 Unique Index, non-unique index
- Primary Key는 Unique Index이다. FK는 non-unique index이다.

### Clustered Index : 저장구조자체를 말한다. Innodb구조로 주로 저장을 한다.

클러스트인덱스는 Btree기준으로 정렬이 되어있고, 범위검색을 위해 같은 노드에서 이웃노드로 연결이 되어잇다.


- 항상 PK기준으로 정렬되어있고, B-tree가 (PK)를 기준으로 정렬되어있고, datapage가 PK를 기준으로 정렬되어있는 구조를 클러스터인덱스라고 한다.


### 클러스터 인덱스

- 클러스터드 인덱스는 사실 인덱스가 아니라 저장구조 그 자체
- B+트리를 사용
- 리프노드 : 실제레코드(데이터페이지)
- 키 : PrimaryKey

![](/Users/jaeyeonkim/Desktop/clusteredIndex.png)


### Secondary Index

- 직접 인덱스를 만들면 세컨더리 인덱스를 생성
- 세컨더리 인덱스의 구조는 B+트리를 사용, 키 : 인덱스를 생성한 컬럼
- 리프노드 : 해당 레코드의 Primary KEY값을 저장
#### 실제데이터를 찾으려면 인덱스를 두 번 찾아야함
