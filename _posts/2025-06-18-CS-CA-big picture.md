---
title: "컴퓨터구조의 큰 그림"
date: 2025-06-18 22:00:00 +0900
categories: [CS 지식, Computer Architecture]
tags: [CS]
toc: true
mermaid: true
---

### **컴퓨터가 이해하는 두 가지 정보**

#### **1) 데이터**

- 숫자, 문자, 이미지, 동영상과 같은 정적인 정보
- 컴퓨터와 주고 받는 / 내부에 저장된 정보를 데이터라 통칭하기도 함
- 0과 1로 `숫자`를 표현하는 방법
- 0과 1로 `문자`를 표현하는 방법

#### **2) 명령어**

- 컴퓨터는 결국 명령어를 처리하는 기계
- 명령어
  - 컴퓨터를 실질적으로 움직이는 정보
  - 데이터는 명령어를 위한 일종의 재료

### **컴퓨터의 네 가지 핵심 부품**

<script src="https://cdn.jsdelivr.net/npm/mermaid/dist/mermaid.min.js"></script>
<script>
  mermaid.initialize({
    startOnLoad: true,
    theme: 'default'
  });
</script>

<div class="mermaid">
flowchart LR

A[컴퓨터 구조] -> B[컴퓨터가 이해하는 정보]
A -> C[컴퓨터의 네 가지 핵심 부품]

B -> D[데이터]
B -> E[명령어]

C -> F[CPU]
C -> G[메모리]
C -> H[보조기억장치]
C -> I[입출력장치]
</div>