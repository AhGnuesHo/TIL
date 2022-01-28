### addLast

addLast 메소드에서는 연결 리스트의 마지막을 가리키는 **임시 포인터**를 사용합니다.
연결 리스트의 요소를 확인하려면 **무조건 head에서 시작**해야 하는데, 연결 리스트의 마지막까지 도달하는 데 next를 너무 많이 사용해야 하기 때문입니다. 
_예를 들면 이런식입니다. head.next.next.next.next = node; _

그럼 어떻게 임시포인트를 사용할까요? 

![](https://images.velog.io/images/nnoshel/post/08146fec-6346-4cef-ae1f-5cfb9f403744/image.png)

head는 정상적으로 첫번째 노드인 a를 가르키고 있습니다. 그리고 a는 b, b는c를 가르킵니다. 그리고 c는 null을 가르키게됩니다. 

그럼 맨 마지막 노드를 찾을 땐 **next 변수가 null을 향해 가르키고있는가?**를 찾으면 알 수 있게됩니다. 

그래서 임시포인터가 리스트 맨 앞에서부터 시작해서 tmp.next가 null이 아니면 그 뒤에 노드가 더 있다는 뜻입니다. 

tmp가 계속 다음 노드로 이동을 하면서 tmp.next가 null이게 되면, 새로운 노드를 가지고 tmp.next가 null이 아닌 새로운 노드를 가리키게 하면 됩니다. 

그러면 아래와 같은 addLast메소드를 작성할 수 있게됩니다. 
```
public void addLast(E obj){
	Node<E> tmp = head;
	while(tmp.next != null)
		tmp=tmp.next
	tmp.next=node;
}
```
그리고 tmp가 범위를 벗어나게 되면 tmp는 가비지 컬렉션 대상이됩니다. 

### 경계조건
만약 생성되어있는 노드가 없어, head가 null을 가리키고있고, tmp도 null을 가르키게 되는 경우엔, tmp.next를 찾을 수 없어 **런타임에러가 발생합니다**. 
즉 리스트 맨 뒤에 노드를 추가하려고하는데 리스트가 비어있는 경우엔
이전 포스팅에 나왔던 **addFirst 메소드를 사용해 노드를 추가**해주면 됩니다. 
```
public void addLast(E obj){
	Node<E> node = new Node<E>(obj);
	if (head == null){ // head가 비어있는 경우
		head=node;
		currentsize++;
		return;
	}
	Node<E> tmp = head;
	while(tmp.next != null)
		tmp=tmp.next
	tmp.next=node;
	currentsize++;
}
```

### 시간복잡도
연결 리스트의 마지막 노드를 찾을 때 리스트의 맨 앞부터 시작해서 마지막 요소까지 살펴보면 시간 복잡도는 $O(n)$ 입니다.  
하지만 tail 포인터를 사용하면 이 시간 복잡도를  $O(1)$ 로 만들 수 있습니다. 
**리스트의 마지막을 가리키는 tail 포인터를** head, currentSize와 같은 전역 변수로 설정하고, 아래와 같이 tail 포인터를 추가하면 됩니다.
```
public void addLast(E obj){
	Node<E> node = new Node<E>(obj);
	if (head == null){
		head=node;
		tail=node; // head 포인터뿐만 아니라 tail 포인터도 바꿔줘야 합니다.
		currentsize++;
		return;
	}
	tail.next=node;
	tail = node;
	currentsize++;
}
```

