---
title: Commit의 원리
date: 2025-04-30 21:48:00 +0900
categories: [Git, Principal]
tags: [Git, 원리]
toc: true
---

커밋이란 작업한 내용을 저장하는 과정인데, 내부적으로는 파일 내용과 폴더 구조, 그리고 커밋 정보가 어떻게 저장되는지 `오브젝트(object)`라는 개념을 통해 이해할 수 있다.

## **커밋이란?**
**커밋(commit)**은 Git에서 작업한 파일들을 저장하는 행위다.
예를 들어, 새 파일 3개(f1, f2, f3)를 만든 후 git add로 스테이지에 올리고, git commit으로 저장하면 하나의 버전이 만들어진다.
커밋 메시지(예: "일")를 남겨서 어떤 작업인지 기록한다.

## **오브젝트(Object) 디렉토리와 커밋 저장 구조**
커밋한 정보는 Git 내부의 **objects(오브젝트) 폴더**에 저장된다.
이 오브젝트는 파일이나 커밋 정보를 담은 **`객체`**라고 생각하면 된다.
커밋 객체 안에는 
- 누가 커밋했는지
- 메시지
- 트리 정보 등이 포함되어 있다.

## **트리(Tree)와 파일 연결 관계**
**트리(Tree)**는 현재 커밋에 포함된 파일들의 이름과 내용(블랍)을 연결하는 역할을 한다.
쉽게 말해, 트리는 폴더 구조와 파일 목록을 저장하는 **목록표** 같은 것이다.
각 파일은 고유한 SHA-1 해시값으로 식별되며, 트리는 이 해시값을 통해 파일 내용을 가리킨다.

## **두 번째 커밋과 부모(Parent) 정보**
두 번째 커밋을 만들면, 이전 커밋을 가리키는 parent(부모) 정보가 추가된다.
이 parent 덕분에 Git은 커밋들이 시간 순서대로 연결된 **역사**를 추적할 수 있다.
두 커밋의 트리 값이 다르다는 것은 파일 내용이나 구조가 바뀌었다는 뜻이다.

## **스냅샷(Snapshot) 개념 이해하기**
커밋은 특정 시점의 프로젝트 상태를 '사진 찍듯' 저장하는 것과 같다.
이 스냅샷 덕분에 언제든지 과거 버전으로 돌아가거나 변경 내용을 비교할 수 있다.
트리와 블랍 정보가 합쳐져서 이 스냅샷을 만든다.

## **디렉토리 구조와 트리 정보 확장**
폴더(디렉토리)를 추가해도 Git은 트리 구조를 통해 폴더와 파일을 모두 관리한다.
예를 들어, `d1`이라는 폴더를 만들고 그 안에 파일을 넣으면, 트리는 이 폴더와 파일 정보를 모두 링크한다.
이렇게 폴더와 파일 상태를 모두 저장할 수 있어 복잡한 프로젝트도 관리가 가능하다.

## **오브젝트의 세 가지 종류**
Git 내부 오브젝트는 크게 3가지다:
- 블랍(Blob): 파일의 실제 내용 저장
- 트리(Tree): 파일명과 블랍을 연결하는 폴더 구조
- 커밋(Commit): 작업 기록과 트리, 부모 커밋 정보 저장

이 세 가지가 모여 Git의 버전 관리가 이루어진다.
