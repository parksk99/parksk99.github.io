---
title: (c++) virtual이란?
category: [cpp]
tag: [cpp]
---

c++ virtual
===
자료구조 공부하다가 메소드 이름 앞 virtual을 봤는데   
뭔지 전혀 기억이 안나서 검색해서 정리해놨던걸 정리함   

*상속관계에서 중요*

## 오버라이딩했을때 virtual을 안붙인다면??
``` c++
class Bag{
    public:
        void print(){ std::cout<<"Bag"<<std::endl; }
};

class Stack : public Bag{
    public:
        void print(){ std::cout<<"Stack"<<std::endl; }
};
```
위와 같은 구조의 클래스가 있을 때
``` c++
int main(){
    Bag* bag;
    Stack stack;
    bag = &stack;

    bag.print();
}
```
main 함수를 실행시키면   
Bag으로 선언한 객체에서 print()메소드를 사용했기 때문에   
부모클래스인 Bag::print()가 실행된다
```
Bag
```

## 근데 void Bag::print()앞에 virtual을 붙인다면??
main 함수 실행시   
자식클래스인 Stack::print()가 실행됨!
```
Stack
```

## 결론
* 이유 : virtual로 메소드를 선언하면 컴파일 타임에 결정되지 않고   
런타임에 결정된다고 함.   

* 오버라이딩했을때 자바에서처럼 다형성을 만들고 싶으면   
앞에 virtual을 붙여줘야한다.

* 참고자료 : 오래전에 검색했던거라 기억이 안남
