# JPA프로그래밍(3)

<br/>

### 실습

- DB는 h2database 사용
- Maven으로 설정(pom.xml로 사용 할 라이브러리  정의)
- Java는 버전 8이상

<br/>

<br/>

##### pom.xml의 dependencies에 아래의 설정을 추가해준다.

<br/>

```
 <dependency> 
 <groupId>org.hibernate</groupId> 
 <artifactId>hibernate-entitymanager</artifactId> 
 <version>5.3.10.Final</version> 
 </dependency> 
 <!-- H2 데이터베이스 --> 
 <dependency> 
 <groupId>com.h2database</groupId> 
 <artifactId>h2</artifactId> 
 <version>1.4.200</version> 
 </dependency> 
```

<br/>

하이버네이트는 5.3.10 버전을 사용하고

H2는 1.4.200사용한다는 의미.

<br/>

##### 스프링 부트 릴리즈마다 추천? 해주는 각각 라이브러리의 버전이 있으니 확인해볼 것!

<br/>

resources 안에 META-INF폴더를 만들고 그 안에 persistence.xml을 만들어준다.

<br/>

```
<?xml version="1.0" encoding="UTF-8"?> 
<persistence version="2.2" 
 xmlns="http://xmlns.jcp.org/xml/ns/persistence" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
 xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/persistence http://xmlns.jcp.org/xml/ns/persistence/persistence_2_2.xsd"> 
 <persistence-unit name="hello"> 
 <properties> 
 <!-- 필수 속성 --> 
 <property name="javax.persistence.jdbc.driver" value="org.h2.Driver"/> 
 <property name="javax.persistence.jdbc.user" value="sa"/> 
 <property name="javax.persistence.jdbc.password" value=""/> 
 <property name="javax.persistence.jdbc.url" value="jdbc:h2:tcp://localhost/~/test"/> 
 <property name="hibernate.dialect" value="org.hibernate.dialect.H2Dialect"/> 
 
 <!-- 옵션 --> 
 <property name="hibernate.show_sql" value="true"/> 
 <property name="hibernate.format_sql" value="true"/> 
 <property name="hibernate.use_sql_comments" value="true"/> 
 <!--<property name="hibernate.hbm2ddl.auto" value="create" />--> 
 </properties> 
 </persistence-unit> 
</persistence> 
```

<br/>

###### 내용을 설명해보면

 hello라는 name을 쓰는 persistence-unit이 있고,

그것들의 계정명, 비밀번호와 사용하는 DB종류는 어떤것이고, 서버 위치는 어디인지를 담고있다.

<br/>

### 데이터베이스 방언

- JPA는 특정 데이터베이스에 종속 X
- 각각의 데이터베이스가 제공하는 SQL 문법과 함수는 조금씩 다름
- 방언: SQL 표준을 지키지 않는 특정 데이터베이스만의 고유한 기능

<br/>

### hibernate.dialect 속성에 지정

- H2 : org.hibernate.dialect.H2Dialect
- Oracle 10g : org.hibernate.dialect.Oracle10gDialect
- MySQL : org.hibernate.dialect.MySQL5InnoDBDialect
- 하이버네이트는 40가지 이상의 데이터베이스 방언 지원

<br/>

<br/>

### JPA를 사용함에 앞서 주의 해야 할점

- 엔티티 매니저 팩토리는 하나만 생성해서 애플리케이션 전체에서 공유(was의 생명주기를 따라간다는 느낌)
- 엔티티 매니저는 쓰레드간에 공유X (사용하고 버려야 한다). 
- JPA의 모든 데이터 변경은 트랜잭션 안에서 실행

<br/>

<br/>

<br/>

## 실습해보기

<br/>

<br/>

<br/>

##### EntityManagerFactory 생성하기

<br/>

```
EntityManagerFactory emf = Persistence.createEntityManagerFactory("hello");
```

<br/>

<br/>

<br/>

##### EntityManager 생성하기

<br/>

```
EntityManager em =  emf.createEntityManager();
```

<br/>

<br/>

<br/>

##### 트랜잭션 생성하기

<br/>

```
EntityTransaction tx =  em.getTransaction();
```

<br/>

<br/>

##### 트랜잭션은 유저의 특정 행동이나 동작 단위로 생성하고 삭제한다.

<br/>

```
tx.begin();
```

으로 생성해주고,

<br/>

##### 성공적으로 트랜잭션이 종료되면 tx.commit();을 통해 DB에 영구적으로 저장하고

##### 중간에 에러가 발생하면 tx.rollback();을 통해 원자성을 유지해준다.

<br/>

<br/>

##### 데이터 삽입

<br/>

```
		try {
			Member member = new Member();
			
			//객체에 내용 담아주고
			member.setId(3L);
			member.setName("helloC");
			
			em.persist(member);
			
			tx.commit();
		} catch (Exception e) {
			tx.rollback();
		} finally {
			em.close();
		}
```

<br/>

<br/>

<br/>

##### 데이터 검색 및 수정

<br/>

```
	try {
		Member findMeber = em.find(Member.class, 1L);
		findMeber.setName("HelloJPA");
		//삭제
		//em.remove(findMeber);
		tx.commit();
	} catch (Exception e) {
		tx.rollback();
	} finally {
		em.close();
	}
```

<br/>

<br/>

<br/>

##### 쿼리문을 직접 작성해서 쏴보기

```
	try {
		List<Member> result = em.createQuery("select m from Member as m", Member.class)
				.setFirstResult(5)//페이징
				.setMaxResults(8)
				.getResultList();
		
		for (Member member : result) {
			System.out.println(member.getName());
		}
		tx.commit();
	} catch (Exception e) {
		tx.rollback();
	} finally {
		em.close();
	}
```

<br/>

<br/>

<br/>

<br/>

<br/>

참고한 강의 :  자바 ORM 표준 JPA 프로그래밍 - 기본편(김영한)

<br/>

<br/>