보통 엔티티는 해당 데이터의 생성 시간과 수정시간을 포합합니다. 
이 정보들은 차후 유지보수에 있어 굉장히 중요하기 때문입니다. 

하지만 매번 데이터를 삽입하거나 갱신할 때 날짜데이터를 등록/수정하려는 코드를 사용하게되면 매우 번거롭기때문에 JPA Auditing을 사용해보겠습니다. 

domain 패키지 아래에 다음과 같은 추상 클래스를 만들겠습니다. 
```
@Getter
@MappedSuperclass // Entity클래스들이 이 클래스를 상속할 경우 필드(createdDate,modifiedDate)들도 컬럼으로 인식
@EntityListeners(AuditingEntityListener.class) // 이 클래스에 Auditing 기능을 포함
public abstract class BaseTimeEntity {
    
    @CreatedDate // 생성시간 저장
    private LocalDateTime createdDate;

    @LastModifiedDate // 수정시간 저장
    private LocalDateTime modifiedDate;
}

```
이 클래스는 모든 Entity의 상위클래스가 되어 @CreatedDate, @LastModifiedDate를 통해 엔티티의 생성시간과, 수정시간을 자동으로 관리하게 됩니다. 

그리고 위 클래스를 엔티티 클래스가 상속받아 사용하게합니다. 
```
	...
  @Entity
  public class Posts extends BaseTimeEntity{
	...
}
```

마지막으로 JPA Auditing 어노테이션을 모두 활성화 할 수 있도록 메인클래스에 어노테이션을 추가해야합니다. 
```
@EnableJpaAuditing // JPA Auditing 활성화
@SpringBootApplication
public class Application {
    public static void main(String[] args){
        SpringApplication.run(Application.class, args);
    }
}
```

>LocalDate와 LocalDateTime은 Java8 이전의 Data와 Calender의 문제를 개선해서 나온 클래스입니다. <br>
Date와 Calender의 문제점은 다음과 같습니다. 
	>
    1. 불변객체가아님 
    	멀티스레드환경에서 문제가 발생 할 수 있습니다. 
    2. Calender Month의 문제 
    	10월을 나타내는 Calender.OCTOBER의 숫자값은 9로, 
        월 값과 숫자값이 달라 개발에 혼란을 줄 수 있습니다. 


### JPA Auditing 테스트
다음과 같이 테스트를 진행해보겠습니다. 
```
@Test
    public void BaseTimeEntity_등록() {
        // given
        LocalDateTime now = LocalDateTime.of(2022,1,4,0,0,0,0);
        postRepository.save(Posts.builder()
                .title("BaseTimeEntity title")
                .content("BaseTimeEntity content")
                .author("BaseTimeEntity author")
                .build());

        //when
        List<Posts> postsList = postRepository.findAll();

        //then
        Posts posts = postsList.get(0);

        System.out.println(">>>>> createDate = "+posts.getCreatedDate()+", modifiedDate = "+posts.getModifiedDate());

        assertThat(posts.getCreatedDate()).isAfter(now);
        assertThat(posts.getModifiedDate()).isAfter(now);

    }
```
테스트가 통과 한 것을 알수있습니다. 
 ![](https://images.velog.io/images/nnoshel/post/64eb9117-17e2-4dc3-86a5-7dad2468bbef/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202022-01-31%20%EC%98%A4%ED%9B%84%206.37.37.png)
 
