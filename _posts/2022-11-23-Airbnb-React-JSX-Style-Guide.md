---
layout: post
title: Airbnb React/JSX Style Guide
author: beomine
date: 2022-11-23 9:34:00 +0900
categories: [Guide, React]
tags: [style guide, react, jsx, airbnb]
---

## Naming
- Filename : use PascalCase for filenames  
  `ReservationCard.jsx`
- Reference Naming : 

  |Style|Category|
  |:------:|:---:|
  | PascalCase | React Components |
  | camelCase | their instances |

  ``` jsx 
    import ReservationCard from './ReservationCard'
    const reservationItem = <ReservationCard/> 
  ```
  
- Component Naming : 파일 이름과 동일하게 사용한다.
- Props Naming : DOM component prop name을 사용하는 걸 피해라

## Quotes
     
  |Style|Category|
  |:------:|:---:|
  |"double"| JSX attributes|
  |'single'| all other JS|
      
  ``` html 
   <Foo bar="bar"/>
   <Foo style={{ left: '20px' }}/>
  ```


## Props 
-  기본적으로 camelCase for prop names
-  the prop value가 React component일 경우 PascalCase 사용

## Tags
- 자식 컴포넌트가 없다면 항상 닫힘 태그 사용  
  
  ``` html
    <Foo className="stuff" />
  ```
- 컴포넌트가 다수의 속성을 가졌다면, 닫힘 태그는 다음 줄에 작성  
  
  ``` html
   <Foo 
     className="stuff"
     bar="bar"
     bar="baz"
   />
  ```


참고 : [Airbnb React/JSX Style Guide](https://github.com/apple77y/javascript/tree/master/react)