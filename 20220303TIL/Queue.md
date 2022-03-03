![](https://images.velog.io/images/nnoshel/post/ead8bbc9-2497-4300-b47b-c597c3e2519b/image.png)
큐는 위 그림처럼 먼저 들어온 데이터가 먼저나가는 FIFO방식이다.
반대로 stack은 LIFO 방식이다. 

먼저 들어온 것이 먼저 나간다는 뜻은 입구와 출구가 다른다는 의미이다. 
그렇기 때문에 큐의 앞 부분은 출구로서 삭제 연산만 수행하고
뒷 부분은 입구로서 삽입 연산만 수행한다.

- Enqueue : 큐 맨 뒤에 데이터 추가
- Dequeue : 큐 맨 앞쪽의 데이터 삭제

컴퓨터는 큐를 버퍼로서 주로 사용한다. 
예를들어 입력이 너무 많은 경우 버퍼에서 큐를 만들어 대기시켜 놓은 후 순차적으로 처리한다. 

큐를 사용하는 법을 알아보자.
____
### Queue 선언

```java
Queue queue = new LinkedList(); // 타입 설정x Object로 입력 Queue<QueueDemo> demo = new LinkedList<QueueDemo>(); // 타입을 QueueDemo클래스로 설정

Queue<Integer> i = new LinkedList<Integer>(); // Integer타입으로 선언
Queue<Integer> i2 = new LinkedList<>(); // new부분 타입 생략 가능
Queue<Integer> i3 = new LinkedList<Integer>(Arrays.asList(1, 2, 3)); // 선언과 동시에 초기값 세팅 

Queue<String> str = new LinkedList<String>(); // String타입 선언 Queue<Character> ch = new LinkedList<Character>(); // Character타입 선언
```

큐는 LinkedList로 이루어져있으므로 Queue와 LinkedList를 모두 임포트해 사용해야한다.

____
### Queue 추가
큐에 값을 넣을 땐 add(), offer()메소드를 사용하면 된다.
위 메소드의 반환 값은 boolean으로 삽입에 성공하면 true를 반환하고 실패하면 예외를 발생시킨다. 

```java
Queue<Integer> stack = new LinkedList<>(); //int형 queue 선언
queue.add(1);     // queue에 값 1 추가
queue.add(2);     // queue에 값 2 추가
queue.offer(3);   // queue에 값 3 추가
```

____
### Queue 제거
큐에서 값을 제거 할 땐  poll()이나 remove()메소드를 사용하면 된다. 
poll() 메소드는 값을 꺼내고 제거한다. 
만약 꺼낼 값이 없다면 null을 반환하고 제거한다. 

```java
import java.util.LinkedList; 
import java.util.Queue; 
public class QueueDemo { 
public static void main(String[] args) { 
Queue<String> que = new LinkedList<String>(); // 값 추가 que.add("Hello"); 
que.add("World"); 
que.add("Hello"); 
que.add("Hello"); 
que.add("World"); 

System.out.println(que); // 결과 출력 -> [Hello, World, Hello, Hello, World]

String str = que.poll(); // 맨 앞의 값 삭제 
System.out.println(str) // 결과 -> Hello
System.out.println(que); // 결과 출력 -> [World, Hello, Hello, World]

que.remove(); // 맨 앞의 값 삭제 
System.out.println(que); // 결과 출력 -> [Hello, Hello, World] 

que.remove("Hello"); // 해당하는 값 삭제
System.out.println(que); // 결과 출력 -> [Hello, World] 

que.clear(); // 초기화 모든 요소 제거
System.out.println(que); // 결과 출력 -> [] } }

```
_____
### Queue 값 출력하기
```java
que.peek();
```
큐의 맨 앞에 있는 값을 출력한다. 
