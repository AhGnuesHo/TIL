AVL트리는 왼쪽과 오른쪽의 높이 차이가 **항상 1보다 작거나 같아야**합니다. 

![](https://images.velog.io/images/nnoshel/post/5ca08ed2-7d28-4e8e-9343-0693396ba33a/image.png)

위 그림에서 완쪽 트리는 높이차이가 1보다 큰 트리입니다. 

AVL트리의 규칙을 위배했기 때문에 12의 조부모노드인 8을 회전시켰습니다. 
____
### 노드

AVL트리의 노드를 만드는 클래스는 다음과 같이 작성할 수 있습니다. 

이 클래스는 AVL트리의 내부 클래스입니다. 

```java
class Node<T>{
	T data;
	Node<T> left; // 왼쪽 노드 포인터
	Node<T> right; // 오른쪽 노드 포인터
	Node<T> parent; // 부모 노드 포인터
	// 생성자
	public Node(T obj){
		data = obj;
		parent = left = right = null;
}
```

___
### add

AVL 트리에 add를 하는 메소드를 만들어보겠습니다. 

우선 AVLTree클래스는 생성자에서 root를 null로 설정하고, currentSize를 0을 설정하고 시작하겠습니다. 

```java
// AVL 클래스의 생성자
public AVLTree(){
	root = null;
	currentSize = 0;
}
// add 메소드
public void add(E obj){
	Node<E> node = new Node<E>(obj); // 추가할 노드
	// 트리가 비어있을 경우
	if (root == null){
		root = node;
		currentSize++;
		return;
	}
	// 트리에 노드가 있을 경우 add 메소드를 재귀로 호출
	add(root, node);
}
```
노드추가 메소드는 반환할 값이 필요없으므로 추가할 노드만 E타입 인자로 설정했습니다. 

새로운 노드를 만들었다면 트리가 비었는지 확인을 해야합니다. 
트리가 비어있는 경우엔 root가 null인경우이므로 추가할 노드를 바로 root로 설정하면 됩니다. 

비어있지 않은 경우에는 요소를 어디에 넣을지 찾아야하므로 현재 보고있는 노드와, 추가할 노드를 가지고 add메소드를 재귀로 호출합니다. 

#### 재귀 add 
```java
public void add(Node<E> parent, Node<E> newNode){
	// newNode의 data가 parent의 data보다 크면 트리의 오른쪽에 추가하면 됩니다.
	if (((Comparable<E>)newNode.data.compareTo(parent.data)>0{
		if (parent.right == null){
			parent.right = newNode;
			newNode.parent = parent;
			currentSize++;
		}
		else {
			add(parent.right, newNode);
            }
	// newNode의 data가 parent의 data보다 작거나 같으면 트리의 왼쪽에 추가하면 됩니다.
	else{
		if (parent.left == null){
			parent.left = newNode;
			newNode.parent = parent;
			currentSize++;
		}
		else {
			add(parent.left, newNode);
      		}
	// AVL트리가 규칙에 맞게 잘 되어있는지 확인합니다.
	checkBalance(newNode);
}
```
이 메소드에서는 추가하고자 하는 노드가 현재 보고있는 노드보다 작은지 큰지 비교해야합니다. 

그리고 자식이 **null이 될때까지 재귀함수를 반복**하면서 null이 되는 위치에 노드를 추가하고 사이즈를 증가시켜주면 됩니다. 

마지막으로 추가한 노드부터 트리를 따라 올라가면서 트리가 규칙에 맞게 생성됐는지 확인합니다. 

___

#### checkBalance 메소드

트리의 균형확인은 **추가한 노드부터 시작**해서 높이 차이가 정상적인지 확인합니다.

node.parent가 null이 되서 **루트에 도달할 때 까지 checkBalance를 재귀호출하**면 됩니다. 

```java
public void checkBalance(Node<E> node){
	// 높이 차이가 1 초과 혹은 -1 미만 (AVL 트리 규칙 위반)
	if ((height(node.left) - height(node.right)>1) || (height(node.left) - height(node.right)<-1)){
		rebalance(node);
	// 부모 노드를 계속 확인해서 루트까지 갑니다.
	if (node.parent == null)
		return;
	checkBalance(node.parent);
}
```

____

#### rebalance 메소드

트리가 균형이 맞지 않는다면 회전을 통해 균형을 맞춰줘야합니다. '

회전을 위해선 **왼쪽과 오른쪽 중 어디가 1보다 큰 쪽인지** 확인해야합니다. 

```java
public void rebalance(Node<E> node){
	// 왼쪽 자식 > 오른쪽 자식
	if (height(node.left) - height(node.right)>1) {
		if(height(node.left.left) > height(node.left.right)) {// 왼쪽 서브 트리 > 오른쪽 서브 트리
			node = rightRotate(node); // 우측 회전
		} else {// 왼쪽 서브 트리 < 오른쪽 서브 트리
			node = leftRightRotate(node); // 좌측-우측 회전
            }
	}
	// 왼쪽 자식 < 오른쪽 자식
	else{ 
		if(height(node.left.left) > height(node.left.right)) {// 왼쪽 서브 트리 > 오른쪽 서브 트리
			node = rightLeftRotate(node); // 우측-좌측 회전
		}else {// 왼쪽 서브 트리 < 오른쪽 서브 트리
			node = leftRotate(node); // 좌측 회전	
            }
	}
	// 루트로 올 때까지 반복합니다.
	if (node.parent == null){
		root=node;
        }
}
```
노드의 왼쪽 자식과 오른쪽 자식의 차이를 통해 어느쪽이 긴 쪽인지 확인을 하면 
또 다시 왼쪽 서브트리와 오른쪽 서브트리를 비교해 어느 회전을 해야하는지 구합니다. 

만약 왼쪽 자식이 길고, 왼쪽 자식의 왼쪽 서브트리가 길면 우측회전을 해야합니다.
왼쪽 자식이 길고, 왼쪽 자식의 오른쪽 서브트리가 길면 좌측-우측회전을 해야합니다. 

회전 메소드는 [여기](https://velog.io/@nnoshel/%ED%8A%B8%EB%A6%AC-%ED%9A%8C%EC%A0%84)에서 작성한 회전 메소드와 같은 메소드입니다. 

이 과정을 루트로 올 때 까지 반복하면 됩니다. 











