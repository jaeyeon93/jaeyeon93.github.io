---
layout: post
title: 2020-2-13 TIL
---


#### mysql 엔진별 select테스트하기

```mysql
select sum(unitprice) from myisam where saleschannel='offline'; -- 0.579 sec
select sum(unitprice) from inno where saleschannel='offline'; -- 8.317 sec

select max(unitssold) from inno; -- max is 10000 min i s 1 time is immediately
select min(totalrevenue) from inno; -- max is 6682700 min is 9.33 sec
select max(totalcost), min(totalcost) from inno; -- max is 5249600 min 6.92 7.2sec

select max(unitssold), min(unitssold) from myisam; -- max is 10000 min i immediately
select max(totalrevenue), min(totalrevenue) from myisam; -- max is 6682700 min is 6.92  time is immediately
select max(totalcost), min(totalcost) from myisam; -- max is 5249600 min 6.92 immediately

select country, itemtype, saleschannel from inno where unitssold > 3000 order by totalrevenue desc, totalprofit desc, totalcost asc; -- duration 7.867sec fetch time 91.589 sec, column 700084
select country, itemtype, saleschannel from myisam where unitssold > 3000 order by totalrevenue desc, totalprofit desc, totalcost asc; -- duration91.589 sec

select country, itemtype, saleschannel from inno where totalcost > 3500000 order by totalrevenue desc, totalprofit desc, totalcost asc; -- duration 7.3sec fetchtime 6.999sec 56216rows after indexing duration 8.115sec fetch time 7.310sec
select country, itemtype, saleschannel from myisam where totalcost > 3500000 order by totalrevenue desc, totalprofit desc, totalcost asc; -- duration 0.558 sec fetchtime 7.746 56216rows

-- make index on totalrevenue column
create index revenue on inno (totalrevenue);
show indexes from inno;
select max(totalrevenue), min(totalrevenue) from inno; -- max is 6682700 min is 6.92  time is immediately 인덱스를 거니까 속도가 미친듯이 증가됬다.(기존에 9.3초 걸리던게 즉시로딩이 된다)
select max(totalrevenue), min(totalrevenue) from myisam; -- max is 6682700 min is 6.92  time is immediately

select * from inno where totalrevenue > 3000000; -- duration is 0.012sec fetchtime 9.787sec
select * from myisam where totalrevenue > 3000000; -- duration is 0.0033sec fetchtime 1.187sec

select * from inno where totalrevenue > 3000000 order by totalrevenue desc, totalprofit desc, totalcost asc; -- duration is 8sec fetchtime 20sec applied index on totalrevenue rows 140992
select * from myisam where totalrevenue > 3000000 order by totalrevenue desc, totalprofit desc, totalcost asc; -- duration is 0.63sec fetchtime 17.419sec rows 140992
```


- myisam은 기본적으로 count, sum 연산에 빠르지만 innodb의 경우 그렇지 않았다. innodb에 인덱스를 걸고나서 max,min등을 연산을 하니 시간이 압도적으로 빨라졌다. 인덱스의 효과인거 같다.
- myisam에서 order by 연산이 매우 느리다고 했다. 특정 조건을 걸고, orderby를 3가지 column에 대해서 적용을 해봤다. 확실히 innodb는 빠르지만 myisam에서는 너무나도 느리다.
- 인덱스를 적용해도 order by 연산이 들어가면 느리다.



copy to table

- 30만개의 결과를 따로 테이블을 복사를 하고 여기서 나머지 Update, Delete, Create에 대한 테스트를 해보려고 한다.

구조복사

    create table new_table like old_table -- 구조복사
    create table new_table (select * from old_table) -- 구조와 데이터복사
    insert into destination_table (select * from source_table) -- 데이터복사

    create table my (select * from myisam where unitssold > 7000); -- copy to 300k data, duration 2.5sec
    create table inn(select * from inno where unitssold > 7000); -- duration 10.712 sec
    show table status; -- check tables status.
    alter table my engine = myisam;

- 구조 복사를 하더라도 기존의 테이블에 primary key 또는 auto_increment가 설정되어있으면 복사가 안된다.
- 구조복사를 했지만 db engine은 복사되지 않았다. myisam으로 변경을 해야된다.

make function

    CREATE [AGGREGATE] FUNCTION function_name
        RETURNS {STRING|INTEGER|REAL|DECIMAL}
        SONAME shared_library_name

    ROUND(X), ROUND(X, D)
    SELECT ROUND(1.298, 0); -- 1
    SELECT ROUND(1.298, 1); -- 1.3


    create function convert_cost return decimal?

- 연산을 하기 위해 function을 만들려고 한다. 현재 공식 레퍼런스에는 위와같은 정의로 mysql에서 사용자 정의 함수를 만들 수 있게 되어있다.
- 나의 경우 Totalprofit, totalcost가 현재 소수점 둘째자리까지 포함이 되는데, 소수점 둘째자리에서 반올림을 하는 함수를 만들려고 한다.
- Math.ROUND레퍼런스를 찾아보니. X는 Value, D는 소수점 몇자리수까지 포함을 할지를 결정한다.
