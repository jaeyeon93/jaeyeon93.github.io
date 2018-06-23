Mysql Create Table
----

```
insert into score values('jimmy', 60, '2018-6-5'), ('pobi',100,'2018-6-5');
```
- 2개의 데이터를 한꺼면에 볼 수 있다-

![](/Users/jaeyeonkim/Desktop/db_dataType.png)

- char는 고정이기때문에 Char(8)에서는 글자가 1개인 경우에도 100칸을 차지를 한다.
- varchar(8)에서는 가변길이이다.

```
create table auction_user(
	id char(16),
    name varchar(32),
    birth date,
    last datetime,
    money dec(8,2)
);
```

```
mysql> insert into auction_user (id, name) values ('a001', 'jaeyeon');
Query OK, 1 row affected (0.01 sec)

mysql> select * from auction_user;
+------+---------+-------+------+-------+
| id   | name    | birth | last | money |
+------+---------+-------+------+-------+
| a001 | jaeyeon | NULL  | NULL |  NULL |
| a001 | jaeyeon | NULL  | NULL |  NULL |
+------+---------+-------+------+-------+
2 rows in set (0.00 sec)
```

- name은 중복이 가능하지만, **id는 중복을 하면 안된다. id는 반드시 고유한 값**을 가져야한다.
- 고유한 값을 Primary Key라는 기본키로 조건을 해야한다.



```
drop table if exists auction_user;
create table auction_user(
	id char(16),
    name varchar(32),
    birth date,
    last datetime,
    money dec(8,2),
    primary key(id)
);

mysql> desc auction_user;
+-------+--------------+------+-----+---------+-------+
| Field | Type         | Null | Key | Default | Extra |
+-------+--------------+------+-----+---------+-------+
| id    | char(16)     | NO   | PRI | NULL    |       |
| name  | varchar(32)  | YES  |     | NULL    |       |
| birth | date         | YES  |     | NULL    |       |
| last  | datetime     | YES  |     | NULL    |       |
| money | decimal(8,2) | YES  |     | NULL    |       |
+-------+--------------+------+-----+---------+-------+
5 rows in set (0.00 sec)
```
- 위의 데이터 설계처럼 primary_key(id)로 id를 기본키로 지정을 한다.

```
mysql> insert into auction_user (id, name) values ('a001', 'jaeyeon');
Query OK, 1 row affected (0.01 sec)

mysql> insert into auction_user (id, name) values ('a001', 'jaeyeon');
ERROR 1062 (23000): Duplicate entry 'a001' for key 'PRIMARY'
```
- primary제약조건으로 인해서 중복된 id의 값은 삽입이 안된다.

```
mysql> select * from auction_user;
+------+---------+-------+------+-------+
| id   | name    | birth | last | money |
+------+---------+-------+------+-------+
| a001 | jaeyeon | NULL  | NULL |  NULL |
| a002 | NULL    | NULL  | NULL |  NULL |
+------+---------+-------+------+-------+
```
- null이 안되는 부분에 not null의 제약조건을 걸어야한다.

```
mysql> desc auction_user;
+-------+--------------+------+-----+---------+-------+
| Field | Type         | Null | Key | Default | Extra |
+-------+--------------+------+-----+---------+-------+
| id    | char(16)     | NO   | PRI | NULL    |       |
| name  | varchar(32)  | NO   |     | NULL    |       |
| birth | date         | YES  |     | NULL    |       |
| last  | datetime     | YES  |     | NULL    |       |
| money | decimal(8,2) | YES  |     | NULL    |       |
+-------+--------------+------+-----+---------+-------+
```
- id, name의 null부분이 no가 됬다.

#### default

- 값을 넣지 않았을때 기본적으로 들어가는 값이다.

```
mysql> insert into auction_user (id, name) values ('a001', 'jimmy');
Query OK, 1 row affected (0.01 sec)

mysql> select * from auction_user;
+------+-------+-------+------+---------+
| id   | name  | birth | last | money   |
+------+-------+-------+------+---------+
| a001 | jimmy | NULL  | NULL | 1000.00 |
+------+-------+-------+------+---------+
```
- money의 값을 넣지 않았음에도 불구하고 1000이 기본으로 들어와 있다.

#### Unique

- 기본키 외에 고유한 값을 가지는 컬럼에 unique를 사용한다.

```
drop table if exists auction_user;
create table auction_user(
	id char(16) not null,
    name varchar(32) not null unique,
    birth date,
    last datetime,
    money dec(8,2) not null default 5.00,
    primary key(id)
);

drop table if exists auction_user;
create table auction_user(
	id char(16) not null,
    name varchar(32) not null,
    birth date,
    last datetime,
    money dec(8,2) not null default 5.00,
    primary key(id),
    unique(name)
);
```
- 위와 같이 2가지 방법으로 unique를 설정할 수 있다.
- 하지만 name을 unique로 사용을 하면 안좋다.

```
mysql> desc auction_user;
+-------+--------------+------+-----+---------+-------+
| Field | Type         | Null | Key | Default | Extra |
+-------+--------------+------+-----+---------+-------+
| id    | char(16)     | NO   | PRI | NULL    |       |
| name  | varchar(32)  | NO   |     | NULL    |       |
| ssn1  | char(6)      | YES  | MUL | NULL    |       |
| ssn2  | char(7)      | YES  |     | NULL    |       |
| last  | datetime     | YES  |     | NULL    |       |
| money | decimal(8,2) | NO   |     | 5.00    |       |
+-------+--------------+------+-----+---------+-------+
```
- ssn1+ss2의 값이 unique로 된다.

#### primary key auto_increment를 사용을 하면 자동을 id값이 증가를 한다.

```
create table auction_user(
	id int primary key auto_increment,
    name varchar(32) not null,
    ssn1 char(6),
    ssn2 char(7),
    last datetime,
    money dec(8,2) not null default 5.00,
    unique(ssn1, ssn2)
);

mysql> desc auction_user;
+-------+--------------+------+-----+---------+----------------+
| Field | Type         | Null | Key | Default | Extra          |
+-------+--------------+------+-----+---------+----------------+
| id    | int(11)      | NO   | PRI | NULL    | auto_increment |
| name  | varchar(32)  | NO   |     | NULL    |                |
| ssn1  | char(6)      | YES  | MUL | NULL    |                |
| ssn2  | char(7)      | YES  |     | NULL    |                |
| last  | datetime     | YES  |     | NULL    |                |
| money | decimal(8,2) | NO   |     | 5.00    |                |
+-------+--------------+------+-----+---------+----------------+
```
