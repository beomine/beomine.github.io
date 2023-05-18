---
layout: post
title: Clean Code 의미 있는 이름
author: sarah
date: 2022-11-22 9:50:00 +0900
categories: [Guide, Clean Code]
tags: [Clean Code, Agile, software, Program, Programming, Programmer, robert c. martin]
---

# 의미 있는 이름
---
## 의도를 분명하게 밝혀라  
- 존재 이유? 수행 기능? 사용 방법? 세 가지 질문에 답할 수 있는 이름으로 지어라

## 그릇된 정보를 피해라  
- 실제 List가 아니라면 Group이라고 쓰거나 단순 복수형을 쓴다.
  > accountList X / accountGroup, bunchOfAccounts, Accounts O
- 서로 흡사한 이름을 사용하지 않도록 주의한다.
- 유사한 개념은 유사한 표기법을 사용한다.

## 의미 있게 구분하라 
- 불용어(noise word)를 사용하지 마라 
  > productInfo, productData
- 모든 지역 변수는 a, 모든 전역 변수는 the를 사용하는 게 아닌 이상 a, the를 쓰지마라

## 발음하기 쉬운 이름을 사용하라

## 검색하기 쉬운 이름을 사용하라
-  Constant 적극 활용  
ex) MAX_CLASSES_PER_STUDENT = 7 -> 7로는 검색하기 어렵지만 변수 이름으로 검색하면 용이하다.

* Single-responsibility Principle (SRP:단일 책임 원칙) : 클래스는 단 한 개의 책임을 가져야 한다.
* Open-Closed Principle (OCP:열림-닫힘 원칙) : 소프트웨어 엔티티(클래스, 모듈, 함수)는 확장을 위해 열려있고, 수정되어서는 안된다.