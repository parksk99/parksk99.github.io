---
title: github page 카테고리 만드는법
category: [Github_pages]
tag: [Github_pages]
---

# 카테고리 쉽게 만들기   

## 1. /_pages/ 안에 category-archive.md파일 생성

```
---
title: "categories"
layout: categories
permalink: /categories/
---
```

## 2. /categories/ 디렉토리 생성

***
위  permalink에 /categories/를 넣어줬기 때문에   
그에 해당하는 디렉토리를 만들어줘야됨   
그리고 그 안에 카테고리 이름으로 md 파일을 만든다.   
```
layout: category
title: Git
```
(/categories/Git.md 파일 내용)

이러면 Git이라는 이름의 카테고리가 만들어진거

## 3. /categories/index.html 파일 생성

***
categories를 누르면 보여줄 화면을 만들어줘아 하는데   
그게 바로 index.html
```
---
title: "categories"
layout: categories
peramlink: /categories/
---
```

검색해봤을때는 이 내용이 없어서 고생하다가   
이것저것 해보던중 어쩌다 정상작동이 됐다.

## 4. 접근 가능하게 만들기

***
이렇게 끝내면 메인 화면에서 카테고리를 누를수가 없다.   
따라서 접근 가능하게 만들어줘야 하는데   
페이지 테마에서 제공하는 기능을 이용했다.

/_data/navigation.yml/ 에 아래 내용을 추가하면   
페이지 최상단에 categories가 생김.   

```
main:
  - title: "Categories"
    url: /categories/
```
> 왜 생기는지는 모르겠음....   
> _config.yml에도 navigation에 대한 내용이 없는데   
> jekyll이 알아서 해주나보다

## 5. _config.yml 수정

***
```
category_archive:
  type: liquid
  path: /categories/
```
/_config.yml/ 에 위 내용이 없으면 추가해줌
liquid가 뭔지 궁금했는데   
liquid라는 언어가 있다고 함

## 6. 끝

***
카테고리 추가는 다 끝났고   
포스팅 할때 맨위 제목 쓸때 다음 코드를 추가해준다
```
category: [Github_pages]
```
그럼 진짜 끝ㅎㅎㅎㅎㅎㅎ

## 7. 결론

***
* 만들땐 어렵고 안되서 화났는데 막상 끝나고 보니 개쉬운 일이였음   
* 더 파고들면 더 원론적인(어려운) 방법도 있는데 아직 프론트에 관심이 없어서 더 알아보지는 않는걸로
* 카테고리를 추가하고 싶으면 /categories/ 안에 md파일만 추가하면 됨(2번 항목)
* 제공하는 기능 활용해서 빨리 끝내고 다른데 시간을 투자하면 되지
* tag 추가하기
* 참고한 문서: 너무 많아서 뭘 어디서 봤는지 까먹었다.
