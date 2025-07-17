---
title: 차원 넘나들기
date: 2025-07-17 23:06:00 +0900
categories: [Git, 얄팍한 코딩사전]
tags: [Git]
toc: true
---

### **여러 브랜치(branch) 만들어보기**

#### **브랜치 생성 / 이동 / 삭제하기**

브랜치 목록 확인
```terminal
git branch
```

브랜치 이동
```terminal
git switch (브랜치명)
```
- `checkout` 명령어가 Git 2.23 버전부터 `switch`, `restore` 로 분리

💡 브랜치 생성과 동시에 이동하기
```terminal
git switch -c (새 브랜치명)
```
- 기존의 `checkout -b (새 브랜치명)`

🗑️ 브랜치 삭제하기
```terminal
git branch -d (브랜치명)
```

📌 다른 브랜치로 가져오지 않은 내용이 있는 브랜치를 삭제할 때에는 `-d` 대신 `-D` 명령어를 사용해야 한다.
```terminal
git branch -D (브랜치명)
```

✏️ 브랜치 이름 바꾸기
```terminal
git branch -m (기존 브랜치명) (새 브랜치명)
```

#### **각각의 브랜치에서 서로 다른 작업하기**

생성한 브랜치들에 대해 다른 작업들을 진행 후 각각의 브랜치에서 커밋을 진행한다.

`git log` : 명령어를 통해 위치한 브랜치에서의 내역만 볼 수 있다.
```terminal
git log -all --decorate --oneline --graph
```

위 명령어를 터미널에 사용해서 가시적으로 볼 수 있지만 GUI 툴을 사용하는 것이 훨씬 가시성이 좋다.

---
