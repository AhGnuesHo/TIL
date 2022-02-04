전 포스팅에 스프링 시큐리티를 적용했습니다. 

로그인 버튼을 화면에 만들기 위해 index.mustache에 다음과 같이 추가하겠습니다. 
** index.mustache **
```
		...
        
 <div class="col-md-6">
            <a href="/posts/save" role="button" class="btn btn-primary">글 등록</a>
            {{#userName}}
                Logged in as: <span id="user">{{userName}}</span>
                <a href="/logout" class="btn btn-info active" role="button">Logout</a>
            {{/userName}}
            {{^userName}}
                <a href="/oauth2/authorization/google" class="btn btn-success active" role="button">Google Login</a>
                <a href="/oauth2/authorization/naver" class="btn btn-secondary active" role="button">Naver Login</a>
            {{/userName}}
        </div>
        
        ...
```
>- **```{{#userName}}```**  **```{{^userName}}```** 
	- 머스터치는 if를 지원하지 않기때문에 **참/거짓에 해당하는 최종값을 무조건 넘겨줘야합니다**. 
	- userName이 존재하면 userName을 노출시키게 됩니다. 
	- 존재하지 않는다면 로그인 버튼을 노출시키게 됩니다. 
    <br>
- **```a href="/logout"```**  **``` a href="/oauth2/authorization/google""```** 
	- 스프링 시큐리티에서 제공하는 로그인 / 로그아웃 url입니다. 
	- 그렇기 때문에 별도의 /logout 컨트롤러를 만들지 않아도 됩니다. 

머스테치에서 사용할 userName을 Controller에서 model로 저장하는 코드를 작성하겠습니다.
```
 private final PostsService postsService;
    private final HttpSession httpSession;

    @GetMapping("/")
    public String index(Model model){
        model.addAttribute("posts", postsService.findAllDesc());
        
        SessionUser user = (SessionUser) httpSession.getAttribute("user");
        // 로그인 성공 시 세션 값
        
        if (user != null) {
            model.addAttribute("userName", user.getName());
        }
        return "index";
    }
```
로그인이 성공했을때 세션에 저장된 user의 정보를 model에 저장했습니다. 
세션에 저장된 값이 없으면 user도 없으므로 로그인 버튼이 노출될 것입니다. 
![로그인 전 인덱스 페이지](https://images.velog.io/images/nnoshel/post/f695907b-c82e-4c2c-b8d6-b54fd7dcacd1/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202022-02-04%20%EC%98%A4%ED%9B%84%209.08.46.png)
로그인을 하기 전에는 다음과 같이 구글 로그인 버튼이 노출됩니다. ![](https://images.velog.io/images/nnoshel/post/1f3cf2d4-a81f-4715-852b-0e9d7a6bb7c5/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202022-02-04%20%EC%98%A4%ED%9B%84%208.51.34.png)
버튼을 클릭하면 위와 같이 구글 로그인 화면으로 이동합니다. 
![](https://images.velog.io/images/nnoshel/post/655b1408-5151-4681-874c-0e2892511439/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202022-02-04%20%EC%98%A4%ED%9B%84%209.09.08.png)
로그인이 성공했다면 구글계정에 등록된 이름이 화면에 노출됩니다. 
![](https://images.velog.io/images/nnoshel/post/b7eb0af4-be33-4b23-b51b-7211ed36e14f/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202022-02-04%20%EC%98%A4%ED%9B%84%208.54.27.png)
디비에서 user 테이블에 회원정보가 잘 들어간 것을 확인 할 수 있습니다. 
______
### 권한 확인

위 h2콘솔을 보면 현재 로그인 된 사용자의 권한은 GUEST입니다. 
이 권한은 글을 작성 할 수 없으므로 다음과 같은 403 권한 거부 에러가 발생합니다. ![](https://images.velog.io/images/nnoshel/post/8e44b3dd-ddad-48a8-880c-8774ea57ee36/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202022-02-04%20%EC%98%A4%ED%9B%84%209.16.34.png)

권한을 USER로 바꿔주고 글을 작성하면 글이 잘 등록되는 것을 확인 할 수 있습니다. 
![](https://images.velog.io/images/nnoshel/post/95c9820d-0aa8-4bc1-a70a-8095e7aa5dd3/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202022-02-04%20%EC%98%A4%ED%9B%84%209.19.17.png)
![](https://images.velog.io/images/nnoshel/post/ec9cf3fc-f3fa-4dc6-b483-332e519380ad/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202022-02-01%20%EC%98%A4%EC%A0%84%203.47.16.png)
