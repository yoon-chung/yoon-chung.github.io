---
layout: post
title: GitHub 블로그 간단하게 만드는 법
categories:
  - GitHub
tags:
  - [github, blog]
toc: true
toc_sticky: true
date: 2022-10-07
---
학교에서 공부하는 내용들과 개인적으로 만들어본 코드들을 정리하고 기록해둘 공간으로, GitHub 블로그를 만들기로 했다. 
구글링을 토대로 여러 방법이 있다는 것을 알게 되었지만, 오랜 삽질(?)끝에 가장 쉽고 간단한 방법으로 개설해보았다.

## 1. GitHub 가입
- GitHub 접속 https://github.com -> Sign Up -> 이메일, 비번, username 입력 -> 계정 생성

<p align="center"><img src="/assets/images/1.png" width="500" height="300"></p>
     
- 가입 후 다음 주소로 접속 : github.com/{username} 
- 상단 두번째 메뉴 Repositories에 저장소를 만들어 블로그 페이지 및 코드를 업로드/관리 가능

## 2. Jekyll 테마 선정 후 복사
- 테마 구경 : http://jekyllthemes.org/
- **minimal mistakes** 테마 선정 -> 데모페이지 확인

<p align="center"><img src="/assets/images/mm.png" width="500" height="500"></p>

- 선택 테마의 Repository로 이동 : https://github.com/mmistakes/minimal-mistakes
- 우측 상단 'Fork'버튼 클릭

<p align="center"><img src="/assets/images/3.png" width="500" height="200"></p>

- 선택한 블로그 테마의 코드가 나의 Repositories로 복사됨

<p align="center"><img src="/assets/images/4.png" width="500" height="200"></p>

## 3. 블로그 주소 설정
- 선택 테마를 복사해온 나의 Repository에서 우측상단 Settings 클릭

<p align="center"><img src="/assets/images/5.png" width="400" height="150"></p>

- Repository name: **'blog'** -> 블로그 주소: **https://{username}.github.io/blog**
- Repository name: **'{username}.github.io'** -> 블로그 주소: **https://{username}.github.io**
- 나만의 URL과 Jekyll 테마가 반영된 기본 블로그 생성 및 publish 완료

## 4. 블로그 기본 설정 변경 (코드 수정)
- 나의 Repository 화면 상단 좌측 Code 탭 클릭 -> **\_config.yml** 파일 선택 (기본 세팅 변경)
- **\_data/navigation.yml** 파일 선택 (블로그 상단 헤더 부분 표시될 네비게이션 메뉴 설정)

- 아래 사이트 참고해서 layout 변경 (Site Settings, Site Author, Defaults, \_posts, Navigation 설정 등)
- https://junhobaik.github.io/jekyll-apply-theme/
- https://mmistakes.github.io/minimal-mistakes/docs/layouts/#custom-sidebar-content


## 5. 첫 포스팅 해보기
- \_posts 디렉토리에 파일을 작성하거나 마크다운 파일을 업로드
- \_posts 디렉토리가 생성되어있지않으면, Code탭 - Add file - Create new file 클릭
- 파일명에 **\_posts/YYYY-MM-DD-파일명.md** 입력
  
<p align="center"><img src="/assets/images/6.png" width="600" height="250"></p>

<p align="center"><img src="/assets/images/7.png" width="600" height="250"></p>

- 메타정보 (레이아웃, 제목, 카테고리, 태그, 날짜 등): 맨 위 --- 사이
- 실제 포스트 내용: --- 아래 부분

<p align="center"><img src="/assets/images/8.png" width="600" height="250"></p>

- 이미지 삽입/정렬, HTML코드 등 마크다운 문법 참고 사이트
- https://ansohxxn.github.io/blog/markdown/#inline-%EC%BD%94%EB%93%9C-%EB%B8%94%EB%A1%9D

(끝)
