# Authentication & Authorization Flow

## Overview

DevLinker는 로그인 성공 시 JWT를 발급하고, 보호 API 요청에서는 Bearer Token을 검증하여 사용자 인증 정보를 처리합니다.

JWT는 요청 사용자를 식별하기 위한 인증 수단이며, 실제 비즈니스 권한 검증은 Service 계층에서 도메인 규칙에 따라 처리합니다.

## Login & Token Issuance Flow

1. 사용자가 이메일과 비밀번호로 로그인 요청
2. `AuthService`에서 사용자 조회 및 비밀번호 검증
3. 인증 성공 시 Access Token과 Refresh Token 발급
4. 클라이언트는 이후 요청에 Access Token을 포함하여 API 요청

## Token Verification Flow

1. 클라이언트가 `Authorization` 헤더에 Bearer Token 전달
2. `JwtAuthenticationFilter`에서 토큰 추출
3. `JwtTokenProvider`에서 토큰 유효성 검증
4. 토큰에서 사용자 정보를 조회
5. `SecurityContextHolder`에 인증 정보 저장
6. Controller와 Service에서 로그인 사용자 기준으로 요청 처리

## Domain Authorization

DevLinker에서는 인증된 사용자 정보만 확인하는 것이 아니라, 모집글 작성자 여부와 팀 멤버 여부 같은 도메인 권한도 함께 검증합니다.

예를 들어 지원 승인 기능에서는 현재 로그인 사용자가 모집글 작성자인지 확인한 뒤 승인 처리를 진행합니다.

팀 공지, 일정, 문서, 회고 기능에서는 사용자가 해당 팀의 멤버인지 확인한 뒤 팀 협업 데이터에 접근하도록 처리합니다.

## Responsibility Separation

JWT 인증은 요청 사용자를 식별하는 역할을 하고, 실제 비즈니스 권한 검증은 Service 계층에서 도메인 규칙에 따라 처리합니다.

이를 통해 인증 처리와 도메인 권한 검증 책임을 분리했습니다.
