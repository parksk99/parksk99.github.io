---
title: 더블포인터 사용
category: [c++]
tag: [c++,pointer]
---
# 더블포인터를 사용하는 경우 중 하나

***
더블포인터를 어따 써먹을수 있는지 감도 안왔는데   
이진트리 공부하다가 쓸데를 찾아냄   
연결리스트로 예를 들어봄   
## 보통 연결리스트
연결리스트에서 마지막 노드 뒤에 새로운 노드를 연결하는 기능을   
마지막 위치를 찾는 find()메소드와 add(int)메소드   
두개로 나눠서 코드를 작성할 것이다.
{% highlight c linenos%}
Node* find(){
	Node* last;

	//추가될 마지막 위치를 찾는 코드는 생략

	return last;
}

void add(int item){
	Node* tmp = find();
	tmp -> next = new Node(item);
}
{% endhighlight %}
여기서 주목할 것은 add(int) 메소드 안에있는 다음 한줄
```c++
tmp -> next = new Node(item); //이걸 next를 안쓰고 코드를 작성해봄
```
## 귀찮은 방법으로 만든 연결리스트
```c++
Node** find(){
	Node** realLast;
	
	//realLast에 찐마지막노드를 넣음
	//찐막노드는 위 코드의 last->next를 뜻함
	
	return realLast;
}

void add(int item){
	Node** tmp = find();
	*tmp = new Node(item);
}
```
달라진 점은 포인터 대신에 *더블포인터*를 썼다는 것   
   
이유는 아래 그림처럼 진짜마지막 노드를 반환해서   
거기에 직접 새로운 노드를 할당하고 싶기때문임   
   
![realLast](/assets/double_pointer/realLast.png)   
    
> 더블포인터를 안쓰고 그냥 포인터를 사용하면   
다음 노드가 연결되는게 아니고 next 자체에 새로운 노드를 생성하게됨   
> 이전 포인터 포스트를 보고 잘 생각해보면 알 수 있다.   
> 하지만 더블포인터를 쓴다면 다음 노드를 연결할수 있게 됨   

## 왜 귀찮게 만드는지
연결리스트에서는 다음 노드에 접근할때   
무조건 next로 접근할 수 있지만   
   
이진 트리에는 'left와 right' 두개의 자식 원소가 있기때문에   
다음 노드에 접근할 때 둘중에 하나를 선택해야한다.   
   
그런데 위의 add(int) 메소드에서는   
선택할 노드가 left인지 right인지 알 수 없다.   
   
따라서 마지막 노드만 찾아서 넘겨주는게 아니고   
찐막노드(새 노드가 들어갈 위치)를 직접 찾아서   
반환해줘야하기 때문에 더블포인터를 사용해야한다.   

## 결론
참고자료: 뇌피셜   
그래서 개소리일수도 있음
