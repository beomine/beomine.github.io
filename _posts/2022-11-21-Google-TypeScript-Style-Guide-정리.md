---
layout: post
title: Google TypeScript Style Guide 정리
author: sarah
date: 2022-11-21 20:55:00 +0900
categories: [Study, TypeScript]
tags: [TypeScript, Style, Guide, naming convention]
---

# Indentifiers
식별자는 모두 ASCII 포맷을 따라야하며, 영어,숫자,언더바(상수,테스트 메서드에 사용),`\`(정규식 표현 관련)로만 이루어져있다.    

|style|Category|
|------|---|
|UpperCamelCase|class / interface / type / enum / decorator / type parameters|
|lowerCamelCase|variable / parameter / function / method / property / module alias|
|CONSTANT_CASE|global constant values, including enum values.|

# Naming Styles
타입스크립트는 기본적으로 타입정보를 표현하기 때문에 이름에 타입에 대한 정보를 넣을 필요가 없다.

# Variables
항상 변수를 선언할 땐, const 혹은 let을 쓴다. Never use **var**

# Switch Statements
Switch 문은 default에 아무 코드가 없더라도 꼭 default를 포함해야한다.

참고 : [Google TypeScript Style Guide](https://google.github.io/styleguide/tsguide.html)