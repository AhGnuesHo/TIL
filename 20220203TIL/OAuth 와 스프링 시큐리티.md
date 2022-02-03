
**스프링 시큐리티는 인증과 인가 기능을 가진 프레임워크로 스프링기반의 어플리케이션에서는 보안을 위한 표준입니다. **

많은 서비스에서 아이디와 패스워드를 직접 입력하기보다는 구글, 페이스북, 네이버 로그인과 같은 소셜 로그인을 사용하고있습니다. 

로그인을 직접 구현할 경우 로그인시 보안, 회원가입 시 이메일 혹은 전화번호 인증, 비밀번호 찾기, 비밀번호 변경, 회원정보 변경을 직접 구현해야합니다. 

>하지만 OAuth로 로그인 기능을 구현하면, 이 것들을 모두 구글, 페이스북, 네이버 등에 맡길 수 있습니다. 

### 구글 서비스 등록
구글 클라우드 플랫폼에 신규 서비스에서 발급받은 인증정보를 application-oauth.properties에 등록하겠습니다. 

>spring.security.oauth2.client.registration.google.client-id=클라이언트 ID
spring.security.oauth2.client.registration.google.client-secret=클라이언트 보안 비밀
spring.security.oauth2.client.registration.google.scope=profile,email
	>	
    scope를 등록하지 않으면 openid,pofile,email이 기본으로 설정되나,
    openid라는 scope가 있느면 Open Id Provider로 인식하기때문에
    OpenId Provider인 서비스와 그렇지 않은(네이버/카카오 등) 
    서비스로 나눠서 OAuth2Service를 만들어야 하기 때문에 하나의 OAuth2Service로
    사용하기위해 openid 스코프를 빼고 등록합니다. 


스프링 부트는 프로퍼티의 이름을 **application-xxx.properties라고 만들면 이름의 profile이 생성되면서 해당 properties의 설정들을 가져올** 수 있습니다. 

aplication.properties에서 application-oauth.properties를 포함하도록 아래와 같이 추가합니다. 
> spring.profiles.include=oauth

클라이언트Id와 클라이언트 보안비밀은 **외부에 노출되면 안되므로 .gitignore에** 다음과 같은 코드를 추가하여 깃허브에 application-oauth.properties가 올라가는 것을 방지하겠습니다. 
>application-oauth.properties

_____________________________________

### 구글 로그인 연동하기

사용자 정보를 담을 User 엔티티 클래스를 생성하겠습니다. 
** User.java **
```
package com.seungha.study.domain.user;

import com.seungha.study.domain.posts.BaseTimeEntity;
import lombok.Builder;
import lombok.Getter;
import lombok.NoArgsConstructor;

import javax.persistence.*;
@Getter
@NoArgsConstructor
@Entity
public class User extends BaseTimeEntity {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private String id; // PK

    @Column(nullable = false)
    private String name; // notnull

    @Column(nullable = false)
    private String email; // notnull

    @Column
    private String picture;

    @Enumerated(EnumType.STRING)
    @Column
    private Role role;

    @Builder
    public User(String name, String email, String picture, Role role) {
        this.name = name;
        this.email = email;
        this.picture = picture;
        this.role = role;
    }

    public User update(String name, String picture){
        this.name = name;
        this.picture = picture;

        return this;
    }

    public String getRoleKey(){
        return this.getKey();
    }
}

```

엔티티 클래스이므로 롬복은 Getter만 지정했습니다. 
setter를 제외 한 이유에 대해서는 이전 포스팅을 확인하면 됩니다. 

> **```@Enumerated(EnumType.STRING)``` **
JPA로 데이터 베이스를 저장할때 **Enum값을 어떤 형태로 저장할지 결정**해줍니다. 
기본적으로는 **int가 저장**되지만 숫자로 저장되면 데이터베이스에서 그 숫자가 무슨 코드를 의미하는지 알기 어렵기때문에 문자열로 지정합니다. 

```Role```은 각 사용자의 권한을 관리해주는 Enum입니다. 
Enum클래스는 아래와 같이 작성하겠습니다. 

** Role.java **

```
@Getter
@RequiredArgsConstructor
public enum Role {

    GUEST("ROLE_GUEST", "손님"), 
    USER("ROLE_USER", "일반 사용자");

    private final String key;
    private final String title;
}

```

>스프링 시큐리티는 권한 코드에 "ROLE_"이 항상 문자 맨 앞에 있어야합니다. 

사용자 CRUD를 위해 레파지토리를 생성하겠습니다.   

** UserRepostiory.java**
```
public interface UserRepostiory extends JpaRepository<User, Long> {
    Optional<User> findByEmail(String email);
    // 소셜 로그인으로 반환되는 값 중 email를 통해 이미 생성된 사용자인지
    	처음 가입하는 사용자인지 판단하기 위해 사용
}
```
> 레파지토리는 DBLayer 접근자로, 인터페이스로 생성합니다.
그리고 JpaRepostiory <Entity 클래스, PK타입>을 상속하면 기본 CRUD메소드가 자동으로 생성됩니다. 

______________

### 스프링 시큐리티 설정
스프링 스큐리티를 사용하기위해 의존성을 추가하겠습니다. 

>  implementation('org.springframework.boot:spring-boot-starter-oauth2-client')

다음과 같이 소셜 로그인 설정 정보를 추가하겠습니다. 
config.auth와 같은 패키지를 만들어 시큐리티와 관련된 클래스는 모두 이곳 만들어 관리하겠습니다. 
** SecurityConfig.java **
```
@RequiredArgsConstructor
@EnableWebSecurity // 스프링시큐리티의 설정을 활성화 시켜준다.
public class SecurityConfig extends WebSecurityConfigurerAdapter { 
    private final CustomOAuth2UserService customOAuth2UserService;
    
    @Override
    public void configure(WebSecurity web) throws Exception {
        http
                .csrf().disable()
                .headers().frameOptions().disable() // h2-console 화면을 사용하기위해 비활성화
                .and()
                .authorizeRequests() // URL별 권한 관리를 설정하는 옵션
                .antMatchers("/", "/css/**","/images/**",
                        "/js/**","/h2-console/**").permitAll() // permitAll : 전체열람가능
                .antMatchers("/api/v1/**").hasRole(Role.USER.name()) // "/api/v1/**"는 USER만 허용
                .anyRequest() // 설정된 값 이외 나머지 URL
                .authenticated() // anyRequest()로 인해 나머지 url들은 모두 인증된 사용자만 허용
                .and()
                .logout()
                .logoutSuccessUrl("/") // 로그아웃 성공시 / 로 이동 
                .and()
                .oauth2Login()
                .userInfoEndpoint() // 로그인 성공 후 사용자 정보 가져오기
                .userService(customOAuth2UserService); // 소셜 로그인 성공 후, 추가로 진행하고자 하는 기능을 명시
    }
}

```

>**```.authorizeRequests() ```**
**URL별 권한 관리를 설정하는 옵션**으로 해당 옵션이 설정되어햐 Matchers를 이용해 URL별로 관리 대상을 지정해줄 수 있습니다.

로그인 이후 가져온 사용자의 정보를 기반으로 사용자 정보수정, 세션 저장등의 기능을 지원할 서비스 클래스를 만들겠습니다. 

** CustomOAuth2UserService.java **
```
@RequiredArgsConstructor // 의존성주입
@Service
public class CustomOAuth2UserService implements OAuth2UserService<OAuth2UserRequest, OAuth2User> {
    private final UserRepostiory userRepostiory;
    private final HttpSession httpSession;

    @Override
    public OAuth2User loadUser(OAuth2UserRequest userRequest) throws OAuth2AuthenticationException {
        OAuth2UserService<OAuth2UserRequest, OAuth2User> delegate = new DefaultOAuth2UserService();
        OAuth2User oAuth2User = delegate.loadUser(userRequest);

        String registrationId = userRequest.getClientRegistration().getRegistrationId();
        // 현재 로그인 진행중인 서비스를 구분
        // 구글로그인인지, 네이버인지, 페이스북인지 구분하기위해 사용

        String userNameAttributeName = userRequest.getClientRegistration().getProviderDetails().getUserInfoEndpoint().getUserNameAttributeName();
        // OAuth2 로그인 진행시 키가 되는 필드값.
        // primary key와 같은 의미.
        // 구글의 경우 기본 "sub"를 사용하지만 네이버 카카오등은 기본 지원하지 않음.
        // 네이버로그인과 구글로그인을 동시 지원할 때 사용

        OAuthAttributes attributes = OAuthAttributes.of(registrationId, userNameAttributeName, oAuth2User.getAttributes());
        //OAuth2UserService를 통해 가져온 OAuth2User의 attribute를 담은 클래스로 네이버 등 다른 소셜 로그인도 이 클래스를 사용
        // 새로 구현해야함

        User user = saveOrUpdate(attributes);

        httpSession.setAttribute("user", new SessionUser(user));
        // 세션에 사용자 정보를 저장하기위한 Dto클래스.

        return new DefaultOAuth2User(
                Collections.singleton(new SimpleGrantedAuthority(user.getRoleKey())),
                attributes.getAttributes(),
                attributes.getNameAttributeKey());
    }

    private User saveOrUpdate(OAuthAttributes attributes) {
        User user = userRepostiory.findByEmail(attributes.getEmail())
                .map(entity -> entity.update(attributes.getName(), attributes.getPicture())).orElse(attributes.toEntity());

        return userRepostiory.save(user);
    }
}

```
구글 사용자 정보가 업데이트 되는 경우를 대비해 update기능도 구현했습니다. 
사용자 이름, 프로필사진이 변경되면 User엔티티에도 반영됩니다. 

** OAuthAttributes.java **
```

@Getter
public class OAuthAttributes {
    private Map<String, Object> attributes;
    private String nameAttributeKey;
    private String name;
    private String email;
    private String picture;

@Builder
    public OAuthAttributes(Map<String, Object> attributes, String nameAttributeKey, String name, String email, String picture) {
        this.attributes = attributes;
        this.nameAttributeKey = nameAttributeKey;
        this.name = name;
        this.email = email;
        this.picture = picture;
    }

    public static OAuthAttributes of(String registrationId, String userNameAttributeName, Map<String, Object> attributes){
        return ofGoogle(userNameAttributeName, attributes);
        //  OAuth2User에서 반환한 정보를  Map에서 OAuthAttributes로 반환하는 코드.
    }

    private static OAuthAttributes ofGoogle(String userNameAttributeName,Map<String, Object> attributes ){
        return OAuthAttributes.builder()
                .name((String) attributes.get("name"))
                .email((String)attributes.get("email"))
                .picture((String)attributes.get("picture"))
                .attributes(attributes)
                .nameAttributeKey(userNameAttributeName)
                .build();
    }

    public User toEntity() {
        return User.builder()
                .name(name)
                .email(email)
                .picture(picture)
                .role(Role.GUEST)
                .build();
        // user엔티티를 생성
        // OAuthAttributes가 user엔티티를 생성하는 시점은 처음 가입할때이므로 기본 권한을 게스트로 줘야함.
        // OAuthAttributes 클래스 생성이 끝났으면 같은 패키지에 세션클래스를 추가해야함
    }
}

```

** SessionUser.java **
```
@Getter
public class SessionUser implements Serializable {
    private String name;
    private String email;
    private String picture;


    public SessionUser(User user) {
        this.name = name;
        this.email = email;
        this.picture = picture;
    }

}
```
SessionUser에는 인증된 사용자 정보만 필됴하기때문에 name, email, picture만 선언합니다.

> 만약 SessionUser클래스 대신 User 클래스를 그대로 사용했다면, **직렬화 오류가 생길것입니다. **
그렇다고 직렬화 코드를 User코드에 함께 넣어서 사용하면 해당 엔티티의 자식 엔티티까지 직렬화 대상에 포함되므로 직렬화 기능만을 가진 sesseion Dto를 생성했습니다. 
