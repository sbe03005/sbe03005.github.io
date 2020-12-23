---
layout: post
title: JPA JPQL Named Query
subheading: JPA JPQL Named Query
author: sbe03005
categories: JPA
banner: https://bit.ly/32PAjtM
tags: JPA JPQL NamedQuery
sidebar: []
---

# Named 쿼리



Named 쿼리는 미리 정의해두고 이름을 부여해 사용하는 JPQL 문법이다. 

동적 쿼리에는 사용할 수 없으며, 정적 쿼리에만 사용할 수 있다. 

엔티티에 어노테이션으로 지정하거나 xml에 정의해둘 수 있다.

이게 장점이 뭐냐면 .. 애플리케이션 로딩 시점에 쿼리를 검증해서 미리 오류를 잡을 수 있다는 점이다. 



## 1. 어노테이션 방식

```java
@Entity
@NamedQuery(
        name = "Member.findByUsername",
        query = "select m from Member m where m.username = :username "
)
public class Member {
    @Id
    @GeneratedValue
    private Long id;
    private String username;
    private int age;

    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "TEAM_ID")
    private Team team;

    @Enumerated(EnumType.STRING)
    private MemberType type;
}
```

Member 엔티티 위에 적용한 것을 보면 name과 query를 지정해둔걸 볼 수 있다.

사용할 때는 아래와 같이 사용하면 된다.

````java
List<Member> result =
		em.createNamedQuery("Member.findByUsername", Member.class)
				.setParameter("username", "memberA")
				.getResultList();
````

<br/>

<br/>

## 2. Xml 방식

**[META-INF/persistence.xml]**

```xml
<persistence-unit name="jpabook" >
<mapping-file>META-INF/ormMember.xml</mapping-file>
```

**[META-INF/ormMember.xml]**

```java
<?xml version="1.0" encoding="UTF-8"?>
<entity-mappings xmlns="http://xmlns.jcp.org/xml/ns/persistence/orm" version="2.1">
    <named-query name="Member.findByUsername">
        <query><![CDATA[
        select m
        from Member m
        where m.username = :username
        ]]></query>
    </named-query>
  
    <named-query name="Member.count">
        <query>select count(m) from Member m</query>
    </named-query>
</entity-mappings>
```

<br/>

<br/>

## 3. 그 외

1. xml방식이 항상 먼저 실행된다.
2. 애플리케이션 운영 환경에 따라서 다른 xml을 배포할 수 있게 된다.

