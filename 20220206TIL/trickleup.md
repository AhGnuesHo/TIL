트리를 순회하는 방법에는 네가지가 있습니다. 
> **중위 순회** : 왼쪽 -> 루트 -> 오른쪽
**전위 순회**: 루트 ->  왼쪽 -> 오른쪽
**후위 순회**: 왼쪽 -> 오른쪽 -> 루트
**레벨 순서 순회** : 레벨대로 순회

다음은 레벨 순서 순위에 대해 알아보겠습니다. 
![](https://images.velog.io/images/nnoshel/post/467debba-5c50-412a-9a34-d830b1106733/image.png)

각 노드에는 위와 같이 숫자가 붙어있습니다. 
예를 들어 3번노드의 자식 노드는 7과 8입니다. 

즉 루트가 0인경우 모든 관계는 다음과 같이 성립할 수 있습니다. 
> 자식노드 = 부모노드 x 2 + 1, 부모노드 x 2 + 2

반대로 자식 노드를 가지고 부모 노드를 알아 낼 수 있습니다. 
> 부모노드 = floor(자식노드 - 1)/2 

위와 같은 이진 힙을 코드로 구현하기위해선 배열을 사용할 수 있습니다. 
배열의 각 인덱스에 노드의 값을 채우는 방법으로 코드를 작성해보겠습니다. 
```
int lastposition; // 어디까지 요소를 넣었는지 기록
E[] array = (E[]) new Object[size];
public void add(E obj){
	array[++lastposition] = obj; // 1. 노드 추가. (현재노드에 +1을 해준다.)
	trickleup(lastposition); // 2. 요소를 추가할땐 trickle up과정이 필수.
}
public void swap(int from, int to){
	E tmp = array[from];
	array[from] = array[to];
	array[to] = tmp;
}
public void trickleup(int position){
	if (position == 0)
		return; // 루트에 도달하면 종료.
	int parent = (int) Math.floor((position-1)/2) // 현재위치로 부모노드 계산.
	if (((Comparable<E>) array[position]).compareTo(array.parent)>0) {
		swap(position, parent); // 부모노드가 자식노드를 비교하고 자리를 바꿈.
		trickleup(parent); // 부모노드를 가지고 루트에 도달할때 까지 trickle up.
	}
}
```
배열은 실제 들어갈 요소의 개수보다 큰 배열이어야합니다. 
```lastposition``` 라는 전역변수는 요소가 어디까지 차 있는지 기록합니다.

힙에 요소를 추가할 때는 추가할 위치의 부모노드의 숫자와 비교를 하고,
부모노드보다 큰 경우 부모노드와 추가할 노드의 자리를 바꿔야합니다. 
이 과정을 trinkle up이라고 합니다. 

>즉 요소를 추가할때는 trinkle up과정을 거쳐야합니다. 

요소가 추가되었다면 ```lastposition``` 에 현재 위치를 알려줘야합니다. 

현재위치를 알게되면 위에서 설명한대로 부모노드를 계산할 수 있게됩니다. 
부모노드를 계산하면서 루트까지 도달하게되면 add가 완료됩니다. 

