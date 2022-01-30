리스트에 첫번째 요소와 마지막 요소를 지우는 방법에 대해 공부
head는 항상 첫번째 노드만을 가르키고 있으므로 head= head.next를 사용하면 첫번째 노드는 아무것도 가르키지 않으므로 가비지 컬랙션 대상, 제거됨
현재 노드를 가르키고있는 current와 이전 노드르 가르키고 있는 prevoius 임시 포인터를 사용한다
커런트.넥스트를 사용해 다음노드로 이동하고 현재 커런드는 프리비어스로 설정해준다.
커런트 넥스트가 Null이면 마지막 노드이고 커런트는 tail과 같아질 것임
프리비어스 넥스트를 널로 해주면 가비지 컬렉션이 된다. 

경계조건 고려해야할것 
리스트가 비어있거나 요소가 한개만 있으먄
첫번째노드를 지우는 것처럼 똑같이하면 됨
즉 removeFirst를 실행

find 메소드는 comparable인터페이스를 사용해 compareTo를 오버라이딩 받아서 현재 노드가 찾으려는 노드와 같은지 비교한다. 
같으면 0을 반환하므로 프리비어스 넥스트가 찾은 노드의 넥스트를 가르키도록하면 찾은 노드는 가비지 컬렉션이 된다. 

peek메소드
peekFirst

public E peekFirst(){
	if (head == null){
		return null;
     	}
	return head.data;
}
위와 같이 리스트가 비어있는 경우 NullPointerException 을 주의해서 head의 데이터를 읽어오면 됩니다.

peekLast

임시포인터를 이용해 임시포인터를 head로 설정해주고, 하나씩 요소 뒤로 가면서 next가 null인 요소의 data를 읽으면 마지막 요소의 내용을 알 수 있습니다.
이렇게 하면 요소의 개수만큼 모든 요소를 살펴봐야합니다.
결국 시간 복잡도는 O(n)입니다.

그렇기 때문에 요소의 마지막을 나타내는 tail포인터를 사용하겠습니다.
마지막 요소를 이미 알고 있기때문에 tail.data를 하면 시간 복잡도는 O(1)이 되게됩니다.

이 방식으로 아래와 같이 구현 할 수 있습니다.

반복문을 이용해 배열를 각각 출력할 때 다음과 같은 코드를 사용합니다. 
```
int arr[] = {1,2,3,4,5};
for (int x:arr){
	system.out.println(x);
}
```

이런 반복문을 쓰는 경우는 굉장히 흔하기때문에 (int x:arr) 이 부분을 Iterator 인터페이스를 구현해보겠습니다.

```
public Iterator<E> iterator(){ 
	return new IteratorHelper();
} 
```
Iterator 인터페이스를 구현하는 클래스를 만들었습니다.  
```

public class LinkedList<E> implements ListI<E>{
	class IteratorHelper implement Iterator<E>{
		Node<E> index;
		public IteratorHelper(){
			index=head;
		}
		public boolean hasNext(){
			return (index != null)
		}
		public E next(){
			if (!hasNext())
				throw new NoSuchElementException();
			E val = index.data;
			index = index.next;
			return val;
		}
	}
}
```
```LinkedList<E>```를 정의하면서 이 안엔 addFirst, addLast, removeFirst.... 와 같은 메소드가 있습니다. 
그리고 이 안에 ```IteratorHelper``` 내부클래스를 만들었습니다. 
이 클래스는 Iterator를 구현합니다.

Iterator 인터페이스에는 구현해야할 메소드가 두개 있습니다. 
- hasNext : 다음 노드가 있으면 true를 반환, 포인터가 null을 가르키고 있으면 false를 반환합니다. 
- next : 현재 가리키고 있는 것을 반환합니다. 

> 
IteratorHelper를 구현하면서 컴파일이 안 된경우 remove메소드를 구현하지 않았기 때문입니다. <br>
그러나 자바 1.8 에서는 인터페이스에서 기본 메소드를 구현 할 수 있게되었습니다. 
interator인터페이스에 remove라는 메소드가 UnsupportedOperationException를 발생시키도록 구현되어있습니다. 
그래서 1.8에서는 remove메소드를 신경 쓸 필요가 없습니다. 

생성자에선 index가 head를 가르키게 했습니다. 
hasNext는 index가 null을 가르키는지만 알면 됩니다. 
리스트가 비어있거나, 리스트의 마지막에 온 경우 index가 null이면 반환 할 것이 없으므로 index != null을 반환했습니다. 
next는 hasNext가 true인지 확인합니다. 즉 리스트가 비어있는지 확인을 한 후,
false라면 ```NoSuchElementException()```를 발생시킵니다. 
반환할것이 있다면 val에게 index.data를 대입하고, index는 다음 노드를 가르킵니다. 

이렇게 작성을 하면 LinkedList의 내용들을 차례로 출력할 수 있습니다. 

단일연결리스트는 tail포인터가 있더라도 removeLast를 하기위한 시간 복잡도가  $O(n)$입니다. 
**무조건 첫 요소부터 시작**해서 마지막에서 두번째 요소까지와야 마지막요소를 제거할 수 있기 때문입니다.
그리고 마지막 노드를 기억하기위해 임시 포인터를 사용해야합니다. 

이를 빠르게 해결하기 위한것이 이중 연결 리스트(Double Linked List)입니다. 


### 이중 연결 리스트
단일 연결 리스트(LinkedList)와 유사하지만, 이중 연결 리스트에는 모든 노드에 **next, previous, data가 있습니다. **

![](https://images.velog.io/images/nnoshel/post/61315338-fa9b-49ed-a7e8-2075a63cb488/image.png)

각 노드에서 **next는 다음 노드**를 가리키고 있고 **previous는 그 전 노드**를 가리키고 있습니다.
tail 포인터는 상수 시간 안에 추가와 제거를 하기 위해 필요합니다.

이중 연결 리스트에서는 뒤에서 두번째 노드를** tail.previous로** 바로 접근 할 수 있기때문에 임시 포인터를 만들 필요없이** tail.previous.next를 null로 **가리키게 하면 됩니다.
그리고 tail은 tail.previous로 설정하면 마지막 노드가 사라지고 마지막에서 두번째 노드가 tail이 됩니다. 

그러나 addFirst를 하는 경우엔 단점이 존재합니다. 
previous 포인터가 추가되기 때문에 노드를 추가하는 과정이 더 복잡해지고 더 많은 메모리 공간이 필요하기 때문입니다. 


### 원형 연결 리스트 (Circular Linked List)

원형 연결 리스트는 마지막 next 포인터가 연결 리스트의 노드를 가리키는 연결 리스트입니다.
![](https://images.velog.io/images/nnoshel/post/c6df55eb-ec09-4ec7-92d0-d7f1c76c3c80/image.png)

원형 연결 리스트의 마지막 next 포인터가 head를 가리키는지 확인하는 방법은 다음과 같습니다.
- head에서 시작하여 ```t==null```이 될 때까지 반복한다면, 시간복잡도는  $O(n)$ 입니다.
- tail 포인터를 사용할 경우, 시간복잡도는  $O(1)$ 입니다.
 
마지막 next 포인터가 임의의 노드를 가리킨다면 확인하는 방법은 다음과 같습니다.
- tail에서 시작하여 tail 포인터가 다시 나타나는지 확인합니다. 시간복잡도는  $O(n)$ 입니다.
- 임시 포인터 2개를 사용하여 시작점을 잡고 currentSize만큼 떨어진 노드까지 확인한 후 시작점을 다음으로 옮겨 같은 노드가 나타날 때까지 반복합니다. 시간복잡도는  $O(n^2)$ 입니다.


### 스택
배열을 이용해 스택을 구현하려면 배열 뒤쪽을 이용해 addFirst와 removeFirst를 해야합니다.
그러나 둘다 시간복잡도는 $O(n)$입니다.
이는 비효율적이기 때문에 시간복잡도가  $O(1 )$인 addLast와 removeLast만 가능합니다. 

### 큐
배열로 큐를 구현하는 경우에 만약 addLast와 removeFirst를 쓴다면 addLast는 시간복잡도가 $O(1)$이고 removeFirst를 $O(n)$이고 입니다.
이것 또한 비효율적입니다. 

addFirst와 removeLast도 마찬가지입니다. 

> 그래서 스택과 큐에서는 기본적인 배열을 사용하지 않습니다. 

리스트에서는 배열 맨 앞을 가리키는 head 포인터를 사용해첫 부분을 제거하거나 추가하는 과정의 시간 복잡도가 $O(1)$입니다.

> 그래서 리스트는 스택과 큐를 하는 데 사용하는데 효율적인 알고리즘이 될 수 있습니다.

#### 배열과 LinkedList비교
배열은 리스트보다 **빠르고 메모리도 덜 차지합니다.**
그러나 **배열은 크기가 고정**되어있기때문에 배열이 차게되면 배열의 크기를 늘리고, 내용을 옮겨야합니다. 
그래서 배열이 처음 부터 적절한 크기로 만들지 않는 경우, 배열의 크기를 늘리고, 내용을 옮기는 과정을 반복하면서 리스트보다 속도가 느려질 수가 있습니다. 

하지만 **리스트는 크기가 정해져있지 않습니다.**
하지만 단점은 **느리고 메모리가 많이 필요합니다. **
