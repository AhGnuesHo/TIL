그레이들 프로젝트를 생성했습니다. 
이 그레이들 프로젝트를 스프링 부트 프로젝트로 변경해야합니다. 
build.gradle파일을 가면 아래와 같은 파일이 열립니다. 
![](https://images.velog.io/images/nnoshel/post/3236a06d-d0f9-4578-aef2-d7e004b8c366/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202022-01-27%20%EC%98%A4%ED%9B%84%208.51.37.png)
이 코드를 지우고 다음과 같이 코드를 작성하겠습니다.
```
buildscript{
    ext{
        springBootVersion = '2.1.7.RELEASE'
    }
    repositories {
        mavenCentral()
       
    }
    dependencies {
        classpath("org.springframework.boot:spring-boot-gradle-plugin:${springBootVersion}")
    }
} 
```
위 코드는 프로젝트의 플러그인 의존성을 관리하기 위해 추가한 설정입니다. 
ext라는 키워드는 build.gradle에서 전역변수를 지정할때 사용합니다. 
즉 ```springBootVersion```라는 전역변수의 값을 2.1.7.RELEASE로 정의 한 것입니다. 
이로서 클래스패스에선 spring-boot-gradle-plugin는 2.1.7.RELEASE를 의존성 받겠다는 의미입니다. 

바로 다음 줄에는 플러그인 의존성들을 적용할 지 결정하는 코드를 작성하겠습니다.
특히 ```io.spring.dependency-management``` 플러그인은 스프링 부트의 의존성을 관리해주므로 꼭 추가해야합니다. 
```
apply plugin : 'java'
apply plugin : 'eclipes'
apply plugin : 'org.springframework.boot'
apply plugin : 'io.spring.dependency-management'
```

다음 코드는 아래와 같습니다. 
```
repositories {
    mavenCentral()
}

dependencies {
    implementation('org.springframework.boot:spring-boot-starter-web')
    testImplementation('org.springframewokr.boot:spring-boot-starter-test')
}
```
각 역할은 다음과 같습니다. 

**repositories** 어떤 원격 저장소에서 받아올지 결정 

**dependencies** 필요한 의존성을 선언
ctrl + space 로 자동완성이 가능하나, 버전은 직접 명시하면 안됩니다. 
위에서 설정한 클래스 패스의 설정을 따라가야하기 때문입니다. 

> 버전을 한곳으로 집중해야 버전 충돌 문제을 피할 수 있으며 개발을 편하게 할 수 있습니다. 

모든 코드를 작성하면 아래와 같습니다. 
![](https://images.velog.io/images/nnoshel/post/78b994ab-c3fc-428d-bd04-cdea154796ad/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202022-01-27%20%EC%98%A4%ED%9B%84%2010.33.05.png)

이제 오른쪽 gradle탭에서 리프레쉬 버튼을 누르면 의존성을 다운받게됩니다. 

>위 일련의 과정들은 직접 코드를 입력하지 않고 스프링 이니셜라이저를 사용하면 한번에 진행 할 수있습니다. 

### 추가
예제 상으론 디펜던시를 추가할 때 
implementation와 testImplementation 가 아닌 compile, testComile을 사용하였다. 

나의 경우 compile을 사용한경우 오류가 나서 의존성을 추가 할 수 없었다. 

사실 그동안 둘 중 아무거나 사용해도 작동은 잘 되서 그다지 궁금증을 가지진 않았으나, 이번기회에 알아보기로했다. 

>**api(compile)**
The dependencies required to compile the production source of the project which are part of the API exposed by the project. For example the project uses Guava and exposes public interfaces with Guava classes in their method signatures.
 
>**implementation**
The dependencies required to compile the production source of the project which are not part of the API exposed by the project. For example the project uses Hibernate for its internal persistence layer implementation.

공식문서의 설명이다...

compile의 경우 하나의 모듈이 수정되면 그를 **직접,간접** 의존하고 있는 모듈이 모두 recompile된다고한다. 

그러나 implementation는 이를 **직접적으로 의존하고있는 모듈만** recompile된다.

즉 implementation가 더 **빠르며 API의 노출도 적어집니다.**
하나의 모듈이 수정된것을 필요한 모듈만 알 수 있으니 compile처럼 모든 모듈이 노출되는 일은 일어나지 않기 때문입니다. 

#### 추추가
그리고 compile 은 deprecated 되고 api 로 대체되었다고 합니다. 
그렇기 때문에 compile을 써야하는 경우 api 를 사용하면 됩니다.
