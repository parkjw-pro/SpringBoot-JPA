# JPA프로그래밍(1)



###### 실무에서는

- 몇십개의 테이블과 객체가 얽혀서 돌아가게 된다.
- 이것들을 잘 설계하고, 매핑을 정확하게 하는 것이 중요하다.
- JPA를 사용할 때의 장점
- 1. 개발생산성
  2. 유지보수 & 속도



###### 객체를 영구 보관하는 다양한 저장소

RDB, NOSQL, FILE 등등..



###### 객체를 관계형 DB에 저장

객체 -> SQL(개발자) -> RDB



객체의 상속관계와 그나마 유사한건

Table 슈퍼타입, 서브타입 관계



###### 연관관계

객체는 참조를 사용 : member.getTeam()

테이블을 외래 키를 사용: JOIN ON M.TEAM_ID = T.TEAM_ID 



##### 그동안 mybatis를 쓰면서, 테이블 구조와 비슷하게

##### DTO를 만들었는데 이 과정이 객체지향답지 못하다고 계속 생각했었다



###### 자바 컬렉션에 넣는다고 생각하면?

```
list.add(member);
Member memeber = list.get(memberId);
Team team = member.getTeam();
```



###### 엔티티 신뢰문제

```
class MemberService {
 ...
 public void process() {
 Member member = memberDAO.find(memberId);
 member.getTeam(); //???
 member.getOrder().getDelivery(); // ???
 }
}
```

개발자가 직접 코드를 까봐야 논리문제를 해결 할 수 있다



##### 비교해보기

```
String memberId = "100";
Member member1 = memberDAO.getMember(memberId);
Member member2 = memberDAO.getMember(memberId);
member1 == member2; //다르다.
class MemberDAO {
 
 public Member getMember(String memberId) {
 String sql = "SELECT * FROM MEMBER WHERE MEMBER_ID = ?";
 ...
 //JDBC API, SQL 실행
 return new Member(...);
 }
}
```



##### 비교하기 - 자바컬렉션 조회

```
String memberId = "100";
Member member1 = list.get(memberId);
Member member2 = list.get(memberId);
member1 == member2; //같다.
```

자바 컬렉셔에서 객체를 다룰 때랑, sql에서 다룰 때랑 다르다.'



> ##### 객체답게 모델링 할수록 매핑 작업만 늘어난다.



객체를 자바 컬렉션에 저장 하듯이, DB에 저장 할 순 없을까??


###### -> JPA (Java Persistence API)







강의 : 자바 ORM 표준 JPA 프로그래밍 - 기본편(김영한)
