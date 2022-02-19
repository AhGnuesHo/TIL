지난 포스팅에 자바 입력에 관해 작성을 했다. 

버퍼에 대한 내용도 작성했지만 스캐너가 손에 익어서 자주 사용했다.

하지만 알고리즘 문제를 풀면서 시간 초과를 해결하려면 버퍼를 쓰는 것이 유리했으므로 버퍼에 대해 자세히 알아보려고 한다. 

일단 앞서 말했듯이** BufferedReader 는 Scanner와 유사하고 Bufferedwriter는 System.out.println()과 유사하다. 
**

**이 둘의 차이점은 속도다.**
스캐너는 사용자가 키보드 입력을 하는 즉시 전달되지만, 버퍼는 중간 **메모리 버퍼에 입력 내용을 저장**했다가 한꺼번에 전달한다. 

하드디스크의 속도는 매우 느리다. 
그러므로 입력이 많을 수록 하드디스크를 통해 입출력을 여러번 반복해야한다. 
그러므로 버퍼에 저장을 했다가 한번에 전달하는 것이 성능상 유리하다. 

### BuffredReader
버퍼 리더의 사용법은 지난 포스팅에 적혀있다. 

> 
```java
public static void main(String[] args) throws IOException {
>
// BufferedReader를 사용하기 위해서는 throws IOException을 해 주어야 함.
BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
>
String s = br.readLine(); //String
int i = Integer.parseInt(br.readLine()); //Int
...
```

위 처럼 선언을 해서 사용하면 된다. 
주의해야 할 점은** 예외처리를 꼭 해줘야하는 것이다.** 
또한 `readLine()` 는 **반환타입이 항상 문자열**이므로 정수형을 사용하고 싶다면 형 변환을 해줘야한다. 

스캐너는 공백단위로 알아서 끊어줬지만 버퍼에서 공백단위로 사용하기 위해서는 `StringTokenizer` 의 `nextToken` 함수를 이용하는 방법이 있다. 

```java
StringTokenizer st = new StringTokenizer(br.readLine());
for (int i = 0; i < N; i++) {
			arr[i] = Integer.parseInt(st.nextToken());
	}
    br.close();
 ```
`StringTokenizer` 를 통해 문자열을 입력받아 온 후, `nextToken`을 사용해 공백 단위로 저장할 수 있다. 

입력이 끝난 후에는 버퍼를 닫아준다. 
____

### BufferedWriter

```java
public class Main {

	public static void main(String[] args) throws IOException {
		// BufferedWriter를 사용하기 위해서는 throws IOException을 해 주어야 함.
		BufferedWriter bw = new BufferedWriter(new OutputStreamWriter(System.out)); // 선언
		bw.write("Hello World");
		bw.flush(); // write로 담은 내용 출력 후, 버퍼를 비움.
		bw.close(); 
	}

}
```
`write()` 함수에 작성하고 싶은 내용을 작성하고 `flush()` 를 해주면 끝이다. 

동일하게 출력이 끝났다면 스트림을 닫아준다. 
