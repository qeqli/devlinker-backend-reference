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

## Troubleshooting

### 1. 지원 승인 도메인 검증

지원 승인 기능은 단순히 Application 상태만 변경하는 기능이 아니라, 작성자 권한 검증, 지원 상태 검증, 팀 생성, 팀원 등록까지 이어지는 핵심 도메인 흐름이었습니다.

이를 해결하기 위해 `approveApplication()`에서 모집글 작성자 검증, 지원 상태 검증, Team 조회/생성, OWNER/MEMBER 등록, `application.approve()`를 순서대로 처리했습니다.

그 결과 승인 요청이 실제 팀 구성 흐름으로 이어지고, 이미 승인 또는 거절된 지원은 재처리되지 않도록 처리했습니다.

### 2. AWS 배포/접속 문제

EC2 인스턴스는 running 상태였지만 외부 브라우저에서 Swagger/API 접속이 되지 않는 문제가 있었습니다.

EC2 실행 상태와 Spring Boot 애플리케이션 실행 상태를 분리해서 확인하고, 8082 포트와 보안그룹 인바운드 규칙을 점검했습니다.

보안그룹 인바운드 규칙에 Spring Boot API 포트 8082를 허용한 뒤 Swagger/API 접근을 확인했습니다.

### 3. 모집글 목록 조회 성능 개선

모집글 데이터가 증가하면 전체 조회 범위와 DTO 변환 비용이 함께 커질 수 있었습니다.

로컬 MySQL에서 모집글 2,007건 기준으로 워밍업 후 동일 API를 10회 호출해 개선 전후 평균 응답시간을 비교했습니다.

전체 조회 방식에서 Pageable 기반 페이징 조회와 id DESC 최신순 정렬을 적용하여 필요한 범위만 조회하도록 개선했습니다.

평균 응답시간은 46.1ms에서 21.3ms로 감소했으며, 약 54% 개선을 확인했습니다.


