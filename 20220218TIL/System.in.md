코딩 테스트 연습을 하다보니 아주 오랜만에 자바 입출력을 사용해야 했다. 

Scanner 클래스를 사용하여 값을 입력받을 수 있다. 

## new Scanner(System.in)
### sc.next();
```java
Scanner sc = new Scanner(System.in);
String str = sc.next();
```
공백을 입력하기 전까지 값을 입력받는다. 

>입력
```
Hello everybody!
```
출력
> ```
Hello
```

everybody! 앞에 공백이 있으므로 everybody!는 출력되지 않는다. 


### sc.nextLine();
Enter를 입력하기 전까지의 값을 입력받음
```java
Scanner sc = new Scanner(System.in);
String str = sc.nextLine();
```
>입력
```
Hello everybody!
12345
```
출력
> ```
Hello everybody!
```

### sc.nextInt();
Enter를 입력하기 전까지의 정수값을 입력받음
문자열을 입력하면 에러발생
```java
Scanner sc = new Scanner(System.in);
int i = sc.nextInt();
```
>입력
```
12345
6789
```
출력
> ```
12345
```

## new Scanner(System.in)
###  br.readLine();
 Enter를 치기 전까지의 값을 입력받음
 ```java
BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
String brStr = br.readLine();
```
>입력
```
abcdefghijk
1234567
```
출력
> ```
abcdefghijk
```

### Integer.parseInt(br.readLine());
Enter를 치기 전까지의 정수값을 입력받음

BufferedReader에서는 Int로 값을 읽는 방법이 없다. 

Integer.parseInt를 사용하여 String을 Int로 변환해야한다.

 ```java
BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
 int brInt = Integer.parseInt(br.readLine());
```
>입력
```
1234567
```
출력
> ```
1234567
```

