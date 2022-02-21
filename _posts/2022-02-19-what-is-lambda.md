---
title: cpp 람다표현식
category: [cpp]
tag: [cpp]
toc: true
toc_label: 목차
toc_sticky: true
---
# 람다표현식

---
C++11 이후 사용 가능하고 익명 함수를 쉽게 정의할수 있는 방법이다. sort함수에 3번째 인자로 들어가는 compare함수를 이상한 방법으로 표현하는 것을 보고 열심히 검색해서 찾아본 결과를 정리해봄. ms홈페이지에 있는 document를 해석하고 정리함.
```c++
#include <iostream>
#include <algorithm>
using namespace std;

int main()
{
    int a[5] = {1, 3, 2, 4, 5};
    sort(a, a + 5, [](int x, int y) -> bool
         { return x > y; });
    for (int i = 0; i < 5; i++)
        cout << a[i] << ' ';
    return 0;
}
```
sort의 세번째 인자에 들어가는게 람다 표현식.   
원래 bool을 반환하는 compare함수 포인터를 넘겨주는게 정석이지만 람다 표현식을 사용하면 함수를 정의하지 않고도 
동일한 기능을 수행할 수 있다.

## 람다 표현식의 구조
![lambda](/assets/what_is_lambda/lambda_syntax.png)   
위 사진처럼 6개의 부분으로 이루어져 있다.
1. capture clause(인접한 변수들을 캡쳐해오기 : 아래에서 설명)
    * 인접한 변수란? 
    > 영어로는 enclosing 이고 번역하면 '둘러싸고 있는' 이라는 뜻이다.
    정확히 뭐라고 번역하는지 몰라서 '둘러싸고 있는 변수'라고 하면 너무 길기도 하고 '인접한'이랑 이해 한번에 안되긴 마찬가지라 걍 인접한이라고 해버렸음.   
2. 인자 리스트 : optional(없어도 괜찮음)
3. mutable specification(mutable하다고 못박기) : optional
4. exception specification(예외 발생할수도 있다고 못박기) : optional
5. trailing return type(리턴 타입을 지정한다고 생각하면 됨) : optional
6. 람다 몸체(일반 function의 몸체와 동일)

### 1. capture clause
람다 표현식 안에서 주변의 지역변수에 접근할 수 없다. 그 이유를 미루어 짐작해보면 람다 표현식이 일종의 함수이기 때문이 아닐까 싶다. 람다식은 컴파일될 때 개별된 스택 영역에 저장될 것이다. 따라서 람다식 안에서는 인접한 지역변수에 접근할 수가 없게 된다.   
<br>
이 생각을 뒷받침하는 증거는 람다식 안에서 전역변수는 아무런 문제 없이 접근하고 사용할 수 있다는 것이다. 
다른 특별한 이유가 있었다면 전역변수에도 바로 접근하지 못했을것 같고 람다식의 특징이 일반적인 함수의 특징과 너무 닮았다는 생각이 든다.   
<br>  
정확히 이러한 이유때문인지는 몰라도 아무튼 다른 지역변수에 접근할 수 없다. 인접한 지역변수에 접근하기 위해서는 특별한 방법이 필요한데 그 방법이 capture이다.   
<br>
capture를 할때 두가지 방법이 있다.   
1. capture by reference[&]
2. capture by value [=]

이름만 봐도 느낌이 온다. 그래서 설명은 하지 않고 두 방법을 어떻게 표현할지만 말해 보겠다.   
#### capture by reference
```cpp
#include<iostream>
#include<algorithm>

int main(){
    int a[5] = {1,2,3,4,5};
    int sum = 0;
    std::for_each(a,a+5, [&sum](int x){
        sum += x;
    });

    std::cout<<sum;
    return 0;
}
```
7번째 줄에 집중해보자. [&sum]   
sum 변수를 람다식에 capture by reference해 온다는 뜻이다.
sum 변수를 수정할 수 있다는 말이고, a배열을 모두 더한 15가 출력된다.   
<br>

#### capture by value   

```cpp
#include<iostream>
#include<algorithm>

int main(){
    int a[5] = {1, 2, 3, 4, 5};
    std::sort(a,a+5, [a](int x, int y) mutable {
        a[0] = 0;
        return x<y;
    });
    cout<<a[0];
    
    return 0;
}
```
6번째 줄의 람다식을 확인해보면 [a]라고 되어있다.(mutable은 잠깐 눈감기. 3번 챕터에서 설명)
a앞에 아무것도 없으면 a를 capture by value하겠다는 의미.   
그래서 a[0]에 0을 넣어도 10번째 줄 코드로 출력해보면 바뀌지 않고 다음과 같이 출력된다.
```
1 2 3 4 5
```

#### default값 설정
아주 많은 인접한 변수를 capture해오고 싶다면 일일히 손으로 적기는 너무 귀찮을 것이다. 그래서 한번에 인접한 변수를 capture하는 방법이 있다.   
<br>물론 value로 캡쳐할지 reference로 캡쳐할지 구분할 수 있다.   
```cpp
#include <iostream>
#include <algorithm>
using namespace std;

int main()
{
    int a,b,c =0,d=0;
    int array[5] = {1,2,3,4,5};
    sort(array,array+5, [&, c](int x, int y) mutable { //capture by reference를 default로
        a = 1; b = 2; c = 3; d = 4;
        return true;
    });
    cout<<a<<' '<<b<<' '<<c<<' '<<d<<' '<<endl;
    sort(array, array+5, [=, &c](int x, int y) mutable { //capture by value를 default로
        a = 0; b = 0; c = 3; d = 0;
        return true;
    });
    cout<<a<<' '<<b<<' '<<c<<' '<<d<<' ';
    return 0;
}
```

맨 앞에 변수명 없이 &를 쓰면 capture by reference =를 쓰면 capture by value를 뜻한다.   
<br>만약 &를 써넣고 특정한 변수는 capture by value를 하고 싶으면 어떻게 할까??????   
물론 이럴 때를 위한 표현도 있다. 위 코드에서 c 변수를 통해 설명하겠다.   
<br>람다 표현식의 capture clause를 잘 살펴보자.   
9번째 줄에서 reference를 디폴트로 캡쳐해올 때 c변수는 value로 캡쳐하고 싶다면 아무 부호 없이 c라고 적어주면 된다.   
반대로 14번째 줄에서 value를 디폴트로 캡쳐해올 때 c변수는 reference로 챕쳐하고 싶다면 &c라고 적어주면 된다.   
<br>이렇게 되면 첫번째 출력에서 다른 변수는 reference로 캡쳐했기 때문에 값이 바뀌지만 c만 그대로 0이 출력되고   
반대로 두번째 출력에서 다른 변수는 value로 캡쳐했기 때문에 값이 안바뀌는데 c만 3으로 바뀐다.
```
1 2 0 4
1 2 3 4
```

### 2. 매개변수
일반적인 함수의 매개변수와 같다.

### 3. mutable
mutable을 검색해보면 '변하기 쉬운'이라는 뜻.   
여기서는 함수 안에서 변수의 값을 바꿀수 있게 한다는 의미이다.   
<br>capture by value를 했을때 람다식 안에서 변수를 수정하고 가공하고싶으면 mutable을 붙여줘야 한다.   
물론 값을 복사해서 갖고들어온 것이기 때문에 밖에는 원본이 있고, 원본이 수정되지는 않는다. 그런데 왜 mutable을 붙일까?   
capture by value를 했을때 기본적으로 const가 붙어서 들어온다고 생각하면 된다. 그래서 변수를 수정하려고 하면 오류가 나는데 
이를 해결하려면 mutable이 필요하다.   
<br>
* 요점 : capture by value를 했을때 변수를 write 하고 싶으면 mutable을 붙여라.

### 4. 예외 throw
예외가 발생할 수 있다면 mutable 뒤에 throw()를 붙여준다.
```
[] () mutable throw() {
    ...
}

### 5. 리턴타입 지정
리턴타입이 void이거나 하나일때는 생략해도 된다. 컴파일러가 알아서 추론한다.   
<br><i>braced init list로부터는 추론을 할수가 없다고 하는데 머리가 너무 아프다. sort함수에서 쓰는 함수포인터를 알아보려고 했던게 어쩌다보니 람다식까지 왔는데 모르는게 또 생겨버렸다. 모르는걸 하나씩 알아가는줄 알았더니 정신차려보니까 모른다는 사실만 눈치채는 기분. </i> 

### 6. 함수 몸체
일반적 함수와 같다.

