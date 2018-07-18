---
layout: post
title: 2018-7-14 DB개론 (3)
---

데이터베이스 개론 : 데이터타입
---

- 데이터 타입 : 성능에 영향을 끼친다. 데이터타입이 공간을 차지하기 때문이다.

#### 숫자형 데이터 타입

- TINYINT : 1byte (0000 0000 ~ 1111 1111까지) == (00~FF, -128~127까지)
- SMALLINT : 2byte
- INT : 4byte (20억)
- BIGINT : 8Byte
- FLOAT, DOUBLE, DECIMAL(=NUMERIC, DEC)굉장히 정확
- UNSIGNED사용 가능(양수만 사용)

#### 시간 데이터 타입

- date ex) 2013.5.17
- time ex) 11:30
- datetime ex) '2014.5.12 12:30'
- year
- timestamp는 현재시간이 자동으로 들어가는 데이터 타입이다.

```
create table test(a int, b timestamp);

insert into test values(1, '2018-6-9 21:10');

select * from test;

insert into test(a) values(2);
```
- timestamp의 경우 아무것도 입력하지 않을때 현재시간이 그대로 들어간다. insert뿐만 아니라 update의 경우에도 마찬가지로 적용이 된다.

#### 문자열 데이터타입
- char(고정길이), varchar(가변길이), text(긴 데이터)

#### 대용량 데이터타입
- text, blob
- Binary Large Object : BLOB
- Blob의 경우 바이너리 데이터이므로 이미지 파일 등도 저장가능
- 실제로는 이미지는 하드디스크에 저장을 하고, db에는 이미지의 주소만 저장을 한다.

#### 공간 데이터 타입(3D공간을 표시)
- point, linestring, polygon
- point의 경우 x,y,z점을

#### bulk insert
- 100,000개의 데이터를 테이블에 임의로 넣는다.
