# Changelog

모든 주요 변경 사항이 이 파일에 기록됩니다.

## [0.2.0] - 2025-10-07

### 추가 (Added)
- **설정 시스템 강화**
  - `pydantic-settings` 기반 설정 관리
  - 환경 변수 검증 (로그 레벨, 환경, 포트 등)
  - 상세한 설정 필드 및 설명 추가
  - 캐싱, 성능, 보안 관련 설정 추가

- **로깅 시스템 개선**
  - JSON 및 텍스트 포맷 지원
  - 구조화된 로그 (타임스탬프, 레벨, 모듈, 함수 등)
  - 요청/응답 로깅 헬퍼 함수
  - 에러 로깅 개선

- **캐싱 시스템**
  - LRU 캐시 구현
  - TTL(Time To Live) 지원
  - 캐시 데코레이터 (`@cached`)
  - 캐시 통계 API

- **HTTP 클라이언트 유틸리티**
  - 타임아웃 설정
  - 자동 재시도 (exponential backoff)
  - AI API 전용 클라이언트

- **입력 검증 강화**
  - 쿼리, 약물, 증상, 나이, 음식 등 검증 함수
  - 입력 정제 (XSS 방지)
  - 의료 용어 정규화 및 동의어 매핑

- **에러 메시지 국제화**
  - 20개 이상의 검증 메시지 추가
  - 한국어, 일본어, 영어 지원

- **테스트 확대**
  - 입력 검증 테스트 (`test_validators.py`)
  - 캐싱 시스템 테스트 (`test_cache.py`)
  - 설정 검증 테스트 (`test_config.py`)

- **Docker 지원**
  - `Dockerfile` 추가
  - `docker-compose.yml` 추가
  - `.dockerignore` 추가
  - 헬스체크 설정

- **문서 보완**
  - API 사용 예시 문서 (`docs/API_EXAMPLES.md`)
  - 배포 가이드 (`docs/DEPLOYMENT.md`)
  - 상세한 환경 변수 설명 (`env.example`)
  - README 업데이트

### 변경 (Changed)
- **하드코딩 제거**
  - `main.py`에서 모든 하드코딩된 값을 설정으로 이동
  - API 버전, 앱 이름, 포트 등 설정 기반으로 변경

- **보안 강화**
  - CORS 설정 개선 (메서드 제한, preflight 캐시)
  - Rate limiting 기본값 조정 (120 → 60)
  - SECRET_KEY 자동 생성 (개발 환경)
  - 최대 요청 크기 제한 추가

- **성능 최적화**
  - 캐싱 활성화 (기본 1시간 TTL)
  - 요청 타임아웃 설정
  - 워커 프로세스 수 설정 가능

- **의존성 업데이트**
  - `tenacity` 추가 (재시도 로직)

### 수정 (Fixed)
- 로그 레벨 설정이 적용되지 않던 문제 수정
- API prefix 하드코딩 문제 수정

## [0.1.0] - 2025-10-06

### 추가 (Added)
- 초기 프로젝트 구조
- 의료 정보 검색 API
- 약물 상호작용 검사 API
- 건강 상태 분석 API
- 영양 성분 분석 API
- 다국어 지원 (7개 언어)
- 기본 테스트
- FastAPI 기반 REST API
- Swagger/ReDoc 문서화

---

## 버전 관리 규칙

이 프로젝트는 [Semantic Versioning](https://semver.org/)을 따릅니다:
- **MAJOR**: 호환되지 않는 API 변경
- **MINOR**: 하위 호환되는 기능 추가
- **PATCH**: 하위 호환되는 버그 수정
