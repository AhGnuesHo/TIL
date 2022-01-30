git에 푸쉬를 하는 도중 사용자 명이 자꾸만 변경되는 일이 있었다. 
그래서 전역 사용자를 변경하는 법을 알아보았다.

```git config --list```명령어를 사용하면 현재 깃 설정정보를 알 수 있습니다. 
![](https://images.velog.io/images/nnoshel/post/3f8747c6-0110-457e-93ea-5946cfa252e8/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202022-01-30%20%EC%98%A4%ED%9B%84%2010.39.58.png)

### 설정변경하기
```git config --global user.name 변경하고싶은이름```
이름을 변경하고 싶으면 user.name을 바꾸고 싶은 이름으로 지정하면됩니다. 
이메일을 변경하고 싶은경우 user.email로 바꾸고 동일하게 변경하고 싶은 이메일을 작성하면 됩니다. 

### 삭제하기
설정된 사용자를 지우고 싶은 경우도 있습니다. 
```
git config --unset --global user.name
```
위와같이 작성하면 사용자가 삭제된 것을 알 수 있습니다. 
