---
title: cpp sort의 compare함수 사용법
category: [cpp]
tag: [cpp]
toc: true
toc_label: "목차"
toc_sticky: true
---
# STL인 sort함수 잘써먹기

---
sort함수의 인자는 총 3개이다. 두개는 정렬할 리스트의 시작과 끝 주소이고, 마지막은 함수 포인터다.   
![funcSort](/assets/cpp_sort/funcSort.png)   

앞에 두개 인자는 뻔하기 때문에 마지막 함수 포인터만 제대로 활용하면 된다.   

## compare함수
내가 원하는 순서로 정렬을 하고싶거나 int 배열이 아닌 범용적이지 않은 경우에 compare함수를 넘겨주면 sort를 원하는대로 커스텀할수 있다.   
> sort함수는 기본적으로 오름차순 정렬을 한다.

### 정렬 순서 정하기
```cpp
#include<iostream>
#include<algorithm>
using namespace std;
bool compare(int a, int b);
int main(){
    int a[5] = {1,2,3,4,5};
    sort(a,a+5, compare);
    for(int i=0; i<5; i++) cout<<a[i]<<' ';
    return 0;
}
bool compare(int a, int b){
    return a>b;
}
```
```
5 4 3 2 1
```

### 리스트 원소가 범용적이지 않은 경우
학생의 이름과 키를 저장하는 pair<string, int> 배열이 있다고 가정하자. 키순으로 학생을 정렬하고 싶다면 compare함수에서 정의해주면 된다.

```cpp
#include<iostream>
#include<algorithm>
#include<string>
using namespace std;
bool compare(pair<string, int> a, pair<string, int> b);
int main(){
    pair<string, int> student[3];
    student[0].first = "kim"; student[0].second = 170;
    student[1].first = "lee"; student[1].second = 175;
    student[2].first = "park"; student[2].second = 160;
    sort(student, student+3, compare);

    for(int i=0; i<3; i++){
        cout<<student[i].first<<" : "<<student[i].second<<endl;
    }
    return 0;
}
bool compare(pair<string, int> a, pair<string, int> b){
    return a.second < b.second;
}
```
```
park : 160
kim : 170 
lee : 175 
```

### 람다식을 이용하는 방법
람다식을 이용하면 굳이 새로 compare함수를 작성할 필요가 없다. 
계속 사용하는게 아니라면 람다식을 사용하면 편하다.   
[람다식 사용법](/cpp/what-is-lambda/)   
```cpp
#include<iostream>
#include<algorithm>
#include<string>
using namespace std;
int main(){
    pair<string, int> student[3];
    student[0].first = "kim"; student[0].second = 170;
    student[1].first = "lee"; student[1].second = 175;
    student[2].first = "park"; student[2].second = 160;
    sort(student, student+3, [](pair<string, int> a, pair<string,int> b) -> bool{
        return a.second < b.second;
    });

    for(int i=0; i<3; i++){
        cout<<student[i].first<<" : "<<student[i].second<<endl;
    }
    return 0;
}
```
결과도 물론 똑같이 나온다.
```
park : 160
kim : 170 
lee : 175 
```