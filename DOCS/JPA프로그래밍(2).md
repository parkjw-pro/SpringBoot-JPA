# JPA프로그래밍(2)



##### JPA-> 패러다임의 불일치를 해결해준다.

<BR>

하이버네이트 -> ORM프레임워크



JPA는 표준명세이다.

- JPA는 인터페이스의 모음
- JPA 2.1표준 명세를 구현한 3가지 구현체
- 하이버네이트, EclipseLink, DataNucleus



#### 생산성 - JPA와 CRUD

- 저장: jpa.persist(member)
- 조회: Member member = jpa.find(memberId) 
- 수정: member.setName(“변경할 이름”) 
- 삭제: jpa.remove(member)



#### JPA가 좋은이유 ? -> 유지보수 측면

멤버의 속성에서 연락처가 추가될 경우,

기존의 방법에서는 SQL을 전부 수정했어야 되었는데

JPA를 사용하면 DB에 컬럼이 추가되어있다는 가정하여 

member 클래스에 연락처만 추가하면 된다.



#### JPA -> 패러다임의 불일치 해결 

1. JPA와 상속 
2. JPA와 연관관계 
3. JPA와 객체 그래프 탐색 
4. JPA와 비교하기





#### JPA와 상속 -저장



ITEM 안에 ALBUM이 있다고 가정하에, 



##### 개발자가 할일

```
jpa.persist(album);
```

##### 나머진 JPA가 처리

```
INSERT INTO ITEM ...
INSERT INTO ALBUM ...
```



#### JPA와 상속 -조회



##### 개발자가 할일

```
Album album = jpa.find(Album.class, albumId);
```

##### 나머진 JPA가 처리

```
SELECT I.*, A.*
FROM ITEM I
JOIN ALBUM A ON I.ITEM_ID = A.ITEM_ID
```







#### JPA와 연관관계, 객체 그래프 탐색



##### 연관관계 저장

```
member.setTeam(team);
jpa.persist(member);
```



##### 객체 그래프 탐색

```
Member member = jpa.find(Member.class, memberId);
Team team = member.getTeam();
```





### JPA의 성능 최적화 기능 

1. ###### 1차 캐시와 동일성(identity) 보장 

2. ###### 트랜잭션을 지원하는 쓰기 지연(transactional write-behind)

3. ###### 지연 로딩(Lazy Loading) 

   

   

   ##### 1차 캐시와 동일성(identity) 보장 

   1. 같은 트랜잭션 안에서는 같은 엔티티 반환 - 약간의 조회성능 향상
   2.  DB Isolation Level이 Read Commit이어도 애플리케이션에서 Repeatable Read 보장

   

   SQL 1번만 실행

   ```
   String memberId = "100";
   Member m1 = jpa.find(Member.class, memberId); //SQL
   Member m2 = jpa.find(Member.class, memberId); //캐시
   println(m1 == m2) //true
   ```

   

   

   ##### 트랜잭션을 지원하는 쓰기 지연(transactional write-behind)

   1.  트랜잭션을 커밋할 때까지 INSERT SQL을 모음
   2.  JDBC BATCH SQL 기능을 사용해서 한번에 SQL 전송

   

   모아서 보내기

   ```
   transaction.begin(); // [트랜잭션] 시작
   em.persist(memberA);
   em.persist(memberB);
   em.persist(memberC);
   //여기까지 INSERT SQL을 데이터베이스에 보내지 않는다.
   //커밋하는 순간 데이터베이스에 INSERT SQL을 모아서 보낸다.
   transaction.commit(); // [트랜잭션] 커밋
   ```

   

   ##### 지연 로딩과 즉시 로딩

   -  지연 로딩: 객체가 실제 사용될 때 로딩
   -  즉시 로딩: JOIN SQL로 한번에 연관된 객체까지 미리 조회

   

   ##### 지연 로딩

   ```
   Member member = memberDAO.find(memberId);
   Team team = member.getTeam();
   String teamName = team.getName();
   ```

   SELECT * FROM MEMBER 

   SELECT * FROM TEAM

   

   

   ##### 즉시 로딩

   ```
   Member member = memberDAO.find(memberId);
   Team team = member.getTeam();
   String teamName = team.getName();
   ```

   SELECT M.*, T.* 

    FROM MEMBER

    JOIN TEAM …





### ORM은 객체와 RDB 두 기둥위에 있는 기술이다.







참고한 강의 :  자바 ORM 표준 JPA 프로그래밍 - 기본편(김영한)
