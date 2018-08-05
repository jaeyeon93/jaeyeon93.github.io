---
layout: post
title: 2018-8-5 BulkInsert
---

Mysql Bulk insert
----
```
create table btest (
	a int primary key,
    b varchar(16)
);

mysql> desc btest;
+-------+-------------+------+-----+---------+-------+
| Field | Type        | Null | Key | Default | Extra |
+-------+-------------+------+-----+---------+-------+
| a     | int(11)     | NO   | PRI | NULL    |       |
| b     | varchar(16) | YES  |     | NULL    |       |
+-------+-------------+------+-----+---------+-------+
2 rows in set (0.00 sec)

```
- 위의 구조로 데이터테이블을 설계를 한다.

```
public class BulkTest {
    private static Random r = new Random();

    /*
    mysql> desc btest;
    +-------+-------------+------+-----+---------+-------+
    | Field | Type        | Null | Key | Default | Extra |
    +-------+-------------+------+-----+---------+-------+
    | a     | int(11)     | NO   | PRI | NULL    |       |
    | b     | varchar(16) | YES  |     | NULL    |       |
    +-------+-------------+------+-----+---------+-------+
    2 rows in set (0.00 sec)
    */
    public static void main(String[] args) {
        BulkTest bulk = new BulkTest();
        int count = Integer.parseInt(args[0]);
        bulk.getData(count, 16);
    }

    private void progress(int curr, int tot) {
        if (tot < 1000)
            return;
        if (curr % (tot / 10) == 0) {
            System.err.println("*");
            return;
        }
        if (curr == tot - 1)
            System.err.println("\n");
    }

    private void getData(int count, int charlen) {
        for (int i = 0; i < count; i++) {
            progress(i, count);
            System.out.printf("%d,%s\n", i, genStr(charlen));
        }
    }

    private Object genStr(int charlen) {
        StringBuffer sb = new StringBuffer();
        for (int i = 0; i < charlen; i++)
            sb.append((char)('a' + r.nextInt(24)));
        return sb.toString();
    }
}

//result
0,ubttwdrudmrmnevb
1,sjajdekwracelxtr
2,sdnqfwubqxoqpsvj
...
...
97,vanqcmcihjbrcrsh
98,odjgjfhxxklvdrhc
99,ouplarenljgdisbv
```

- java src 디렉토리에서 $java db.BulkTest 100000 > big.csv명령어를 통해 값을 넣는다.

```
$mysql -u jimmy -p --local-infile=1
```
- MySql의 버전 문제로 위의 플래그를 추가를 해서 mysql에 접속을 해야한다.

```
load data local infile 'big.csv'
into table btest
fields terminated by ',';
```
- 위의 명령어를 실행한 후 select * from btest;를 실행하면 데이터 10만개가 들어가있다.

데이터베이스 개론 Select 1
----

```
$ git clone https://github.com/honux77/introdb
$ cd introdb/week2
$ cat run.sh
$ cat select.sql
$ ./run.sh
```

#### Select 사용법

```
mysql> select * from tableName;
```
- 전체 테이블이 출력



```
mysql> select column1, column2 from tableName;
```
- 컬럼 1과 2만 출력이 됨

```
mysql> select column1, column2 from tableName where 조건;
```
- 전체 레코드중에서 조건을 만족하는 레코드의 컬럼1과 컬럼2 값이 출력이된다.

```
mysql> select id, name, grade from auction_user where grade='D';
```
- 위의 명령어를 실행을 하면 조건에 해당하는 행(row)들이 밑에처럼 출력이 된다.
![](/Users/jaeyeonkim/Desktop/dbSelect-1.png)

#### where 조건절

- where뒤에 나오는 조건절에는 <>,>, =, <, >=와 같은 부등호
- and, or와 같은 논리연산자
- like를 이용한 문자열부분 검색 등이 올 수 있다.
- VALUE 표현식
  "%VALUE" : 뒤가 VALUE로 끝나는 값.
  "VALUE%" : VALUE로 시작하는 값.
  "%VALUE%" : VALUE가 들어가는 값.

  ```
  mysql> select name, money from auction_user where money > 8000;
  mysql> select name, money from auction_user where money > 8000 and grade <>'B';
  ```

 - % : 0개 이상의 임의의 글자
 - _ : 1개이상의 임의의 글자
 - ex) '김%' : 김으로 시작하는 모든 문자열
 - ex) '김_' : 김으로 시작하는 두글자 문자열
 - ex) '_호%' : 두번째 글자가 호인 문자열

![](/Users/jaeyeonkim/Desktop/dbSelect_2.png)

#### Select는 닫힌 연산자입니다.
- select의 결과에 또 다시 select를 적용할 수 있다.

#### select의 결과의 종류

1. 스칼라 : 단하나의 값만 나오는 경우
```
mysql> select name from auction_user where name = '김배영';
+-----------+
| name      |
+-----------+
| 김배영    |
+-----------+
1 row in set (0.00 sec)
```

2. 튜플 : 하나의 레코드가 결과로 나온 경우
```
mysql> select * from auction_user where name = '김배영';
+------+-----------+-------+-------+
| id   | name      | grade | money |
+------+-----------+-------+-------+
| 8471 | 김배영    | P     |  2900 |
+------+-----------+-------+-------+
1 row in set (0.00 sec)
```
3. 테이블 : 그 외의 경우

#### 문제풀이

1. 소지금액이 1000원이상 1200원 이하인 사용자의 아이디, 이름, 소지금액을 검색해라.
```
select id, name, money from auction_user where money >= 1000 and money <= 1200;
```
```
mysql> select id,name,money from auction_user where money between 1000 and 1200;
```
- between을 더 많이 사용을 하게 된다.

2. 사용자의 등급을 검색해라
 ```
 mysql> select grade from auction_user;
 ```
3. 사용자의 등급을 중복제외하고 검색하라
```
mysql> select distinct grade from auction_user;
+-------+
| grade |
+-------+
| B     |
| U     |
| P     |
| G     |
| S     |
| D     |
+-------+
6 rows in set (0.01 sec)
```
4. 사용자의 등급을 중복되지 않게 알파벳 내림차순으로 정렬해서 출력하시오
```
mysql> select distinct grade from auction_user order by grade;
+-------+
| grade |
+-------+
| B     |
| D     |
| G     |
| P     |
| S     |
| U     |
+-------+
// 오름차순
```

```
mysql> select distinct grade from auction_user order by grade desc;
+-------+
| grade |
+-------+
| U     |
| S     |
| P     |
| G     |
| D     |
| B     |
+-------+
// 내림차순
```

- distinct : 중복제거
- order by column : column을 기준으로 오름차순
- order by column desc : column을 기준으로 내림차순


5. 이름이 김씨인 사람을 출력하세요
```
select name from auction_user where name like '김%';
```
6. 김씨이면서 이름이 세글자인 사람을 출력하세요
```
select name from auction_user where name like '김__';
```

7. B등급 사용자는 몇 명인가요?
```
mysql> select grade, count(*) as num_people from auction_user where grade='D';
+-------+------------+
| grade | num_people |
+-------+------------+
| D     |       1692 |
+-------+------------+
```
8. 등급별 사용자 수를 출력하세요

```
mysql> select grade, count(*) as num_people from auction_user group by grade;
+-------+------------+
| grade | num_people |
+-------+------------+
| B     |       1667 |
| D     |       1692 |
| G     |       1630 |
| P     |       1685 |
| S     |       1639 |
| U     |       1686 |
+-------+------------+
```

9. 현재 시간이 몇시인가요?
```
mysql> select now();
+---------------------+
| now()               |
+---------------------+
| 2018-06-13 20:47:23 |
+---------------------+
```
![](/Users/jaeyeonkim/Desktop/dbSelectProblem.png)

```
mysql> select log(2,8) * sin(pi()/4)
    -> ;
+------------------------+
| log(2,8) * sin(pi()/4) |
+------------------------+
|     2.1213203435596424 |
+------------------------+
```
