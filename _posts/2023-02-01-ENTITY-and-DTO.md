---
title:  "Entity, DTO"
excerpt: "Spring boot Entity,DTO 공부"

categories: 
- Blog
tags: 
 - SpringBoot
 - Entity
 - DTO

toc: false
toc_sticky: true
 
date: 2023-02-01
---

지인이 entity와 dto를 쓰는 이유를 물었을때, 정확하게 답변을 할수가 없었다.


>'실무에서 지난 프로젝트때 그렇게 만들었으니까', '선임들 코드가 그랬으니까'

 라는 생각을 가지고  entity와 dto를 사용해왔던 나를 반성하며 이 두가지를 정리해보려고 한다.
 velog에서 작성하는 첫번째 글이기도 하다.
 
 
## Entity
 `Entity` 클래스는 `DB`와 맞닿는 핵심 클래스이다. 이 Entity 클래스를 기준으로 테이블이 생성된다. Entity의 각 필드는 DB에서 하나의 Column이 된다. 
Entity 생성 시 Getter는 만들지만 Setter를 만들지는 않는다. Setter로 인해 Entity의 인스턴스 값들의 변화를 명확히 알 수 없게되기 때문이다.
Entity에 값을 넣어주기 위해서는 Setter가 아닌 `Builder` 패턴을 사용한다. Builder를 이용하면 필요한 값만 집어넣을 수 있다.


~~~java
@Entity
@Getter
@NoArgsConstructor(access = AccessLevel.PROTECTED)
//접근권한을 PROTECTED로 지정해서 외부에서 객체 생성을 막는다.
public class BoardEntity {
	@Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
	private Long id;
    //strategy = GenerationType.IDENTITY으로 인해 id값이 null이면 auto_increment
    
    private String title;
    private String content;

    @Builder
    public BoardEntity(Long id, String title, String content){
        this.id = id;
        this.title = title;
        this.content = content;
    }
}

//builder를 이용해 값 넣기
BoardEntity boardEntity = BoardEntity.builder()
                        .title("제목")
                        .content("내용")
                        .build();
//이런식으로 필요한 내용을 넣어준다.
~~~

## DTO
`DTO (Data Transfer Object)`는 계층간 데이터 교환에 사용된다. 특별한 로직을 가지지 않고 순수한 데이터 객체상태를 유지한다. DTO를 이용해 Entity 클래스를 보호하면서 Entity 클래스에서 필요한 데이터만 선택적으로 담아 사용한다. 코드만 보면 Entity랑 거의 비슷하게 생겼다.

```java
@Getter
@NoArgsConstructor(access = AccessLevel.PROTECTED)
public class BoardDTO {

	private Long id;
    private String title;
    private String content;

	//toEntity를 이용해서 DTO -> Entity로 변환 이때 builder 쓰면 편하다.
    public static BoardEntity toEntity() {
        return BoardEntity.builder()
                .title(this.title)
                .content(this.content)
                .build();
    }

}
```




>참고
- https://velog.io/@ohzzi/Entity-DAO-DTO%EA%B0%80-%EB%AC%B4%EC%97%87%EC%9D%B4%EB%A9%B0-%EC%99%9C-%EC%82%AC%EC%9A%A9%ED%95%A0%EA%B9%8C
- https://velog.io/@jsb100800/Spring-boot-DTO-Entity-%EA%B0%84-%EB%B3%80%ED%99%98-%EC%96%B4%EB%8A%90-Layer%EC%97%90%EC%84%9C-%ED%95%98%EB%8A%94%EA%B2%8C-%EC%A2%8B%EC%9D%84%EA%B9%8C
- https://startces.tistory.com/m/55
