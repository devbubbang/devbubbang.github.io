---
title: 2주차
date: 2025-05-20 23:11:00 +0900
categories: [기획공부, 초보 기획자 / PM을 위한 Test Case 작성 및 QA 노하우]
tags: [기획]
toc: true
---

수강 날짜 : 2025.05.20

## **Test Case 이해하기 & 작성하기**

### **애자일(Agile) 팀의 Test Case 작성 노하우**

애자일은 조직이나 팀마다 그 정의가 다르며, 단순히 Yes/No가 아닌 수준(Level)의 문제다. 기획자와 PM의 입장에서 애자일 방식으로 전환할 때 다음과 같은 변화가 있다:

- 스토리보드 대신 PRD와 User Story를 작성한다 (Waterfall에서 Agile로의 전환)
- 기획자/PM이 모든 디테일을 일일이 명시하지 않는다

> PRD? : Product Requirements Document = 제품 요구사항 정의서

#### **PRD의 주요 구성 요소**
- Target 고객
- Target 고객의 Pain Point
- 제품의 VP (Value Proposition = 가치 제안)
- 성공의 정의
- KPI (Key Performance Indicator, 구체적인 지표 = 핵심 성과 지표)
- User Story

유저스토리보다 더 세부적이거나 예외적인 사항에 대해 이슈가 발생할 때는 **팀이 함께 논의**하는 것이 중요하다.

#### **워터폴과 애자일의 프로세스 차이**

**워터폴 방식**
- 스토리보드 > 검토 > 기획 확정 > Test Case 작성

**애자일 방식**
- 문제 정의 >(tc작성) > 옵션 검토 > 옵션 확정 > (tc 작성) > 세부 사항 검토 > 세부 사항 확정

**주요 차이점**
- 기획자/PM이 모든 것을 작성할 필요가 없다
- 기획이 한 번에 완료된다는 생각과 부담이 없다
- Test Case 역시 한 번에 완료된다는 생각과 부담이 없다

### **Test Case의 한계**

"세상에 완벽한 툴, 프레임워크, 방법론은 없다."

Test Case의 주요 한계점은 다음과 같다:

1. 기획의 **핵심/주요한 부분에 대해서만** 고려하고, 기획하지 않은 부분에 대해서는 고려하지 않음
2. **복잡한** 것을 구조화하여 코드 상의 영향을 예측하기 어려움
3. 누락 없이 작성하려 해도 휴먼 에러(사람이 작업)가 발생할 수 있음

---

## **Test Case를 바탕으로 한 실전 QA 노하우**

### **프로젝트의 상황과 맥락을 고려한 QA 노하우**

프로젝트에는 외부 고객뿐만 아니라 내부 고객의 요청과 이해관계가 존재한다:

- 프로젝트의 목적, 과업 범위, 주요 사항 등에 대해 이해관계자들의 이해가 모두 다름
- 제품 출시 후 가장 먼저 피드백을 받는 이들은 운영, C/S 인력
- 제품과 직접 소통하는 마케팅, 사업개발 차원에서도 제품의 완성도에 관심이 있음

### **기획자/PM 이외에도 QA 참여 시키기**

제품과 프로젝트에 대한 이해를 바탕으로, 다양한 이해관계자들의 참여가 중요하다:

- 제품과 관련된 다양한 역할 이해
- 프로젝트의 목표와 과업 범위를 명확히 공지
- 이해관계자 간의 차이와 갈등 방지

QA 참여의 장점:
- 타 조직(이해관계자)의 입장 이해
- 최초 논의와 비교하여 변경 사항에 대한 인지
- 제품 기여도에 관여한다는 느낌 유도
- 책임 소재에 대한 분리가 없음의 인식을 유도

> 모두가 **함께 동일하게 이해하고, 함께 기여하는** 프로덕트를 만드는 것이 목표다.

### **QA 진행 전 체크리스트**

"제품 출시와 관련된 다양한 이해관계자가 참여하고 그 과정에서 소통이 명확하고 원활하게 하기 위한 QA를 만들기 위한 준비 사항"

1. Test Case를 최종 확인 및 정리
2. QA의 배경과 목적, 제품의 개요에 대해 설명
3. 이슈 발생 시 전달 방법 안내
4. QA서버와 실서버의 연동/분리 여부 확인
5. 시나리오에 맞는 계정 또는 환경 세팅

#### **중요 포인트**

1~3번: QA의 목적, 테스트 방법, 소통 방법에 대한 명확한 설명이 필요하다.

4번: QA서버(Dev)와 실서버(Master)의 연동 여부를 반드시 확인해야 한다.
> "QA를 위한 가상의 환경에서 진행하는 동작이 실서버에 반영될 수 있음을 아무리 안전하다고 하더라도 반드시 보장을 할 수 있도록 확인해야 한다."

5번: 다양한 케이스/시나리오를 재현할 수 있는 환경과 계정이 필요하다.
> 단독적인 유형으로는 충분한 검증이 어렵고, 소통의 어려움을 유발할 수 있다.