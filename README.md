\# DevLinker Backend



DevLinker는 개발자 팀 모집부터 지원 승인, 팀 생성, 협업 기능, 회고까지 이어지는 팀 프로젝트 플랫폼입니다.



\## Overview



핵심 서비스 흐름은 다음과 같습니다.



```text

모집글 작성 → 지원 신청 → 승인 / 거절 → 팀 생성 → 팀 협업 → 회고

```



본 레포지토리는 DevLinker의 백엔드 구현 내용을 중심으로 정리되어 있습니다.



\## Main Features



\- 회원가입 / 로그인

\- JWT 기반 인증 및 인가

\- 모집글 작성, 조회, 수정, 삭제

\- 모집글 검색 및 목록 조회

\- 지원 신청, 승인, 거절

\- 지원 승인 시 팀 생성 및 팀원 등록

\- 팀 공지, 일정, 문서, 회고 API

\- 공통 API 응답 포맷

\- 전역 예외 처리

\- 모집글 목록 조회 성능 개선

\- 지원 승인 서비스 로직 단위 테스트



\## Core Domain Flow



지원 승인 로직은 DevLinker의 핵심 도메인 흐름입니다.



```text

Application 승인

→ ApplicationStatus 변경

→ Team 생성

→ TeamMember 등록

→ 승인 결과 응답

```



승인 과정에서는 게시글 작성자 권한, 지원 상태, 팀원 중복 등록 여부를 검증합니다.



\## Tech Stack



\### Backend



\- Java 17

\- Spring Boot

\- Spring Security

\- Spring Data JPA

\- MySQL

\- Gradle



\### Test



\- JUnit

\- Mockito



\### Infra / Deployment Validation



\- AWS EC2

\- AWS RDS

\- AWS S3

\- AWS CloudFront

\- Swagger



\## Backend Responsibility



담당 범위는 백엔드 도메인 설계, API 구현, JWT 인증, 공통 응답/예외 처리, AWS 배포 검증, 성능 개선, 테스트 코드 작성입니다.



프론트엔드는 팀원이 담당했으며, 이 레포지토리는 백엔드 구현 내용을 중심으로 구성했습니다.



\## Performance Improvement



모집글 목록 조회 성능 개선을 진행했습니다.



\- 기존 조회 방식의 응답 시간 측정

\- 데이터 증가 상황에서 병목 구간 확인

\- 조회 쿼리 및 페이징 로직 개선

\- 개선 전후 응답 시간 비교



\## Test



지원 승인 서비스 로직에 대한 단위 테스트를 작성했습니다.



검증한 주요 시나리오는 다음과 같습니다.



\- 정상 승인 시 지원 상태 변경

\- 승인 시 팀 생성 및 팀원 등록

\- 게시글 작성자가 아닌 사용자의 승인 차단

\- 이미 처리된 지원의 재처리 차단



\## Environment Variables



실행 전 아래 환경변수 설정이 필요합니다.



| Name | Description |

| --- | --- |

| DB\_URL | MySQL 데이터베이스 접속 URL |

| DB\_USERNAME | 데이터베이스 사용자명 |

| DB\_PASSWORD | 데이터베이스 비밀번호 |

| JWT\_SECRET | JWT 서명 secret |

| JWT\_ACCESS\_TOKEN\_EXPIRATION | Access Token 만료 시간 |

| JWT\_REFRESH\_TOKEN\_EXPIRATION | Refresh Token 만료 시간 |

| SERVER\_PORT | 서버 실행 포트 |



\## Run



Windows:



```bash

gradlew.bat bootRun

```



Linux / macOS:



```bash

./gradlew bootRun

```



\## Test



Windows:



```bash

gradlew.bat test

```



Linux / macOS:



```bash

./gradlew test

```



