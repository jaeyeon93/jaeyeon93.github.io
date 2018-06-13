---
layout: post
title: 2018-6-14 Build Tools
---

## 빌드란?
-------
- build : 배포가능한 상태로 결과물을 만들어내는 과정
- 빌드배포의 끝판왕은 무중단배포이다.
- 배포 : 빌드한 결과물을 사용자가 사용가능한 상태로 만드는거
- CI툴? 지속적 통합툴? CI툴에서 문제가 생기면 자동으로 메세지나 이메일이 온다. **피드백을 최대한 빨리해결하기 위한 환경을 구축**하는 것이다.
- maven : XML기반으로 빌드, if-else를 사용할 수 없다
- gradle : groovy라는 스크립트로 만든 빌드, 기본적인 구조는 maven에서 가지고왔다.


#### build도구이해

1. dependancy에 대한 이해
2. 빌드명령어, 방법 이해, target이라는 디렉토리에 컴파일 결과들이 있다. 결과는 target디렉토리에 jar파일로 결과물이 나온다. 결론 : 일단 빌드도구 명령어부터 익히자.
3. 빌드를 할때 사람손을 타면 에러가 많이난다. 자동화를 많이 시켜야한다.
4. 빌드와 배포를 할 줄 알아야한다. 그리고 프로젝트 세팅도 할 줄 알아야한다 :)


- @RunWith(SpringRunner.class)
-  @SpringBootTest(webEnvironment WebEnvironment.RANDOM_PORT)
-  위의 2가지 어노테이션을 지정해야 ATDD가 진행이 된다.

- DTO, Domain : 상태데이터를 가진다.
- Domain은 핵심적인 비즈니스 로직들을 가지고있고, OOP적인 요소들이 많다.
- WebLayer, Service Layer, Repository Layer의 경우 상태데이터를 가지지 않는다.
- Service들이 Domain, DTO에는 접근이 되지만, 역으로는 안된다.
- Repository Layyer : DAO의 역할, 외부서비스와의 연동 ex) JSON API
- Web Layer : 요청왔을때 최초의 반응을 한다. 유효성 체크 등
- DTO : Domain의 상태가 비즈니스로직에 의해 변경되지 않도록 Freezing시키는것. 최종 결과물을 저장해서 View에 전달.
- Service Layer : 조율, 중재자. Repository에 대한 정보를 가지고 와서 Domain에게 위임, 트랜젝션의 역할 수행. 상대적으로 가벼운 역할을 수행.


#### DTO vs VO의 차이

##### DTO : mutable Object, 인스턴스가 만들어져도 상태가 변경가능. setter메소드가 있기때문에. DTO는 setter와 getter를 둘 다 가지고 있다.

##### VO(Value Object) : Immutable Object -> value와 varibale의 차이점. value는 불변이고, variable은 변경가능한 변수다. 한번 인스턴스가 생성이 되면, 변경이 불가능하다.

```
class A {
	private List\<Integer> numbers;

    public List\<Integer> getNumbers() {
    	return numbers;
    }
}
```
- 위의 코드는 mutable이다.
