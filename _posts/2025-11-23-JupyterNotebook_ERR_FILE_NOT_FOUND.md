---
layout: single
title: Jupyter Notebook 실행 후 파일에 액세스할 수 없는 에러 해결하는 법
categories:
  - 문제 해결
tags:
  - [Jupyter Notebook, Python]
toc: true
toc_sticky: true
date: 2025-11-23
---

## 1. 문제상황
- Anaconda 설치 후 Jupyter Notebook 실행
- (관리자권한으로 실행)
- 빈화면이 뜨면서 
  **파일에 액세스할 수 없음.이동, 수정 또는 삭제되었을 수 있습니다.ERR_FILE_NOT_FOUND** 
  이 에러는 왜 뜨는걸까?

## 2. 해결방법
- C:\Users\사용자이름\AppData 경로로 들어가야하는데 윈도우에서 이 폴더를 숨김처리했기 때문
- **윈도우탐색기에서 숨김파일 및 폴더를 보이게 설정**
- 아무 폴더나 연 다음 상단-> 보기 탭 -> 맨아래 숨긴항목
- Windows10/11이라면      -> '...' 더보기 버튼 클릭 -> 옵션 -> 폴더옵션 -> 보기 탭-> 숨김 표


(끝)
