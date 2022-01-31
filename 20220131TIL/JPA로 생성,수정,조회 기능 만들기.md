이전 포스팅에서 도메인 모델의 가지는 역할을 알아보았습니다. 
이 도메인 모델을 기억하고 crud를 해보겠습니다. 
### 등록
**PostsApiController**
```
@RequiredArgsConstructor // 생성자 생성
@RestController
public class PostsApiController {

    private final PostsService postsService;

    @PostMapping("/api/v1/posts")
    public Long save(@RequestBody PostsSaveRequestDto requestDto){
        return postsService.save(requestDto);
    }
}
```
```@Controller```와 ```@RestController```는 모두 컨트롤러를 지정해줍니다. 

다만 차이점은 ```@RestController```는 ```@Controller```에 ```@ResponseBody```가 추가된 것으로 ```@ResponseBody```를 하위 메소드마다 따로 붙이지 않아도 JSon형태로 객체를 전송할 수 있습니다. 


**PostsService**
```@RequiredArgsConstructor
@Service
public class PostsService {
    private final PostsRepository postsRepository;

    @Transactional
    public Long save (PostsSaveRequestDto requestDto){
        return postsRepository.save(requestDto.toEntity()).getId();
    }
}
```
controller와 service에서는 Bean주입을 ```Autowired```로 받지 않고 생성자로 주입을 받았습니다. 이와 관련된 포스팅은 [생성자주입을 사용하는 이유](https://velog.io/@nnoshel/생성자-주입을-사용하는-이유)에서 자세히 볼 수 있습니다. 

생성자는 롬복의 ```@RequiredArgsConstructor```를 통해 final이 선언된 모든 필드에 대해 생성자를 생성해줍니다. 

**PostsSaveRequestDto**
```
@Getter @NoArgsConstructor
public class PostsSaveRequestDto {
    private String title;
    private String content;
    private String author;

    @Builder
    public PostsSaveRequestDto(String title, String content, String author) {
        this.title = title;
        this.content = content;
        this.author = author;
    }

    public Posts toEntity() {
        return Posts.builder()
                .title(title)
                .content(content)
                .author(author)
                .build();
    }

}
```
위 Dto는 Entity클래스와 굉장히 유사합니다. 
하지만 Entity클래스와 유사하다고 **Entity클래스를 Response/Request로 함께 사용하면 안됩니다. **

Entity클래스는 데이터베이스와 굉장히 밀접한 클래스이기 때문에 Entity클래스를 기반으로 데이터베이스 테이블이 생성되고 스키마가 변경됩니다. 

즉 Entity클래스를 기준으로 여러 비즈니스 로직들이 작동을 하는데, 
Response/Request용 Dto는 View를 위한 클래스라 많은 변경이 이루어져야합니다. 
>Entity클래스를 Dto와 함께 사용하면 테이블도 잦은 변경이 이루어지게 될것이며 이는 다른 비즈니스 로직에도 영향을 미칠 수 있습니다. 

그래서 View Layer와 DB Layer는 철저히 분리가 되어야합니다. 

### 등록 테스트
등록 기능을 테스트해보겠습니다. 
```
@RunWith(SpringRunner.class)
@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT) // 랜덤포트실행
public class PostsApiControllerTest {

    @LocalServerPort
    private int port;

    @Autowired
    private TestRestTemplate restTemplate;

    @Autowired
    private PostsRepository postsRepository;

    @After
    public void tearDown() throws Exception {
        postsRepository.deleteAll();
    }

    @Test
    public void Posts가_등록된다() throws Exception {
        //given
        String title = "테스트 제목입니다.";
        String content = "테스트 내용입니다.";
        PostsSaveRequestDto requestDto = PostsSaveRequestDto.builder()
                .content(content)
                .title(title)
                .author("작성자")
                .build();

        String url = "http://localhost:"+ port + "/api/v1/posts";

        //when
        ResponseEntity<Long> responseEntity = restTemplate.postForEntity(url, requestDto, Long.class);

        //then
        assertThat(responseEntity.getStatusCode()).isEqualTo(HttpStatus.OK);
        assertThat(responseEntity.getBody()).isGreaterThan(0L);
        
        List<Posts> all = postsRepository.findAll();
        assertThat(all.get(0).getTitle()).isEqualTo(title);
        assertThat(all.get(0).getContent()).isEqualTo(content);
    }

}
```

**ResponseEntity**는 **요청과 응답**에 해당하는  HttpHeader와 HttpBody를 포함하고 있는 클래스인 **HttpEntity를 상속받아 구현된 클래스**입니다.

따라서 ResponseEntity는 HttpStatus, HttpHeaders, HttpBody를 포함하고 있고, 이 요청은** RestTemplate를 사용해 RESTful원칙을 지켜 HTTP서버와 통신하여** 테스트의 결과를 확인 할 수 있습니다. 

이와 관련한 자세한 내용은 추후 기술하겠습니다.

![](https://images.velog.io/images/nnoshel/post/6904ce05-56f4-4bc7-8028-879eee3c0249/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202022-01-31%20%EC%98%A4%ED%9B%84%204.10.10.png)랜덤 포트 실행가 실행되면서 테스트를 통과했습니다.

### 수정 / 조회
**PostsApiController**
수정은 PUT, 조회는 GET 메소드를 정의해 다음과 같은 코드를 추가했습니다. 
```
@PutMapping("/api/v1/posts/{id}") // 수정
    public Long update(@PathVariable Long id, @RequestBody PostsUpdateRequestDto requestDto){
        return postsService.update(id, requestDto);
    }
    @GetMapping ("/api/v1/posts/{id}") // 조회
    public PostsResponseDto findById (@PathVariable Long id){
        return postsService.findById(id);
    }
```
수정과 조회를 할 때는 id에 맞는 게시글만을 출력할 것이므로 ```@PathVariable```를 사용해 id를 따로 받아왔습니다. 
 
**PostResponseDto**
```
@Getter
public class PostsResponseDto {
    private Long id;
    private String title;
    private String content;
    private String author;

  	public PostsResponseDto(Posts entity) {
        this.id = entity.getId();
        this.title = entity.getTitle();
        this.content = entity.getContent();
        this.author = entity.getAuthor();
    }
}
```
응답을 받을 dto를 만들었습니다. 
여기서 주의할 점은 entity를 받아서 처리했다는 것입니다.

**PostsUpdateRequestDto**
```
@Getter
@NoArgsConstructor
public class PostsUpdateRequestDto {
    private String title;
    private String content;

    @Builder
    public PostsUpdateRequestDto(String title, String content) {
        this.title = title;
        this.content = content;
    }
}
```

**Posts**
```
  public void update (String title, String content){
        this.title = title;
        this.content = content;
    }
  ```
  수정 시 작성자는 수정할 필요가 없기때문에 제목과 내용 필드만을 작성합니다.
  
  **PostService**
 ```
 @Transactional
    public Long update(Long id, PostsUpdateRequestDto requestDto) {
        Posts posts = postsRepository.findById(id).orElseThrow(() -> new IllegalArgumentException("해당 게시글이 없습니다. id =" + id));

        posts.update(requestDto.getTitle(), requestDto.getContent());

        return id;
    }

    public PostsResponseDto findById(Long id) {
        Posts entity = postsRepository.findById(id).orElseThrow(() -> new IllegalArgumentException("해당 게시글이 없습니다. id =" + id));

        return new PostsResponseDto(entity);
    }
```
   update와 findById의 첫째줄이 동일하게 작성되어있습니다. 
   이 라인은 id를 데이터 베이스에서 찾을 수 없을때 예외를 발생시키는 문장입니다. 
   
   또 여기에는 쿼리를 날리는 부분이 없습니다. 
   이게 가능한 이유는 JPA의 영속성 컨테스트 때문입니다. 
   
   #### 영속성 컨텍스트
  ** 엔티티를 영구 저장하는 환경**을 영속성 컨텍스트라고 합니다. 
   JPA의 핵심내용은 엔티티가 영속성 컨텍스트에 포함되어 있냐 아니냐로 갈립니다. 
   
   JPA의 엔티티 매니저가 활성화 된 상태로 **트랜잭션 안에서 데이터베이스의 데이터를 가져오면** 이 데이터는 영속성이 유지된 상태입니다. 
   그리고 **변경된 값은 트랜잭션이 끝나는 시점에 테이블에 반영**됩니다. 
   
   > 즉 엔티티의 값만 변경하면 별도로 update 쿼리를 날릴 필요가 없습니다. 
   
   
### 수정 테스트
   ```
   @Test
    public void Posts가_수정된다() throws Exception {
        //given 수정할 게시글 작성
        Posts savePosts = postsRepository.save(Posts.builder()
                .title("수정테스트 제목")
                .content("수정테스트 내용")
                .author("수정테스트 작성자")
                .build());

        Long updateId = savePosts.getId();// 작성한 게시글에서 아이디 가져오기
        String expectTitle = "새 제목"; // 수정할 제목
        String expectContent = "새 내용"; // 수정할 내용

        PostsUpdateRequestDto requestDto = PostsUpdateRequestDto.builder()
                .title(expectTitle)
                .content(expectContent)
                .build();

        String url = "http://localhost:" + port + "/api/v1/posts/" + updateId;

        HttpEntity<PostsUpdateRequestDto> requestEntity = new HttpEntity<>(requestDto);

        //when
        ResponseEntity<Long> responseEntity = restTemplate.exchange(url, HttpMethod.PUT, requestEntity, Long.class);

        //then
        assertThat(responseEntity.getStatusCode()).isEqualTo(HttpStatus.OK);
        assertThat(responseEntity.getBody()).isGreaterThan(0L);

        List<Posts> all = postsRepository.findAll();
        assertThat(all.get(0).getTitle()).isEqualTo(expectTitle);
        assertThat(all.get(0).getContent()).isEqualTo(expectContent);
    }
  ```
다음과 같이 업데이트가 된 것을 확인 할 수 있습니다. ![](https://images.velog.io/images/nnoshel/post/3d60bde0-d596-4f7c-ac4d-0d38b293e0c9/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202022-01-31%20%EC%98%A4%ED%9B%84%205.22.06.png)

### 조회 테스트
조회는 실제로 톰텟을 실행해서 확인해보도록 하겠습니다. 
H2 웹 콘솔에 직접 접근하려면 프로퍼티에 다음과 같은 옵션을 추가해야합니다. 
```spring.h2.console.enabled=true```
추가한 뒤 메인메소드를 실행하고 localhost:8080/h2-console 로 접속하겠습니다. 

서버가 새로 올라갈때마다 h2 데이터베이스는 새로운 테이블과 스키마를 만듭니다. 
아래와 같이 Post테이블을 확인 할 수 있습니다. 

![](https://images.velog.io/images/nnoshel/post/3048e3f6-45b3-493f-897b-74398d4fc62f/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202022-01-31%20%EC%98%A4%ED%9B%84%205.34.20.png)

방금 서버를 올렸기때문에 등록된 데이터가 없습니다. 

직접 쿼리문을 작성해 데이터를 등록하겠습니다. 
![](https://images.velog.io/images/nnoshel/post/6d6974aa-6a1c-4fa5-a22f-a9f17ab95fad/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202022-01-31%20%EC%98%A4%ED%9B%84%205.42.19.png)

위와 같이 등록된 데이터를 브러우저에서 ```http://localhost:8080/api/v1/posts/1``` 을 입력해 조회 기능이 작동하는 지 테스트하겠습니다. 
_여기서 1은 PathVariable에 속하는 id 입니다._
![](https://images.velog.io/images/nnoshel/post/1f504aa9-2bd0-443d-bccb-95cb01ab8b6f/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202022-01-31%20%EC%98%A4%ED%9B%84%205.59.14.png)

위와 같이 데이터가 조회되는 것을 확인 할 수 있습니다. 
