해시에 무언가를 추가하거나 제거하는 add, remove메소드를 만들어보겠습니다.

_______
### add
```
public boolean add(K key, V value){
	// resize
	if (loadFactor() > maxLoadFactor)
		resize(tableSize*2);
	// 키와 값을 저장해 놓을 object he 정의
	HashElement<K,V> he = new HashElement(key, value);
	// he의 index 찾기
	int hashval = key.hashCode();
	hashval = hashval & 0x7FFFFFFF;
	hashval = hashval % tableSize;
	// add he
	harray[hashval].add(he);

	numElements++;
	return true;
}
```
위 코드는 key와 vaule를 제네릭으로 받아서 boolean을 반환하는 add메소드 입니다. 

해시에 추가할 땐 **크기를 조정**할지 결정을 해야합니다.

현재 전역변수와 최대 적재율을 비교를 하고, resize라는 메소드를 사용하겠습니다. 


### remove
```
public boolean remove(K key, V value){
	// index 찾기
	int hashval = key.hashCode();
	hashval = hashval & 0x7FFFFFFF;
	hashval = hashval % tableSize;
	// 해당하는 index의 키와 값 제거
	harray[hashval].remove(he);

	numElements++;
	return true;
}
```
remove 메소드에서는 크기 조정을 걱정할 필요도 없고 객체를 생성할 일도 없습니다.

### getValue 
키의 값을 찾는 getValue 메소드입니다.
키의** index가 무엇인지 찾고** 해시에서 그 index를 찾을 때까지 **반복**합니다.
그리고 key의 값이 동일하면 그 때 키의 값을 반환합니다.
```
public V getValue(K key){
	// 해당하는 index 찾기
	int hashval = key.hashCode();
	hashval = hashval & 0x7FFFFFFF;
	hashval = hashval % tableSize;
	// 그 index를 찾을 때까지 반복
	for (HashElement<K, V> he : harray[hashval]){
		if (((Comparable<K>)key).compareTo(he.key) == 0){
			return he.val;
                }
        }
	return null;
}
```

### resize
연결 리스트가 너무 길어질 경우 해시의 크기를 조절하는 resize 함수입니다.
크기가 너무 커진다면, 새로운 연결 리스트 배열을 만들고 해시의 모든 **연결 리스트에 있는 요소의 키와 값을 각각 찾아내야 합니다**.

모든 데이터를 복사하고 복사본을 만들기 때문에 **복잡도가 높습니다.**
```
public void resize(int newSize){
	// 새로운 체인 해시 생성
	<LinkedList<HashElement<K, V>>[] new_array = ...;
	(<LinkedList<HashElement<K, V>>[]) LinkedList[newSize];
	for (int i=0; i<newSize; i++)
		new_array[i] = new <LinkedList<HashElement<K, V>>[];
	// index에 맞게 값 채워넣기
	for (k key : this) {
		V val = getValue(key);
		HashElement<K,V> he = new HashElement<K, V>(key, val);
		int hashVal = (key.hashCode() & 0x7FFFFFFF) % newSize;
		new_array[hashVal].add(he);
	}
	// 덮어쓰기
	hash_array=new_array;
	tableSize=newSize;
}
```

### Key 반복자
모든 키에 대해 반복하여 해시의** 전체 내용**을 살펴봅니다.
시간 복잡도는  $O(n)$ 입니다.

```
// 키에 연결된 연결 리스트의 내용을 살펴보는 함수
class IteratorHelper<T> implements Iterator<T>{
	T[] keys;
	int position;
	// key반복자 사용
	public IteratorHelper(){
		keys = (T[]) Object[numElements];
		int p=0;
		for (int i=0; i<tableSize; i++) {
			<LinkedList<HashElement<K, V>> list = hash.array[i];
			for (HashElement<K, V> h : list)
				keys[p++] = (T) h.key();
		}
	position=0;
	}
	// 끝을 확인할 때 사용
	public boolean hasNext()
		return position < keys.length;
}
// 해시의 전체 내용을 살펴보는 함수
public T next(){
	if (!hasNext())
		return null;
	return keys[position++];
}
```
