웹서비스를 개발하려면 데이터베이스를 다루는 것을 피할수 없습니다. 
그렇지만 **데이터를 어떻게 저장할지** 초점을 맞추는 관계형 데이터베이스와 
**기능과 속성을 한 곳에서 관리**하는 객체지향프로그래밍 언어를 함께 사용하려고 하면 이 둘의 사상이 너무 다르기 때문에 패러다임에 불일치가 생깁니다. 

이런 패러다임의 불일치를 해결하기위한 기술이 **JPA**입니다. 

JPA를 통해 개발자는 객체지향적으로 프로그래밍을하면 **JPA가 관계형 데이터베이스에 맞게 대신 SQL을 생성**해줍니다. 
즉 개발자는 더이상 **SQL에 종속적으로 프로그래밍을 하지 않아도 됩니다. **

### Spring Data JPA
인터페이스인 JPA를 사용하기 위해서는 구현체가 필요합니다. 
구현체들을 추상화시켜 사용하기 쉽게 만든 것이 Spring Data JPA 모듈입니다. 

Spring Data JPA를 쓰는 것과 Hibernate를 사용하는 것은 큰 차이가 없음에도 Spring Data JPA를 사용하는 이유는 다음과 같습니다. 
- **구현체 교체의 용이성** : Hibernate가 수명을 다해 새로은 JPA구현체가 떠오르면 Spring Data JPA를 사용하고 있는 경우 쉽게 교체를 할 수 있습니다. 
- **저장소 교체의 용이성** : 다른 관계형 데이터베이스로의 교체가 필요한 경우 의존성만 교체를 하면 됩니다. _기본적인 CRUD의 인터페이스가 같기 때문에_

### JPA 적용하기
build.gradle에 다음의 의존성을 추가해야합니다. 
![](https://images.velog.io/images/nnoshel/post/d09d1798-ef47-41cf-8cae-c976b391da1f/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202022-01-30%20%EC%98%A4%ED%9B%84%208.58.01.png)
> - **spring-boot-starter-data-jpa**
	스프링부트용 Spring Data JPA 추상화 라이브러리
- ** h2**
 인메모리 관계형 데이터베이스
 프로젝트에 의존성 추가만으로 관리가능
 메모리로 실행되므로 재시작할때마다 초기화 -> 테스트 용으로 많이 사용

### **Entity 클래스** 생성
이 클래스는 DB와 매칭될 클래스로 ** @Entity 어노테이션**을 사용해야합니다. 

domain 패키지 생성하고, 그 아래에 posts패키지와 Posts 클래스를 생성했습니다. 

_주요 어노테이션은 주요 어노테이션을 클래스 명과 가까이 배치하는 것이 좋습니다. 
코드를 단순화 시켜주는 롬복 어노테이션보다 @Getter, @NoArgsConstructor JPA 어노테이션인 @Entity 어노테이션을 클래스 명과 가까운 위치에 배치했습니다. _

```
@Getter // Getter 메소드 생성
@NoArgsConstructor // 기본생성자 추가
@Entity // 테이블과 링크 될 클래스 임을 나타냄
public class Posts {
    @Id // 해당 테이블의  PK 입니다. 
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    // PK생성규칙입니다. GenerationType.IDENTITY옵션을 추가해야 자동 증가를 사용할 수 있습니다. 
    private Long id;
    
    @Column(length = 500, nullable = false) // 테이블의 컬럼을 나타냅니다. 
    private String title;

    @Column(columnDefinition = "TEXT", nullable = false)
    private String content;

    private String author;

    @Builder // 클래스의 빌더패턴 클래스 생성 , 생성자 상단에 선언하면 생성자에 포함된 필드 (title, content, author)만 빌더에포함
    public Posts(String title, String content, String author) {
        this.title = title;
        this.content = content;
        this.author = author;
    }
}
```
이 클래스에는 setter 메소드가 없습니다. 
setter가 있는 경우 해당 클래스의 인스턴스 값들이 어제 어디서 변해야하는지 코드상으로 정확히 알 수 없기때문에** Entity클래스에서는 setter를 만들지 않아야합니다. **

만약 필드의 값 변경이 필요한 경우 그 기능만을 수행할 수 있는 메소드를 추가해야합니다. 
예를 들어 주문 취소메소드가 필요한 경우입니다. setter를 사용해 인스턴스의 값을 바로 변경했습니다. 
```
public Class Order{
	public void setStatus(boolean s){
     	this.status = status;
    }
}

public void 주문취소이벤트(){
	order.setStatus(false);
}
```
이 코드를 다음처럼 변경해 사용해야 합니다. 
```
public Class Order{
	public void cancleOrder(){
     	this.status = false;
    } 
}
```
주문을 취소하는 메소드를 만듭니다. 이 메소드는 상태를 false로 변경해주는 역할만 합니다. 
그리고 취소해야하는 경우 cancleOrder를 호출합니다. 
```
public void 주문취소이벤트(){
	order.cancleOrder();
}
```

JPA는 기본적으로 생성자를 통해 값을 채운 후 DB에 insert합니다. 
값 변경이 필요한 경우엔 위의 예시처럼 해당 이벤트에 맞는 메소드를 호출하여 값을 변경합니다. 그래서 setter가 없어도 됩니다. 

@Builder를 통해 생성시점에 어느 필드에 어느 값을 채워줄 수 있는지 명확하게 인지할 수 있습니다. 

### **JpaRepository 인터페이스** 생성 
DB로 접근하기위해 생성한 인터페이스입니다.

```
import org.springframework.data.jpa.repository.JpaRepository;

public interface PostRepository extends JpaRepository<Posts, Long> {

}
```
JpaRepository<엔티티 클래스, PK 타입> 을 상속하면 자동적으로 기본 CRUD가 생성됩니다. 
> Entity 클래스와 기본 Entity Repository는 함께 위치해있어야합니다. 

### Spring Data JPA 테스트 코드 작성하기

save와 findAll 기능을 테스트해보겠습니다. 
```
import org.junit.After;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.test.context.junit4.SpringRunner;

import java.util.List;

import static org.assertj.core.api.Assertions.assertThat;

@RunWith(SpringRunner.class)
@SpringBootTest
public class PostRepositoryTest {

    @Autowired
    PostRepository postRepository;

    @After // 단위테스트가 끝날때마다 수행되는 메소드, 데이터 간 데이터침범을 막기위해사용
    public void cleanUp(){
        postRepository.deleteAll();
    }

    @Test
    public void 게시글_저장불러오기(){
        //given
        String title = "테스트 제목";
        String content = "테스트 내용";

        postRepository.save(Posts.builder() //post에 insert,update쿼리를 실행 (id가 있으면 update, 없으면 insert)
                                .title(title)
                                .content(content)
                                .author("me")
                                .build());
        // when
        List<Posts> postsList = postRepository.findAll(); // 테이블의 모든 데이터 조회

        //then
        Posts posts = postsList.get(0);
        assertThat(posts.getTitle()).isEqualTo(title);
        assertThat(posts.getContent()).isEqualTo(content);

    }
}
```
![](https://images.velog.io/images/nnoshel/post/d73c2495-9ba1-4f08-a0eb-d77a5a68e8a6/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202022-01-30%20%EC%98%A4%ED%9B%84%209.56.06.png)
위와 같이 테스트를 통과했음을 알 수 있습니다. 

### 로그에 쿼리 출력하기
jpa를 통해 직접 쿼리를 작성하지 않고도 데이터베이스를 수정 조회를 할 수 있었습니다. 
그럼 jpa가 자동으로 어떤 쿼리를 만들었는지 확인해보도록 하겠습니다. 
/src/main/resources 아래에 프로퍼티를 생성하겠습니다. 
application.propertise 파일을 만들고 다음과 같이 작성하겠습니다. 
```spring.jpa.show_sql=true```
그럼 다음과 같이 콘솔에서 쿼리를 확인 할 수 있습니다. 
![](https://images.velog.io/images/nnoshel/post/94b2e974-c63d-4574-9558-368253531d79/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202022-01-30%20%EC%98%A4%ED%9B%84%2010.01.54.png)
