데이터모델링
===

#### 현실세계에서 응용에 필요한 데이터를 잘 고르고 다듬어서 DBMS에 저장하는 방법

![](https://github.com/jaeyeon93/jaeyeon93.github.io/blob/master/images/database/modeling/db-modeling-result.png?raw=true)

- 요구사항을 먼저 분석
- ER Diagram을 이용해서 개념적설계를 표현한다. (위로 갈수록 추상적, 밑으로 갈수록 구체적)
- ERD -> 관계스키마 -> SQL가는 것은 쉽다.

#### 데이터 모델링 과정
1. 요구사항분석(Requirements Analysis)
2. 개념적설계(Conceptual Database Design) : ERD
3. 논리적설계(Logical Database Design) : Realtional data model
4. 물리적설계(Physical Database Design) : SQL

#### 첫번째 : 요구사항 분석

- 앱의 요구사항을 데이터의 관점에서 바라본다.
- 사용자 / 앱이 데이터베이스에 요구하는것은 무엇인가
- 무엇을 어떻게 저장할것인가?

![](https://github.com/jaeyeon93/jaeyeon93.github.io/blob/master/images/database/modeling/db-requirements.png?raw=true)

![](https://github.com/jaeyeon93/jaeyeon93.github.io/blob/master/images/database/modeling/db-requirements2.png?raw=true)

![](https://github.com/jaeyeon93/jaeyeon93.github.io/blob/master/images/database/modeling/db-requirements3.png?raw=true)

![](https://github.com/jaeyeon93/jaeyeon93.github.io/blob/master/images/database/modeling/db-requirements4.png?raw=true)

![](https://github.com/jaeyeon93/jaeyeon93.github.io/blob/master/images/database/modeling/db-conceptual.png?raw=true)
- 개념적설계는 요구사항분석의 다음 단계이다. 출력으로 Entiry-Relationship Diagram)을 사용한다.

#### 객체와 속성(Entity and Attribute)

개체
- 실제현실에서 독립적으로 존재하는 어떤 것
- 직사각형으로 표현

속성
- 개체를 설명할 수 있는 특성
- 타원으로표현
- 키 속성 : 개체마다 고유한 값을 가지는 속성, 밑줄로 표현하며 하나 이상 존재 가능

![](https://github.com/jaeyeon93/jaeyeon93.github.io/blob/master/images/database/modeling/db-relationship.png?raw=true)
![](https://github.com/jaeyeon93/jaeyeon93.github.io/blob/master/images/database/modeling/db-weekEntity.png?raw=true)

### 실습 : Relationship표현해보기
Entity를 그렸던 곳에다 Relationship도 추가해보자.

![](https://github.com/jaeyeon93/jaeyeon93.github.io/blob/master/images/database/modeling/db-modeling-result.png?raw=true)

### 이제는 ERD -> RDBMS형태로 바꾼다. 그래야 SQL로 바꾸기 쉽다.

## 세번째 : 논리적 설계 ERD -> RDBMS
- ERD의 결과를 직접 SQL로 바꾸기는 어렵다.
- 관계 데이터 모델로 변경한다.
- 입력 : ERD
- 출력 : 관계데이터모델(논리스키마)

### 간단한 규칙 1번 : 개체(Entity) -> 테이블

- 기본적으로 ERD의 개체는 R모델에서 테이블(릴레이션)이 됩니다.

```
사원(ID, ...)
부서(ID, ...)
가족(이름 ...)
프로젝트(PID,....)
```

![](/Users/jaeyeonkim/Desktop/blog/images/database/modeling/db-oneToOne.png)

![](/Users/jaeyeonkim/Desktop/blog/images/database/modeling/db-foreignkey.png)
- 외래키는 관계 표현에 사용이 된다.
- 관계를 맺는 테이블에 외래키를 추가
- 관계의 속성도 테이블의 속성으로 추가한다.

![](/Users/jaeyeonkim/Desktop/blog/images/database/modeling/db-constraint.png)
